---
title: Correct width in all browsers (JS snippet)
date: 2012-11-04T17:24:53+00:00
authors:
- admin
layout: post

categories:
  - The Code
tags:
  - function
  - IE4
  - IE6
  - javascript
  - JS
---
This is a short JS snippet which is quite hard to find but it works like a charm.  
Basically with help of this JS snippet you get your width of the page correctly  
displayed in old IE6 and IE4 if you need to make it compatible with them or  
android/chrome/other browsers.

```
<div  class="fusion-fullwidth fullwidth-box hundred-percent-fullwidth"  style='background-color: #ffffff;background-position: center center;background-repeat: no-repeat;padding-top:0px;padding-right:0px;padding-bottom:0px;padding-left:0px;'>
  <div class="fusion-builder-row fusion-row ">
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: jscript; title: ; notranslate" title="">
function get_width() {
    var myWidth = 0;
    if(document.width) {
    //chrome
    myWidth = document.width;
    } else if( typeof( window.innerWidth ) == 'number' ) {
    //Non-IE
    myWidth = window.innerWidth;
    } else if( document.documentElement && ( document.documentElement.clientWidth) ) {
    //IE 6+ in 'standards compliant mode'
    myWidth = document.documentElement.clientWidth;
    } else if( document.body && ( document.body.clientWidth ) ) {
    //IE 4 compatible
    myWidth = document.body.clientWidth;
    }
    return myWidth;
 }
</pre>
        
        <p>
          And here is another JS snippet which does the same super compatibility except with height now width.
        </p>
        
        <div class="fusion-clearfix">
        </div>
      </div>
    </div>
    
    <div  class="fusion-layout-column fusion_builder_column fusion_builder_column_1_1  fusion-one-full fusion-column-first fusion-column-last fusion-column-no-min-height 1_1"  style='margin-top:0px;margin-bottom:0px;'>
      <div class="fusion-column-wrapper" style="background-position:left top;background-repeat:no-repeat;-webkit-background-size:cover;-moz-background-size:cover;-o-background-size:cover;background-size:cover;"  data-bg-url="">
        <pre class="brush: jscript; title: ; notranslate" title="">
function get_height() {
   var myHeight = 0;
   if(document.height) {
   //chrome
   myHeight = document.height;
   } else if( typeof( window.innerHeight ) == 'number' ) {
   //Non-IE
   myHeight = window.innerHeight;
   } else if(document.documentElement && (document.documentElement.clientHeight)) {
   //IE 6+ in 'standards compliant mode'
   myHeight = document.documentElement.clientHeight;
   } else if(document.body && (document.body.clientHeight)) {
   //IE 4 compatible
   myHeight = document.body.clientHeight;
   }
   return myHeight;
}
</pre>
        
<p>
  This JS snippets can be quite hard to find so hopefully this helps people who are searching for something like this.
  
  <div class="fusion-clearfix">
  </div></div> </div></div></div>
```