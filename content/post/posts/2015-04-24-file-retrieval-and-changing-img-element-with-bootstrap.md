---
title: File retrieval and changing img element with Bootstrap
date: 2015-04-24T09:40:05+00:00
authors:
- admin
layout: post
categories:
  - programming
---
Sharing a smaller code snippet / mini tutorial / example since I have found it very useful in connection to HTML preview display of cam image or an uploaded image, depending on what you want really 🙂  
So, the example is using Twitters Bootstrap, jQuery and beloved base64. I know the last one is just an encoding, but I like it nonetheless!

In the following code a simple example is provided with an image which will be replaced later with uploaded image without reloading.

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>demo canvas example</title>

<!-- Bootstrap Definitions -->
<link href="css/bootstrap.min.css" rel="stylesheet">
<!-- Choosen font -->
<link href='http://fonts.googleapis.com/css?family=Francois+One' rel='stylesheet' type='text/css'>

</head>
<body>

<div class="row text-center" id="pass" style="padding-bottom: 20px;">
<div class="col-sm-10"><h2>Image placeholder</h2></div>
<div class="col-sm-12 ">
<img id="imagePreview" height="220" width="355" src="chosenImage.jpg"
class="img-responsive center-block" alt="">
<!-- regarding btn-file class check at the bottom for a neat solution of making design same as for other Bootstrap buttons -->
<span class="btn btn-primary btn-file">
<!-- File upload button and file chooser -->
Upload <input type="file" id="fileuploadform">
</span>
</div>

</div>
<!-- Scripts loading -->
<!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.2/jquery.min.js"></script>
<!-- Include all compiled plugins (below), or include individual files as needed -->
<script src="js/bootstrap.min.js"></script>

<!-- This script can be seen in the next code section -->
<script>
.... JS script part
</script>
</body>
</html>
```
        
Javascript nicely separated 🙂

```
var trimmedReaderResult;

//If upload form is used, the following function will get triggered
$("#fileuploadform").on('change', function () {
previewAndSendDoc(1);
})

//following the workflow
function previewAndSendDoc(whichDiv) {
if (whichDiv === 1) {
var uploadformid = 'fileuploadform';
//Getting image named imagePreview, this <img> element will be used for manipulation
var previewImg = document.getElementById('imagePreview');
}
//getting the file from the file upload button
var uploadform = document.getElementById(uploadformid).files[0];
//Initializing FileReader
var reader = new FileReader();
```
        

In this function reader function callback will be called after it finishes the file and replace image of <img alt="" /> previewImage element newly received file. After that in this case the base64 of the image will be taken and truncated until only binary data without prefix remains for further manipulation


```
reader.onloadend = function () {
previewImg.src = reader.result;
trimmedReaderResult = reader.result.substring(reader.result.indexOf(',')+1);
//getting pure base64 of image
}
```
        
Function which will go to the above function after readAsDataUrl is finished
```
if (uploadform) {
  reader.readAsDataURL(uploadform);
} else {
  previewImg.src = "";
}
//sendingDocument();
}
```
        
And addition for colouring button for a file upload to have the same design as other Boostrap buttons:

```
.btn-file {
    position: relative;
    overflow: hidden;
}
.btn-file input[type=file] {
    position: absolute;
    top: 0;
    right: 0;
    min-width: 100%;
    min-height: 100%;
    font-size: 100px;
    text-align: right;
    filter: alpha(opacity=0);
    opacity: 0;
    outline: none;
    background: white;
    cursor: inherit;
    display: block;
}
```
