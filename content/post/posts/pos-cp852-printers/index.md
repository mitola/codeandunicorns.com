---
title: POS CP852 printers

date: "2012-10-31T20:12:09+00:00"

# Link this post with a project
projects: []

# Is this an unpublished draft?
draft: false
# Show this page in the Featured widget?
featured: false
authors:
- admin

categories:
  - The Code
tags:
  - base64_encode
  - CP852 printer
  - encoding
  - HTML
  - PHP
  - POS
  - receipt
  - snippet
---
For programmers in web development and problems with POS CP852 encoding. Have you ever worked on a project with ancient POS receipt printers that use CP852 encoding, which have no suitable drivers and probably several other things that make finding a solution hard for you? We had so i am sharing the code which will hopeffuly be useful to someone who will be having similar problems in the future. If you need extra information feel free to contact via contact form.

For this solution you are going to need jquery 1.8.1. a bit of html and a bit of php. more details about printers with POS CP852 bellow :

### Code snippet for POS CP852 (encoding) printers:

```
<div  class="fusion-fullwidth fullwidth-box hundred-percent-fullwidth"  style='background-color: #ffffff;background-position: center center;background-repeat: no-repeat;padding-top:0px;padding-right:0px;padding-bottom:0px;padding-left:0px;'>
  <div class="fusion-builder-row fusion-row ">
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: xml; title: ; notranslate" title="">

<!DOCTYPE html>
<html>
<head>
<title></title>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<script src="jquery-1.8.1.min.js"></script>
<script>
$(document).ready(function () {
$("#do").click(function(){
window.open("data.php");
});
});
</script>
</head>
<body>
<div id="data"></div>
<a href="#" id="do">Click</a>
</body>
</html>
</pre>
        
        <p>
          Everything this code does is that it takes data from the echo call of the php file, and calls an external application with which to open the file(use autoit script to automatically send a file to spool of the printer and you should be ok 🙂 ). this code will work in most major web browsers but not in Firefox, the code for Firefox is written bellow.
        </p>
        
        <div class="fusion-clearfix">
        </div>
      </div>
    </div>
    
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: php; title: ; notranslate" title="">
header('Content-Type: application/octet-stream');
header("Content-Disposition: attachment; filename=\"to_print\"");

$something = "This is good extended ASCII test čćšžđ čćžđš.";
$something = iconv('UTF-8', 'CP852', $something);

echo hexstr("1b1d7405") . $something . hexstr("0d0a0d0a");

function hexstr($hexstr) {
 $hexstr = str_replace(' ', '', $hexstr);
 $hexstr = str_replace('\x', '', $hexstr);
 $retstr = pack('H*', $hexstr);
 return $retstr;
}
function strhex($string) {
 $hexstr = unpack('H*', $string);
 return array_shift($hexstr);
}
</pre>
        
        <p>
          This is the code for the most major browsers bundled together with previously written HTML/jquery code and should work ok. But take on notice for iconv command you need the corresponding .dll file to work on a server, but if you are using Xammp it should work because it should have the .dll included already. Not to repeat myself, using the bottom PHP code you will need the same .dll file.
        </p>
        
        <div class="fusion-clearfix">
        </div>
      </div>
    </div>
    
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: xml; title: ; notranslate" title=""></pre>
<!DOCTYPE html>
<html>
 <head>
 <title></title>
 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
 <script src="jquery-1.8.1.min.js"></script>
 <script>
 $(document).ready(function () {
 $("#do1").click(function(){
 window.open("data.php");
 });

$("#do2").click(function(){
$.ajax({
url : "data.php",
success : function (data) {
window.open("data:application/print;charset=cp852; base64," + data);
},
error : function () {
alert("error");
}
});
});
});
</script>
</head>
<body>
<div id="data"></div>
<a href="#" id="do1">Click</a>
<a href="#" id="do2">Click2</a>
<br />
<br />
<a href="data.php">print</a>
</body>
</html>
<pre></pre>
        
        <p>
          This code will work the same as the previous one you will still need an external app or settings to print it directly to spool without using the confirmation for printing if you do a trick or two which should help with it :). But this code is only meant for Firefox, the following PHP code is meant just for Firefox too due to certain limitations I encountered.
        </p>
        
        <div class="fusion-clearfix">
        </div>
      </div>
    </div>
    
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: php; title: ; notranslate" title=""></pre>
header('Content-Type: text/html');
header("Content-Disposition: attachment; filename=\"to_print.print\""); //if you want unique name you can comment or change the filename pars.

$something = "testing cp852 encoding ščćžčćžšđčćžšđ";

$something = iconv('UTF-8', 'CP852', $something);//php_iconv _ dll need extension file to work on a server

echo base64_encode(hexstr("1b1d7405") . $something . hexstr("0d0a0d0a"));//this are hex commands , first hex command is for start of the choosen printer with a manual coding seting and //last one  "0d0a0d0a" is meant for \n in translation to printer command

function hexstr($hexstr) {
 $hexstr = str_replace(' ', '', $hexstr);
 $hexstr = str_replace('\x', '', $hexstr);
 $retstr = pack('H*', $hexstr);
 return $retstr;
}
<pre></pre>
        
<p>
  I hope this code helps anyone who is having similar problems. Cheers!
  
  <div class="fusion-clearfix">
  </div></div> </div></div></div>
```