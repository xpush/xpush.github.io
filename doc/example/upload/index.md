---
layout: doc
title: Example of image file upload
date: April 25, 2014
---

## Image File Upload Example

간단한 파일업로드 기능을 구현해 보겠습니다.

full source는 [여기](https://github.com/xpush/lib-xpush-web/blob/master/example/upload.html)에서 확인할 수 있습니다.

아래 데모에서 사용한 XPUSH 서버는 XPUSH 개발팀에서 제공하는 임시 테스트용 서버이므로, 일시적으로 동작하지 않거나 성능을 보장할 수 없습니다. 그러므로 여러분이 직접 설치하신 XPUSH 를 사용하시기 바랍니다.

ui를 위한 jquery 와 xpush.js, 그리고 파일 업로드를 위한 socket.io-stream.js를 include 합니다.

### script import

<pre data-lang="html">
<code class="prettyprint">&lt;script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"&gt;&lt;/script&gt;

&lt;!-- xpush --&gt;
&lt;script src="http://xpush.github.io/lib/dist/xpush.js"&gt;&lt;/script&gt;

&lt;script type="text/javascript" src="http://xpush.github.io/lib/dist/socket.io-stream.js"&gt;&lt;/script&gt;
</code>
</pre>

### script code

화면 로딩이 완료되면 channel02를 생성합니다.

<pre data-lang="js">
<code class="prettyprint">// Create new xpush
var xpush = new XPush('http://demo.stalk.io:8000', 'demo');

$(document).ready( function(){
  // channel02 을 생성합니다.
  xpush.createSimpleChannel('channel02', function(){
    // 생성 후에 success 메시지를 보여줍니다.
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#danger" ).hide()
    $( "#success" ).html(html);
    $( "#success" ).show();

  });
});
</code>
</pre>

upload 함수를 생성합니다. upload함수는 xpush의 uploadStream 함수를 사용하여 파일을 업로드합니다.

uploadStream 의 첫번째 인자는 channel id 입니다.

uploadStream 의 두번째 인자는 업로드 option입니다. file type의 input Document Element 객체를 포함한 json입니다. type을 `image`로 설정할 경우 thumbnail 이미지를 생성해줍니다.

	{ file: fileObj, type : 'image' }

uploadStream 의 세번째 인자는 progress status을 알려주는 function입니다. 지정된 함수의 첫번째 인자에 status 값을 %형태로 전달해줍니다.

uploadStream 의 네번째 인자는 업로드 완료 후에 수행되는 callback function입니다. 지정된 함수의 첫번째 인자에 업로드한 결과를 JSON 형태로 전달해줍니다. name은 업로드한 이미지의 유니크한 키이고, tname은 thumbnail입니다.

	{result : {channel: "channel02", name: "Zk__N6hqm.jpg", tname: "T_Zk__N6hqm.jpg"}}


getFileUrl 함수는 uploadStream로 받은 name이나 tname 을 이용하여 다운로드 받을 수 있는 URL을 생성해줍니다.

<pre data-lang="js">
<code class="prettyprint">var upload = function(){

  // File Document Element 객체를 가져옵니다.
  var inputObj = document.getElementById("uploadFile");

  // progressbar 를 가져옵니다.
  var progressbar = $("#progress_bar");
  var progressdiv = $("#progress_div");


 // Validation을 추가합니다.
  if( inputObj.value === '' ){
    var html =  '<strong>Upload Failed!</strong> Select file.';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    return;
  }

  // file의 type이 image type인지 체크합니다.
  var file = inputObj.files[0];
  if( file.type.indexOf( "image" ) < 0 ){

    var html =  '<strong>Upload Failed!</strong> Select image only';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    inputObj.value = "";
    return;
  }

  // xpush의 uploadStream 함수를 사용하여 파일을 업로드합니다.
  xpush.uploadStream( 'channel02', {
    file: inputObj, type : 'image'
  }, function(data, idx){
    inputObj.value = "";

    // progress 정보를 받아 bar 형태로 보여줍니다.
    var progress = data+"%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.show();
  }, function(data,idx){

    inputObj.value = "";

    // thumbnail image를 다운로드 받을 수 있는 URL을 얻어 옵니다.
    var thumbnailUrl = xpush.getFileUrl('channel02', data.result.tname );
    // original image를 다운로드 받을 수 있는 URL을 얻어 옵니다.
    var imageUrl = xpush.getFileUrl('channel02', data.result.name );

    var progress = "0%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.hide();

    // 업로드결과를 화면에 보여줍니다.
    var html =  '<strong>Upload Success!</strong> ' + imageUrl;
    $( "#danger" ).hide();
    $( "#success" ).html(html);
    $( "#success" ).show();

    // thumbnail image를 보여주기 위한 template 객체를 복사하여 새로운 Document Element을 생성합니다.
    var newImage = $( "#template" ).clone();

    // 새로 만든 Document Element 객체를 수정합니다.
    newImage.attr( "id", "template_"+ Date.now() );
    newImage.attr( "src", thumbnailUrl );
    newImage.bind( "click", function(){
      var popup = window.open(imageUrl, '_blank', 'location=no');
    });

    // 새로 만든 Document Element 객체를 지정된 영역에 추가합니다.
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

        &lt;h2&gt;Uploaded images&lt;/h2&gt;
        &lt;div style="display:flex;" id="list"&gt;
        &lt;/div&gt;
        &lt;img id="template" class="img-thumbnail" style="display:none"&gt;
      &lt;/div&gt;
    &lt;/div&gt;
  &lt;/div&gt;
&lt;/div&gt;
</code>
</pre>


### 실행 결과

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
  // channel02 을 생성합니다.
  xpush.createSimpleChannel('channel02', function(){
    // 생성 후에 success 메시지를 보여줍니다.
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#danger" ).hide()
    $( "#success" ).html(html);
    $( "#success" ).show();

  });
});


var upload = function(){
  // File Document Element 객체를 가져옵니다.
  var inputObj = document.getElementById("uploadFile");

  // progressbar 를 가져옵니다.
  var progressbar = $("#progress_bar");
  var progressdiv = $("#progress_div");


 // Validation을 추가합니다.
  if( inputObj.value === '' ){
    var html =  '<strong>Upload Failed!</strong> Select file.';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    return;
  }

  // file의 type이 image type인지 체크합니다.
  var file = inputObj.files[0];
  if( file.type.indexOf( "image" ) < 0 ){

    var html =  '<strong>Upload Failed!</strong> Select image only';
    $( "#success" ).hide();
    $( "#danger" ).html(html);
    $( "#danger" ).show();

    inputObj.value = "";
    return;
  }

  // xpush의 uploadStream 함수를 사용하여 파일을 업로드합니다.
  xpush.uploadStream( 'channel02', {
    file: inputObj, type : 'image'
  }, function(data, idx){
    inputObj.value = "";

    // progress 정보를 받아 bar 형태로 보여줍니다.
    var progress = data+"%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.show();
  }, function(data,idx){

    inputObj.value = "";

    // thumbnail image를 다운로드 받을 수 있는 URL을 얻어 옵니다.
    var thumbnailUrl = xpush.getFileUrl('channel02', data.result.tname );
    // original image를 다운로드 받을 수 있는 URL을 얻어 옵니다.
    var imageUrl = xpush.getFileUrl('channel02', data.result.name );

    var progress = "0%";
    progressbar.html( progress );
    progressbar.css( { width : progress } );
    progressdiv.hide();

    // 업로드결과를 화면에 보여줍니다.
    var html =  '<strong>Upload Success!</strong> ' + imageUrl;
    $( "#danger" ).hide();
    $( "#success" ).html(html);
    $( "#success" ).show();

    // thumbnail image를 보여주기 위한 template 객체를 복사하여 새로운 Document Element을 생성합니다.
    var newImage = $( "#template" ).clone();

    // 새로 만든 Document Element 객체를 수정합니다.
    newImage.attr( "id", "template_"+ Date.now() );
    newImage.attr( "src", thumbnailUrl );
    newImage.bind( "click", function(){
      var popup = window.open(imageUrl, '_blank', 'location=no');
    });

    // 새로 만든 Document Element 객체를 지정된 영역에 추가합니다.
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

        <h2>Uploaded images</h2>
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
