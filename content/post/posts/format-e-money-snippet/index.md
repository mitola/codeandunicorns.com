---
title: Format € money snippet
date: 2012-11-05T13:01:04+00:00
authors:
- admin
layout: post
categories:
  - The Code
tags:
  - format
  - function
  - javascript
  - JS
  - method
  - snippet
---
The following function transforms the inputed numeric value for example  
var sum = 50.3  
sum.formatMoney(2,&#8217;,&#8217; , &#8216;.&#8217;) + &#8220;€&#8221;  
and the output of this function is then 50,30€. As you notice it is quite simple to change it in $ or anything you desire.
```
<pre class="brush: jscript; title: ; notranslate" title="">Number.prototype.formatMoney = function(c, d, t){
    var n = this, c = isNaN(c = Math.abs(c)) ? 2 : c, d = d == undefined ? "," : d, t = t == undefined ? "." : t, s = n &lt; 0 ? "-" : "", i = parseInt(n = Math.abs(+n || 0).toFixed(c)) + "", j = (j = i.length) &gt; 3 ? j % 3 : 0;
    return s + (j ? i.substr(0, j) + t : "") + i.substr(j).replace(/(\d{3})(?=\d)/g, "$1" + t) + (c ? d + Math.abs(n - i).toFixed(c).slice(2) : "");
};
</pre>
```