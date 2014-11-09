---
layout: doc
title: Example of image file upload
date: April 25, 2014
---

## Image File Upload Example

Let's implement a simple file upload.

full source can be found in [here](https://github.com/xpush/lib-xpush-web/blob/master/example/upload.html).

Since XPUSH servers used in the demo below is a temporary test servers provided by XPUSH team, we can't guarantee performance and iIt may be temporarily unavailable. Therefore, please use the XPUSH you install it yourself.

Include js files : jquery, socket.io-stream.js, xpush.js

### script import

<pre data-lang="html">
<code class="prettyprint">&lt;script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"&gt;&lt;/script&gt;

&lt;!-- xpush --&gt;
&lt;script src="http://xpush.github.io/lib/dist/xpush.js"&gt;&lt;/script&gt;

&lt;script type="text/javascript" src="http://xpush.github.io/lib/dist/socket.io-stream.js"&gt;&lt;/script&gt;
</code>
</pre>

### script code

When screen loading is complete, create a channel02.

<pre data-lang="js">
<code class="prettyprint">// Create new xpush
var xpush = new XPush('http://demo.stalk.io:8000', 'demo');

$(document).ready( function(){
  // create a channel02.
  xpush.createSimpleChannel('channel02', function(){
    // show the success message after generating
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#danger" ).hide()
    $( "#success" ).html(html);
    $( "#success" ).show();

  });
});
</code>
</pre>

Generate the upload function. The upload function to upload a file using the function of uploadStream xpush.

The first argument of the uploadStream function is channel id.

The second argument of the uploadStream function is options. The json object containing the input Document Element of the file type. If you set the type to `image`, it will generate a thumbnail image.

	{ file: fileObj, type : 'image' }

The third argument of the uploadStream function is the function to show progressing status.

The forth argument of the uploadStream function is callback function will be occured when upload is finished. if will deliver the results as a JSON format. name is the unique key of the uploaded images, tname is a thumbnail.

	{result : {channel: "channel02", name: "Zk__N6hqm.jpg", tname: "T_Zk__N6hqm.jpg"}}


getFileUrl function create a download URL using the name or tname

<pre data-lang="js">
<code class="prettyprint">var upload = function(){

  // Gets the File Document Element object.
  var inputObj = document.getElementById("uploadFile");

  // Gets the progressbar.
  var progressbar = $("#progress_bar");
  var progressdiv = $("#progress_div");


 // Add the Validation.
  if( inputObj.value === '' ){
    var html =  '<strong>Upload Failed!</strong> Select file.';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    return;
  }

  // check file type whether file is the image type.
  var file = inputObj.files[0];
  if( file.type.indexOf( "image" ) < 0 ){

    var html =  '<strong>Upload Failed!</strong> Select image only';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    inputObj.value = "";
    return;
  }

  // Use uploadStream function of xpush to upload the file.
  xpush.uploadStream( 'channel02', {
    file: inputObj, type : 'image'
  }, function(data, idx){
    inputObj.value = "";

    // show the progress information as progressbar
    var progress = data+"%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.show();
  }, function(data,idx){

    inputObj.value = "";

    // Obtains the URL that you can download a thumbnail image
    var thumbnailUrl = xpush.getFileUrl('channel02', data.result.tname );
    // Obtains the URL that you can download a original image
    var imageUrl = xpush.getFileUrl('channel02', data.result.name );

    var progress = "0%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.hide();

    //Shows the results of the upload to screen.
    var html =  '<strong>Upload Success!</strong> ' + imageUrl;
    $( "#danger" ).hide();
    $( "#success" ).html(html);
    $( "#success" ).show();

    // Copy the template object to show a thumbnail image 
    var newImage = $( "#template" ).clone();

    // modify the newly created object.
    newImage.attr( "id", "template_"+ Date.now() );
    newImage.attr( "src", thumbnailUrl );
    newImage.bind( "click", function(){
      var popup = window.open(imageUrl, '_blank', 'location=no');
    });

    // Add the newly created Document Element object in the specified area.
    newImage.appendTo( "#list" );
    newImage.show();
  });
};
</code>
</pre>

### html code

<pre data-lang="html">
<code class="prettyprint">
&lt;div class="row" style="margin-top:20px;"&gt;
  &lt;div class="col-sm-12"&gt;
    &lt;div class="jumbotron"&gt;
      &lt;h1&gt;File Upload Example&lt;/h1&gt;
      &lt;p class="lead"&gt;Upload image file with simple channel&lt;/p&gt;
      &lt;p&gt;&lt;a href="https://github.com/xpush/lib-xpush-web/blob/master/example/upload.html" target="_blank" class="btn btn-primary btn-lg" role="button"&gt;View source from github&lt;/a&gt;&lt;/p&gt;
    &lt;/div&gt;
    &lt;div id="success" class="alert alert-success" role="alert" style="display:none"&gt;&lt;/div&gt;
    &lt;div id="danger" class="alert alert-danger" role="alert" style="display:none"&gt;&lt;/div&gt;

    &lt;div style="display:flex;"&gt;
      &lt;input class="form-control" name="uploadFile" id="uploadFile" type="file" /&gt;
      &lt;button type="submit" id="form-button" class="btn btn-primary" style="margin-left:10px;" onclick="upload();"&gt;
        Upload
      &lt;/button&gt;
    &lt;/div&gt;
    &lt;span class="help-block"&gt;Select image to upload. uploaded images will be displayed in under area&lt;/span&gt;

    &lt;div class="row"&gt;
      &lt;div class="col-sm-8"&gt;
        &lt;div id="progress_div" class="progress" style="width:200px; display:none;"&gt;
          &lt;div id="progress_bar" class="progress-bar" style="width: 0%;"&gt;&lt;/div&gt;
        &lt;/div&gt;

        &lt;h2&gt;Uploaded images : click image to enlarge&lt;/h2&gt;
        &lt;div style="display:flex;" id="list"&gt;
        &lt;/div&gt;
        &lt;img id="template" class="img-thumbnail" style="display:none"&gt;
      &lt;/div&gt;
    &lt;/div&gt;
  &lt;/div&gt;
&lt;/div&gt;
</code>
</pre>


### Code Result

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>

<!-- xpush -->
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>

<script type="text/javascript" src="http://xpush.github.io/lib/dist/socket.io-stream.js"></script>

<style>
  .img-thumbnail {
    margin: 10px;
  }
</style>

<script type="text/javascript">
// Create new xpush
var xpush = new XPush('http://demo.stalk.io:8000', 'demo');

$(document).ready( function(){
  // create a channel02.
  xpush.createSimpleChannel('channel02', function(){
    // show the success message after generating
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#danger" ).hide()
    $( "#success" ).html(html);
    $( "#success" ).show();

  });
});


var upload = function(){
  // Gets the File Document Element object.
  var inputObj = document.getElementById("uploadFile");

  // Gets the progressbar.
  var progressbar = $("#progress_bar");
  var progressdiv = $("#progress_div");

  // Add the Validation. 
  if( inputObj.value === '' ){
    var html =  '<strong>Upload Failed!</strong> Select file.';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    return;
  }

  // check file type whether file is the image type.
  var file = inputObj.files[0];
  if( file.type.indexOf( "image" ) < 0 ){

    var html =  '<strong>Upload Failed!</strong> Select image only';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    inputObj.value = "";
    return;
  }

  // Use uploadStream function of xpush to upload the file.
  xpush.uploadStream( 'channel02', {
    file: inputObj, type : 'image'
  }, function(data, idx){
    inputObj.value = "";

    // show the progress information as progressbar
    var progress = data+"%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.show();
  }, function(data,idx){

    inputObj.value = "";

    // Obtains the URL that you can download a thumbnail image
    var thumbnailUrl = xpush.getFileUrl('channel02', data.result.tname );
    // Obtains the URL that you can download a original image
    var imageUrl = xpush.getFileUrl('channel02', data.result.name );

    var progress = "0%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.hide();

    // Shows the results of the upload to screen
    var html =  '<strong>Upload Success!</strong> ' + imageUrl;
    $( "#danger" ).hide();
    $( "#success" ).html(html);
    $( "#success" ).show();

    // Copy the template object to show a thumbnail image 
    var newImage = $( "#template" ).clone();

    // modify the newly created object.
    newImage.attr( "id", "template_"+ Date.now() );
    newImage.attr( "src", thumbnailUrl );
    newImage.bind( "click", function(){
      var popup = window.open(imageUrl, '_blank', 'location=no');
    });

    // Add the newly created Document Element object in the specified area.
    newImage.appendTo( "#list" );
    newImage.show();
  });
};
</script>

<div class="row" style="margin-top:20px;">
  <div class="col-sm-12">
    <div class="jumbotron">
      <h1>File Upload Example</h1>
      <p class="lead">Upload image file with simple channel</p>
      <p><a href="https://github.com/xpush/lib-xpush-web/blob/master/example/upload.html" target="_blank" class="btn btn-primary btn-lg" role="button">View source from github</a></p>
    </div>
    <div id="success" class="alert alert-success" role="alert" style="display:none"></div>
    <div id="danger" class="alert alert-danger" role="alert" style="display:none"></div>

    <div style="display:flex;">
      <input class="form-control" name="uploadFile" id="uploadFile" type="file" />
      <button type="submit" id="form-button" class="btn btn-primary" style="margin-left:10px;" onclick="upload();">
        Upload
      </button>
    </div>
    <span class="help-block">Select image to upload. uploaded images will be displayed in under area</span>

    <div class="row">
      <div class="col-sm-8">
        <div id="progress_div" class="progress" style="width:200px; display:none;">
          <div id="progress_bar" class="progress-bar" style="width: 0%;"></div>
        </div>

        <h2>Uploaded images : click image to enlarge</h2>
        <div style="display:flex;" id="list">
        </div>
        <img id="template" class="img-thumbnail" style="display:none">
      </div>
    </div>
  </div>
</div>

<script type="text/javascript">
	prettyPrint();
</script>