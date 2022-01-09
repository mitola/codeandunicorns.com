---
title: 3 Data validation methods in javascript

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
  - function
  - javascript
  - method
---
Function is isValideDate can be used for example on a string data.isValideDate() to confirm that date is in correct format like these 2 for example:

  * 04-11-2012
  * 4-11-12

The isNumeric function is used same way with data.isNumeric() on a string and the function is used validation if the whole input is numeric

```
<pre class="brush: jscript; title: ; notranslate" title="">String.prototype.isValidDate = function()
{
    var IsoDateRe = new RegExp("^([0-9]{2})-([0-9]{2})-([0-9]{4})$");
    var matches = IsoDateRe.exec(this);
    if (!matches) return false;
    var composedDate = new Date(matches[3], (matches[2] - 1), matches[1]);
    return ((composedDate.getMonth() == (matches[2] - 1)) &&
        (composedDate.getDate() == matches[1]) &&
        (composedDate.getFullYear() == matches[3]));
}

String.prototype.isNumeric = function () {
    var intRegex = /^\d+$/;

    var v = this;

    if(intRegex.test(v)) {
        return true;
    }
    return false;
}
</pre>
```

And just for you, a third function for strings. This function data.isDouble() checks if the inputed data is of a double type.

```
<pre class="brush: jscript; title: ; notranslate" title="">String.prototype.isDouble = function () {
    var validChars = '0123456789.';

    for(var i = 0; i &lt; this.length; i++) {
        if(validChars.indexOf(this.charAt(i)) == -1)
            return false;
    }
    var dblVar = parseFloat(this);

    if(isNaN(dblVar))
        return false;
    return true;;
}
</pre>
```