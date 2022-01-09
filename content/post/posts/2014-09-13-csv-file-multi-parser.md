---
title: Simple .csv file parser
date: 2014-09-13T19:26:12+00:00

authors:
- admin
    
categories:
  - ALL
tags:
  - csv
  - parser
  - PHP
---
In the following short snippet is an example of using PHP for simple parsing of .csv file / files comparing them and outputting the results in a third file. In this case itself it is not appropriate for use on live websites/apps since it is really inefficient, but I liked the simplicity and quick adaptability as a basis 🙂 The snippet uses only functions and libs from PHP itself (5.2+)
```
<div  class="fusion-fullwidth fullwidth-box hundred-percent-fullwidth"  style='background-color: #ffffff;background-position: center center;background-repeat: no-repeat;padding-top:0px;padding-right:0px;padding-bottom:0px;padding-left:0px;'>
  <div class="fusion-builder-row fusion-row ">
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: php; title: ; notranslate" title="">

<?php

$all_acc_file = fopen('big_original_file.csv', 'r'); // test matchingdatacheck.csv
$result_file = fopen("result.csv","w");

$i = 0;
$result_found=0;
while (($line = fgetcsv($all_acc_file)) !== FALSE) {
  //$line is an array of the csv elements
  //print_r($line);
  $file_missmatch = fopen('comparison_table.csv', 'r');
  while (($linemiss = fgetcsv($file_missmatch)) !== FALSE) {

  //matching in this example twelth or seventh entry beetwen a line in big_original_file.csv and a line in comparison_table.csv
  if($line[11] == $linemiss[11] || $line[6] == $linemiss[6]){
    $result_arr = array($linemiss[2] , $linemiss[3] , $line[2] , $line[3],$line[11],$line[6]);
    $result_found=1;
   }
 }

 if($result_found==1){
  fputcsv($result_file, $result_arr);
 }

 fclose($file_missmatch);
 $i++; echo"$i\n";
 $result_found=0;
}

fclose($all_acc_file);
fclose($result_file);</pre>
</pre>
        
        <div class="fusion-clearfix">
        </div>
      </div>
    </div>
  </div>
</div>
```