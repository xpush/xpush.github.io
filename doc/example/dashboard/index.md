---
layout: doc
title: Example of dashboard
date: April 31, 2014
---

## Dashboard Example

간단한 dashboard 기능을 구현해 보겠습니다.

full source는 [여기](https://github.com/xpush/lib-xpush-web/blob/master/example/dashboard.html)에서 확인할 수 있습니다.

아래 데모에서 사용한 XPUSH 서버는 XPUSH 개발팀에서 제공하는 임시 테스트용 서버이므로, 일시적으로 동작하지 않거나 성능을 보장할 수 없습니다. 그러므로 여러분이 직접 설치하신 XPUSH 를 사용하시기 바랍니다.

### script import

ui를 위한 jquery 와 chart를 그리기 위한 d3.js, google jsapi, message 전송을 위한 xpush.js를 include 합니다.

<pre data-lang="javascript">
<code class="prettyprint">&lt;script type="text/javascript" src="https://www.google.com/jsapi"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="http://mbostock.github.com/d3/d3.js"&gt;&lt;/script&gt;

&lt;script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"&gt;&lt;/script&gt;
&lt;script src="http://xpush.github.io/lib/dist/xpush.js"&gt;&lt;/script&gt;
</code>
</pre>

### script code

google chart를 그리기 위한 option과 dataTable을 생성하고, chart를 초기화합니다. 

<pre data-lang="javascript">
<code class="prettyprint">// CPU Stats을 위한 gauge , Memory Stats 를 위한 corechart 를 import한다.
  google.load("visualization", "1", {packages:["gauge","corechart"]});
  google.setOnLoadCallback(initGoogleChart);

  var cpuChart;
  var memChart;

  var cpuDataTable;
  var memDataTable;

  var options = {
    width: 400, height: 200,
    redFrom: 90, redTo: 100,
    yellowFrom:75, yellowTo: 90,
    minorTicks: 5
  };

  // Line chart Option
  var memOptions = {
    width : "80%",
    min : 0,
    vAxis:{
      format : "#,###",
      minValue:0,
      maxValue:4,
      viewWindow:{min:0}
    },
    chartArea : {width:'80%'},
    pointSize : 3,
    animation : {
      duration: 1000,
      easing: 'out'
    },
    legend: { position: 'bottom' }
  };

  // Timestamp를 hh:min:ss 형태로 보여주는 함수
  function getTime(){
    var now = new Date();
    var hours="";
    var minutes ="";
    var seconds = "";
    if( now.getHours() &lt; 10 ){
      hours = "0"+ now.getHours();
    } else {
      hours = now.getHours();
    }

    if( now.getMinutes() &lt; 10 ){
      minutes = "0"+ now.getMinutes();
    } else {
      minutes = now.getMinutes();
    }

    if( now.getSeconds() &lt; 10 ){
      seconds = "0"+ now.getSeconds();
    } else {
      seconds = now.getSeconds();
    }
    var x = hours+":"+minutes+":"+seconds;
    return x;
  }

  // 숫자를 M, K 단위 포맷으로 변경하는 함수.
  function getNumberFormatString(number, isPoint){
    if( number &gt;= 1000000){
      if(isPoint) return "#,###.###M";
      else return "#,###M";
    } else if( number&gt;=1000 && number &lt; 1000000) {
      if(isPoint) return "#,###.###K";
      else return "#,###K";
    } else {
      if(isPoint) return "#,###.###";
      else return "#,###";
    }
  }

  // 숫자를 M, K 단위로 변경한다.
  function getNumberFormat(number, string){
    if(string.length == 6){
      if( "#,###M" == string ){
        return parseInt(number/1000000);
      }else if( "#,###K" ==string ){
        return parseInt(number/1000);
      } 
    } else if(string.length == 10) {
      if ( "#,###.###M" == string ){
        return number/1000000.0;
      } else if ( "#,###.###K" == string ){
        return number/1000.0;
      }
    } else if(string.length == 5) {
      return parseInt(number);
    } else if(string.length == 9) {
      return number;
    }
  }

  // 원형큐의 함수를 정의한다.
  var CircularQueueItem = function (value, next, back) {
    this.next = next;
    this.value = value;
    this.back = back;
    return this;
  };

  // 원형 큐를 생성한다.
  var CircularQueue = function (queueLength) {
    this._current = new CircularQueueItem(undefined, undefined, undefined);
    var item = this._current;
    for (var i = 0; i &lt; queueLength - 1; i++) {
        item.next = new CircularQueueItem(undefined, undefined, item);
        item = item.next;
    }
    item.next = this._current;
    this._current.back = item;

    this.push = function (value) {
      this._current.value = value;
      this._current = this._current.next;
    };
    this.pop = function () {
      this._current = this._current.back;
      return this._current.value;
    };
    return this;
  }

  // google chart를 그린다.
  function initGoogleChart() {

    // cpu gauge chart의 data 형식을 정의한다.
    cpuDataTable = google.visualization.arrayToDataTable([
      ['Label', 'Value'],
      ['stalk-front-c01', 0],
      ['stalk-front-c02', 0]
    ]);

    // memory line chart의 data 형식을 정의한다.
    memDataTable = new google.visualization.DataTable();

    memDataTable.addColumn('string', 'Time');
    memDataTable.addColumn('number', 'stalk-front-c01');
    memDataTable.addColumn('number', 'stalk-front-c02');

    // chart_cpu 영역에 gauge chart 를 그린다.
    cpuChart = new google.visualization.Gauge(document.getElementById('chart_cpu'));
    cpuChart.draw(cpuDataTable, options);

    // chart_mem 영역에 line chart 를 그린다.
    memChart = new google.visualization.LineChart(document.getElementById('chart_mem'));
    memChart.draw(memDataTable, memOptions);


    // size 변경시 memChart를 다시 그린다.
    window.onresize = function(){
      memChart.draw(memDataTable, memOptions);
    };
  }
</code>
</pre>

D3를 사용해서 말이 달리기 위한 영역을 그립니다. 말은 원형 큐에 달리는 모션순서대로 이미지를 6개 등록하고, 큐에서 하나씩 이미지를 꺼내와서 이미지 소스를 변경함으로써 달릴 수 있습니다.

<pre data-lang="javascript">
<code class="prettyprint">// serverlist를 변수로 설정한다.
  var hostNames = ["stalk-front-c01","stalk-front-c02"];
  var sorts = {"stalk-front-c01":0, "stalk-front-c02":1 };
  var serviceNames = ["server01", "server02"];

  var svg;
  var queues = [];
  var cw, z, w, h;

  // SVG를 이용해서 horse chart를 초기화한다.
  function initHorseChart(){
    w = 400,
    h = 250,
    cw = 200,
    z = d3.scale.category10();

    // svg를 그린다.
    svg = d3.select("#transaction").append("svg:svg")
    .attr("width", w)
    .attr("height", h);
    
    var data = [0,1];

    // server명을 보여주기 위한 사각형을 그린다.
    var sel = svg.selectAll("rect").data(data).enter();
    sel.append("rect")
    .attr("id", function(d) {return "rect_"+serviceNames[d];})
    .attr("fill", "white" )
    .attr("stroke", "#999")
    .attr("width", 100)
    .attr("height", 40)
    .attr("x", function(d) {return ( d * cw ) + 50;} )
    .attr("y", 210);
    
    // server명을 TEXT 형태로 보여준다.
    sel.append("svg:text")
    .attr("fill", "black")
    .attr("x", function(d) {return ( d * cw ) + 100;})
    .attr("y", 235)
    .attr("text-anchor", "middle")
    .style("font-size", "12px")
    .text(function(d) {return hostNames[d]});
    
    // horse image를 추가한다.
    sel.append("image")
    .attr("id", function(d) {return "image"+d;})
    .attr( "xlink:href", "assets/0.png" )
    .attr("x", function(d) {return ( d * cw ) + 55;} )
    .attr("y", 80 )
    .attr("width", 100)
    .attr("height", 100)
    .attr("opacity", "1" );

    // Circle Queue 를 생성한다.
    for( var key in data ){
      var queue = new CircularQueue(6);
      queue.push("5");
      queue.push("4");
      queue.push("3");
      queue.push("2");
      queue.push("1");
      queue.push("0");
      queues.push( queue );
    }
  }
</code>
</pre>

XPUSH 객체를 초기화하고 DB data나 System data가 들어올 때, 해당 chart나 변수를 호출하도록 합니다.

<pre data-lang="javascript">
<code class="prettyprint">var mem01;
  var mem02;
  var memFormat;
  var xpush;

  var u01 = 0;
  var u02 = 0;

  // xpush를 초기화 하고 event listener를 등록한다.
  function initXpush(){
    xpush = new XPush('http://demo.stalk.io:8000', 'demo');
    xpush.createSimpleChannel('channel-dashboard', function(){
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      // `message` event로 들어오는 data를 받아 화면에 출력합니다.
      xpush.on( 'message', function(channel, name, data){

        if( data.type && data.type == 'db' ) {
          drawTopSite( data.json );
        } else if ( data.type && data.type == 'sys' ) {
          cpuDataTable.setValue(sorts[data.json.H], 1, Math.round(data.json.C));
          cpuChart.draw(cpuDataTable, options);

          if( 0 == sorts[data.json.H] ){
            // memory 변수를 변경합니다.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem01 = getNumberFormat( Number( data.json.M ), memFormat);

            // User 변수를 변경합니다.
            u01 = data.json.U;
          } else if( 1 == sorts[data.json.H]  ){
            // memory 변수를 변경합니다.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem02 = getNumberFormat( Number( data.json.M ), memFormat);

            // User 변수를 변경합니다.
            u02 = data.json.U;
          }
        }
      });
    });
  };

  $(document).ready( function(){
    initHorseChart();
    initXpush();
  });
</code>
</pre>

각 chart를 update하기 위한 함수들을 선언합니다. 값에 따라 말의 이미지가 바뀌는 interval의 속도를 조절하면 말이 달리는 속도를 변경할 수 있습니다.

<pre data-lang="javascript">
<code class="prettyprint">var startFlag = false;
  var monitor;
  var btnToggle = $( "#btnToggle" );

  // 메모리 line chart를 그린다.
  var drawMemChart = function(){

    // line chart의 X축은 시간이 다를수 있기에 5초에 한번씩 서버에서 받은 memory value를 이용하여 그린다.
    monitor = setInterval( function() {
      if( mem01 && mem02 ){
        var time = getTime();
        memOptions.vAxis.format = memFormat;
        memDataTable.addRow( [time, mem01, mem02] );

        if( memDataTable.getNumberOfRows() > 5 ) {
          memDataTable.removeRow( memDataTable.getNumberOfRows() -6 );
        }

        memChart.draw(memDataTable,memOptions);
      }

      drawSysChart( [{ 'H':'stalk-front-c01','V':u01 },{ 'H':'stalk-front-c02','V':u02 }]);
    }, 5000 );
  };

  // 모니터링기능을 켜거나 끈다.
  var toggleMonitor = function(){
    if( !startFlag  ){
      // 서버에 모니터링이 시작함을 알리는 메세지를 보낸다.
      xpush.send('channel-dashboard', 'message', 'START_MONITOR' );
      startFlag = true;
      btnToggle.removeClass( "btn-primary" ).addClass( "btn-danger" );
      btnToggle.html( "Off" );
      drawMemChart();

      $( "#site_body" ).hide();
      $( "#site_list" ).show();
    } else {
      // 서버에 모니터링이 끈남을 알리는 메세지를 보낸다.
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      startFlag = false;
      mem01 = 0;
      mem02 = 0;
      u01 = 0;
      u02 = 0;
      btnToggle.removeClass( "btn-danger" ).addClass( "btn-primary" );
      btnToggle.html( "On" );

      $( "#site_body" ).show();
      $( "#site_list" ).hide();

      if( monitor ){
        clearInterval( monitor );
      }
    }
  };

  // 5초간 동심원을 그린다.
  function drawParticle( cx, cy, radius, sort ) {     
    svg.append("svg:circle")
    .attr("cx", cx)
    .attr("cy", cy)
    .attr("r", 1e-6)
    .style("stroke", z(sort * 9))
    .style("stroke-opacity", 1)
    .transition()
    .duration(5000)
    .ease(Math.sqrt)
    .attr("r", radius)
    .style("stroke-opacity", 1e-6)
    .remove();
  }

  var topSite = [];
  var drawTopSite = function( json ){
    // template을 복사하여 새로운 DOM 객체를 생성합니다
    $( "#site_list" ).children().remove();
    for( var key in json ){
      var data = json[key];
      var liId = data.S.replace( ".", "" );
      
      if( $( "#template_" + data.S ).length != 0 ){
        var site = $( "#template_" + liId ).eq(0);
        site.children( "span:first" ).text( data.C );
        site.children( "span:last" ).text( data.S );
      } else {
        var newSite = $( "#template" ).clone();

        // 새로 만든 DOM 객체를 수정합니다.
        newSite.attr( "id", "template_"+ liId );
        newSite.children( "span:first" ).text( data.C );
        newSite.children( "span:last" ).text( data.S );

        // 새로 만든 DOM 객체를 ul DOM에 추가합니다.
        newSite.appendTo( "#site_list" );
        newSite.show();
      }
    }
  };
 
  // 말을 달리게 한다.
  function runHorse( val, sort ){
    var img = svg.select("#image"+ sort );
    if( val == 0 ){
      if( img.attr("opacity") == 1 ){
        img.attr( "opacity", "1" )
        .transition()
        .duration(500)
        .attr( "opacity", "0.5" );
      }
      return;
    } else {
      if( img.attr("opacity") == 0.5 ){
        img.attr( "opacity", "0.5" )
        .transition()
        .duration(500)
        .attr( "opacity", "1" );
      }
    }
    
    // 말이 달리는 주기를 조절한다.
    var speed = Math.round( 50000 / val );
    
    svg.append("svg:text")
    .attr("fill", "#000")
    .attr("x", (sort * cw ) + 105 )
    .attr("y", 190)
    .attr("text-anchor", "end")
    .style("font-size", "16px")
    .text( val )
    .transition()
    .duration(5200)
    .style("stroke-opacity", 1e-6)
    .remove(); 
    
    var now = Date.now();
    // 말이 달리기 위해 주기마다 이미지를 변환한다.
    var t = setInterval(function(){
      img.attr( "xlink:href", "assets/"+queues[sort].pop() +".png" );
      if ( Date.now() - now > 5250 ){
        clearInterval(t);
      }
    }, speed );
  }
  
  // 동심원 영역을 그린다.
  function drawSysChart( json ){

    var totalUsers = 0;
    for (var inx = 0, len = json.length; inx < len ; inx ++){
      var val = json[inx].V;
      totalUsers = plus( totalUsers, Number( val ) );
      var sort = sorts[json[inx].H];

      // 동심원과 함께 말이 달리게 한다.
      runHorse( val, sort );
      
      if( val != 0 ){
        // Position X
        var cx = ( sort * cw ) + 85;

       	// 동심원의 개수를 계산한다.
        var until = Math.floor( val / 200 ) * 5 ;
        
        var jnx = 1;
        if( until < 1 ){
          until = 1;
        }         
        
        // 동심원의 갯수만큼 동심원을 그린다.
        while( jnx <= until ){              
          drawParticle( cx, 130, val * ( jnx * ( 1/until) ), sort );
          jnx++;
        }
      }
    }
    setTotal( Number(totalUsers).toFixed(2) );
  }
  
  // Total 영역을 그린다.
  function setTotal( totalUsers ){
    svg.append("svg:text")
    .attr("fill", "#000")
    .attr("x", 250 )
    .attr("y", 65)
    .attr("text-anchor", "end")
    .style("font-size", "30px")
    .text( totalUsers )
    .transition()
    .duration(5200)
    .style("stroke-opacity", 1e-6)
    .remove();
  }
  
  // 소숫점을 포함하여 더하기를 하는 함수
  function plus() {
    var argsLength = arguments.length;
     
    if(argsLength == 0) return 0;
     
    var result = 0.0;  //결과값 
    var argString = "";  //문자열로 변환된 파라미터
    var pointIndex = 0;  //소수점의 위치
    var decimalSize = 0; //소수의 자리수
    var maxDecimalSize = 0; //파라미터 중 가장 소수점이 큰 수의 소수 자리수
    var maxPower = 1.0;  //파라미터 중 가장 소수점이 큰 수의 소수 자리수 * 10
     
    for(var inx = 0; inx < argsLength; inx++) {
     
      argString = arguments[inx] + "";
      pointIndex = argString.indexOf(".");
      decimalSize = (pointIndex < 0) ? 0 : argString.length - pointIndex - 1;
      
      if(maxDecimalSize < decimalSize) {
        result = result * Math.pow(10.0, decimalSize - maxDecimalSize);
        maxDecimalSize = decimalSize;
        maxPower = Math.pow(10.0, maxDecimalSize);
      }
      
      result += eval((parseFloat(arguments[inx]) * maxPower).toFixed(0));
    }
    return (result / maxPower);
  }
</code>
</pre>

### HTML code

<pre data-lang="html">
<code class="prettyprint">&lt;div class="row"&gt;
  &lt;div &gt;

    &lt;!-- Title and button--&gt;
    &lt;div class="alert alert-block alert-success"&gt;

      &lt;i class="icon-ok green"&gt;&lt;/i&gt;
      Welcome to
      &lt;strong class="green"&gt;
        XPUSH Dashboard
      &lt;/strong&gt;
      &lt;button type="button" class="btn btn-primary" id="btnToggle" onclick="toggleMonitor()"&gt;On&lt;/button&gt;
    &lt;/div&gt;

    &lt;div class="row"&gt;
      &lt;!-- CPU Gauge Chart --&gt;
      &lt;div class="col-sm-6"&gt;
        &lt;div class="panel panel-default"&gt;
          &lt;div class="panel-heading"&gt;
            &lt;h4 class="lighter"&gt;
              &lt;span class="glyphicon glyphicon-stats"&gt;&lt;/span&gt;&nbsp;CPU Stats
            &lt;/h4&gt;
          &lt;/div&gt;

          &lt;div class="panel-body"&gt;
            &lt;div id="chart_cpu" &gt;&lt;/div&gt;               
          &lt;/div&gt;
        &lt;/div&gt;
      &lt;/div&gt;
      &lt;!-- Memory Line Chart --&gt;
      &lt;div class="col-sm-6"&gt;
        &lt;div class="panel panel-default"&gt;
          &lt;div class="panel-heading"&gt;
            &lt;h4 class="lighter"&gt;
              &lt;span class="glyphicon glyphicon-stats"&gt;&lt;/span&gt;&nbsp;Memory Stats
            &lt;/h4&gt;
          &lt;/div&gt;
          &lt;div class="panel-body"&gt;
            &lt;div id="chart_mem" &gt;&lt;/div&gt;  
          &lt;/div&gt;
        &lt;/div&gt;
      &lt;/div&gt;
    &lt;/div&gt;

    &lt;div class="row"&gt;
      &lt;!-- Horse Chart --&gt;
      &lt;div class="col-sm-6"&gt;
        &lt;div class="panel panel-default"&gt;
          &lt;div class="panel-heading"&gt;
            &lt;h4 class="lighter"&gt;
              &lt;span class="glyphicon glyphicon-stats"&gt;&lt;/span&gt;&nbsp;Connected Users
            &lt;/h4&gt;
          &lt;/div&gt;
          &lt;div class="panel-body"&gt;
            &lt;div id="transaction" style="text-align:center;"&gt;
            &lt;/div&gt;
          &lt;/div&gt;
        &lt;/div&gt;
      &lt;/div&gt;

      &lt;!-- Ranking Chart --&gt;
      &lt;div class="col-sm-6"&gt;
        &lt;div class="panel panel-default"&gt;
          &lt;div class="panel-heading"&gt;
            &lt;h4 class="lighter"&gt;
              &lt;span class="glyphicon glyphicon-stats"&gt;&lt;/span&gt;&nbsp;Site Ranking : Active Users
            &lt;/h4&gt;
          &lt;/div&gt;
          &lt;div class="panel-body" id="site_body"&gt;
            &lt;div style="min-height:225px;"&gt;
              &lt;h2&gt;Click button to view&lt;/h2&gt;
            &lt;/div&gt;
          &lt;/div&gt;
          &lt;ul class="list-group" id="site_list" style="display:node;"&gt;
            &lt;li class="list-group-item" style="display:none"&gt;
              &lt;span class="badge" &gt;0&lt;/span&gt;&lt;span&gt;&lt;/span&gt;
            &lt;/li&gt;                
          &lt;/ul&gt;
          &lt;li class="list-group-item" id="template" style="display:none"&gt;
            &lt;span class="badge" &gt;0&lt;/span&gt;&lt;span&gt;&lt;/span&gt;
          &lt;/li&gt;              
      &lt;/div&gt;
    &lt;/div&gt;
  &lt;/div&gt;
&lt;/div&gt;
</code>
</pre>

### 실행 결과

<style type="text/css">
  circle {
    fill: none;
    stroke-width: 1.5px;
	}

	#chart_cpu {
    width: 400px;
    height: 200px;
    margin: 0px auto;
	}

  #btnToggle {
    float:right;
    margin-top:-6px; 
  }
</style>

<script type="text/javascript" src="https://www.google.com/jsapi"></script>
<script type="text/javascript" src="http://mbostock.github.com/d3/d3.js"></script>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>
<script type="text/javascript">  
  // CPU Stats을 위한 gauge , Memory Stats 를 위한 corechart 를 import한다.
  google.load("visualization", "1", {packages:["gauge","corechart"]});
  google.setOnLoadCallback(initGoogleChart);

  var cpuChart;
  var memChart;

  var cpuDataTable;
  var memDataTable;

  var options = {
    width: 400, height: 200,
    redFrom: 90, redTo: 100,
    yellowFrom:75, yellowTo: 90,
    minorTicks: 5
  };

  // Line chart Option
  var memOptions = {
    width : "80%",
    min : 0,
    vAxis:{
      format : "#,###",
      minValue:0,
      maxValue:4,
      viewWindow:{min:0}
    },
    chartArea : {width:'80%'},
    pointSize : 3,
    animation : {
      duration: 1000,
      easing: 'out'
    },
    legend: { position: 'bottom' }
  };

  // Timestamp를 hh:min:ss 형태로 보여주는 함수
  function getTime(){
    var now = new Date();
    var hours="";
    var minutes ="";
    var seconds = "";
    if( now.getHours() < 10 ){
      hours = "0"+ now.getHours();
    } else {
      hours = now.getHours();
    }

    if( now.getMinutes() < 10 ){
      minutes = "0"+ now.getMinutes();
    } else {
      minutes = now.getMinutes();
    }

    if( now.getSeconds() < 10 ){
      seconds = "0"+ now.getSeconds();
    } else {
      seconds = now.getSeconds();
    }
    var x = hours+":"+minutes+":"+seconds;
    return x;
  }

  // 숫자를 M, K 단위 포맷으로 변경하는 함수.
  function getNumberFormatString(number, isPoint){
    if( number >= 1000000){
      if(isPoint) return "#,###.###M";
      else return "#,###M";
    } else if( number>=1000 && number < 1000000) {
      if(isPoint) return "#,###.###K";
      else return "#,###K";
    } else {
      if(isPoint) return "#,###.###";
      else return "#,###";
    }
  }

  // 숫자를 M, K 단위로 변경한다.
  function getNumberFormat(number, string){
    if(string.length == 6){
      if( "#,###M" == string ){
        return parseInt(number/1000000);
      }else if( "#,###K" ==string ){
        return parseInt(number/1000);
      } 
    } else if(string.length == 10) {
      if ( "#,###.###M" == string ){
        return number/1000000.0;
      } else if ( "#,###.###K" == string ){
        return number/1000.0;
      }
    } else if(string.length == 5) {
      return parseInt(number);
    } else if(string.length == 9) {
      return number;
    }
  }

  // 원형큐의 함수를 정의한다.
  var CircularQueueItem = function (value, next, back) {
    this.next = next;
    this.value = value;
    this.back = back;
    return this;
  };

  // 원형 큐를 생성한다.
  var CircularQueue = function (queueLength) {
    this._current = new CircularQueueItem(undefined, undefined, undefined);
    var item = this._current;
    for (var i = 0; i < queueLength - 1; i++) {
        item.next = new CircularQueueItem(undefined, undefined, item);
        item = item.next;
    }
    item.next = this._current;
    this._current.back = item;

    this.push = function (value) {
      this._current.value = value;
      this._current = this._current.next;
    };
    this.pop = function () {
      this._current = this._current.back;
      return this._current.value;
    };
    return this;
  }

  // google chart를 그린다.
  function initGoogleChart() {

    // cpu gauge chart의 data 형식을 정의한다.
    cpuDataTable = google.visualization.arrayToDataTable([
      ['Label', 'Value'],
      ['stalk-front-c01', 0],
      ['stalk-front-c02', 0]
    ]);

    // memory line chart의 data 형식을 정의한다.
    memDataTable = new google.visualization.DataTable();

    memDataTable.addColumn('string', 'Time');
    memDataTable.addColumn('number', 'stalk-front-c01');
    memDataTable.addColumn('number', 'stalk-front-c02');

    // chart_cpu 영역에 gauge chart 를 그린다.
    cpuChart = new google.visualization.Gauge(document.getElementById('chart_cpu'));
    cpuChart.draw(cpuDataTable, options);

    // chart_mem 영역에 line chart 를 그린다.
    memChart = new google.visualization.LineChart(document.getElementById('chart_mem'));
    memChart.draw(memDataTable, memOptions);


    // size 변경시 memChart를 다시 그린다.
    window.onresize = function(){
      memChart.draw(memDataTable, memOptions);
    };
  }

  // serverlist를 변수로 설정한다.
  var hostNames = ["stalk-front-c01","stalk-front-c02"];
  var sorts = {"stalk-front-c01":0, "stalk-front-c02":1 };
  var serviceNames = ["server01", "server02"];

  var svg;
  var queues = [];
  var cw, z, w, h;

  // SVG를 이용해서 horse chart를 초기화한다.
  function initHorseChart(){
    w = 400,
    h = 250,
    cw = 200,
    z = d3.scale.category10();

    // svg를 그린다.
    svg = d3.select("#transaction").append("svg:svg")
    .attr("width", w)
    .attr("height", h);
    
    var data = [0,1];

    // server명을 보여주기 위한 사각형을 그린다.
    var sel = svg.selectAll("rect").data(data).enter();
    sel.append("rect")
    .attr("id", function(d) {return "rect_"+serviceNames[d];})
    .attr("fill", "white" )
    .attr("stroke", "#999")
    .attr("width", 100)
    .attr("height", 40)
    .attr("x", function(d) {return ( d * cw ) + 50;} )
    .attr("y", 210);
    
    // server명을 TEXT 형태로 보여준다.
    sel.append("svg:text")
    .attr("fill", "black")
    .attr("x", function(d) {return ( d * cw ) + 100;})
    .attr("y", 235)
    .attr("text-anchor", "middle")
    .style("font-size", "12px")
    .text(function(d) {return hostNames[d]});
    
    // horse image를 추가한다.
    sel.append("image")
    .attr("id", function(d) {return "image"+d;})
    .attr( "xlink:href", "assets/0.png" )
    .attr("x", function(d) {return ( d * cw ) + 55;} )
    .attr("y", 80 )
    .attr("width", 100)
    .attr("height", 100)
    .attr("opacity", "1" );

    // Circle Queue 를 생성한다.
    for( var key in data ){
      var queue = new CircularQueue(6);
      queue.push("5");
      queue.push("4");
      queue.push("3");
      queue.push("2");
      queue.push("1");
      queue.push("0");
      queues.push( queue );
    }
  }

  var mem01;
  var mem02;
  var memFormat;
  var xpush;

  var u01 = 0;
  var u02 = 0;

  // xpush를 초기화 하고 event listener를 등록한다.
  function initXpush(){
    xpush = new XPush('http://demo.stalk.io:8000', 'demo');
    xpush.createSimpleChannel('channel-dashboard', function(){
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      // `message` event로 들어오는 data를 받아 화면에 출력합니다.
      xpush.on( 'message', function(channel, name, data){

        if( data.type && data.type == 'db' ) {
          drawTopSite( data.json );
        } else if ( data.type && data.type == 'sys' ) {
          cpuDataTable.setValue(sorts[data.json.H], 1, Math.round(data.json.C));
          cpuChart.draw(cpuDataTable, options);

          if( 0 == sorts[data.json.H] ){
            // memory 변수를 변경합니다.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem01 = getNumberFormat( Number( data.json.M ), memFormat);

            // User 변수를 변경합니다.
            u01 = data.json.U;
          } else if( 1 == sorts[data.json.H]  ){
            // memory 변수를 변경합니다.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem02 = getNumberFormat( Number( data.json.M ), memFormat);

            // User 변수를 변경합니다.
            u02 = data.json.U;
          }
        }
      });
    });
  };

  $(document).ready( function(){
    initHorseChart();
    initXpush();
  });

  var startFlag = false;
  var monitor;
  var btnToggle = $( "#btnToggle" );

  // 메모리 line chart를 그린다.
  var drawMemChart = function(){

    // line chart의 X축은 시간이 다를수 있기에 5초에 한번씩 서버에서 받은 memory value를 이용하여 그린다.
    monitor = setInterval( function() {
      if( mem01 && mem02 ){
        var time = getTime();
        memOptions.vAxis.format = memFormat;
        memDataTable.addRow( [time, mem01, mem02] );

        if( memDataTable.getNumberOfRows() > 5 ) {
          memDataTable.removeRow( memDataTable.getNumberOfRows() -6 );
        }

        memChart.draw(memDataTable,memOptions);
      }

      drawSysChart( [{ 'H':'stalk-front-c01','V':u01 },{ 'H':'stalk-front-c02','V':u02 }]);
    }, 5000 );
  };

  // 모니터링기능을 켜거나 끈다.
  var toggleMonitor = function(){
    if( !startFlag  ){
      // 서버에 모니터링이 시작함을 알리는 메세지를 보낸다.
      xpush.send('channel-dashboard', 'message', 'START_MONITOR' );
      startFlag = true;
      btnToggle.removeClass( "btn-primary" ).addClass( "btn-danger" );
      btnToggle.html( "Off" );
      drawMemChart();

      $( "#site_body" ).hide();
      $( "#site_list" ).show();
    } else {
      // 서버에 모니터링이 끈남을 알리는 메세지를 보낸다.
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      startFlag = false;
      mem01 = 0;
      mem02 = 0;
      u01 = 0;
      u02 = 0;
      btnToggle.removeClass( "btn-danger" ).addClass( "btn-primary" );
      btnToggle.html( "On" );

      $( "#site_body" ).show();
      $( "#site_list" ).hide();

      if( monitor ){
        clearInterval( monitor );
      }
    }
  };

  // 5초간 동심원을 그린다.
  function drawParticle( cx, cy, radius, sort ) {     
    svg.append("svg:circle")
    .attr("cx", cx)
    .attr("cy", cy)
    .attr("r", 1e-6)
    .style("stroke", z(sort * 9))
    .style("stroke-opacity", 1)
    .transition()
    .duration(5000)
    .ease(Math.sqrt)
    .attr("r", radius)
    .style("stroke-opacity", 1e-6)
    .remove();
  }

  var topSite = [];
  var drawTopSite = function( json ){
    // template을 복사하여 새로운 DOM 객체를 생성합니다
    $( "#site_list" ).children().remove();
    for( var key in json ){
      var data = json[key];
      var liId = data.S.replace( ".", "" );
      
      if( $( "#template_" + data.S ).length != 0 ){
        var site = $( "#template_" + liId ).eq(0);
        site.children( "span:first" ).text( data.C );
        site.children( "span:last" ).text( data.S );
      } else {
        var newSite = $( "#template" ).clone();

        // 새로 만든 DOM 객체를 수정합니다.
        newSite.attr( "id", "template_"+ liId );
        newSite.children( "span:first" ).text( data.C );
        newSite.children( "span:last" ).text( data.S );

        // 새로 만든 DOM 객체를 ul DOM에 추가합니다.
        newSite.appendTo( "#site_list" );
        newSite.show();
      }
    }
  };
 
  // 말을 달리게 한다.
  function runHorse( val, sort ){
    var img = svg.select("#image"+ sort );
    if( val == 0 ){
      if( img.attr("opacity") == 1 ){
        img.attr( "opacity", "1" )
        .transition()
        .duration(500)
        .attr( "opacity", "0.5" );
      }
      return;
    } else {
      if( img.attr("opacity") == 0.5 ){
        img.attr( "opacity", "0.5" )
        .transition()
        .duration(500)
        .attr( "opacity", "1" );
      }
    }
    
    // 말이 달리는 주기를 조절한다.
    var speed = Math.round( 50000 / val );
    
    svg.append("svg:text")
    .attr("fill", "#000")
    .attr("x", (sort * cw ) + 105 )
    .attr("y", 190)
    .attr("text-anchor", "end")
    .style("font-size", "16px")
    .text( val )
    .transition()
    .duration(5200)
    .style("stroke-opacity", 1e-6)
    .remove(); 
    
    var now = Date.now();
    // 말이 달리기 위해 주기마다 이미지를 변환한다.
    var t = setInterval(function(){
      img.attr( "xlink:href", "assets/"+queues[sort].pop() +".png" );
      if ( Date.now() - now > 5250 ){
        clearInterval(t);
      }
    }, speed );
  }
  
  // 동심원 영역을 그린다.
  function drawSysChart( json ){

    var totalUsers = 0;
    for (var inx = 0, len = json.length; inx < len ; inx ++){
      var val = json[inx].V;
      totalUsers = plus( totalUsers, Number( val ) );
      var sort = sorts[json[inx].H];

      // 동심원과 함께 말이 달리게 한다.
      runHorse( val, sort );
      
      if( val != 0 ){
        // Position X
        var cx = ( sort * cw ) + 85;

        // 동심원의 개수를 계산한다.
        var until = Math.floor( val / 200 ) * 5 ;
        
        var jnx = 1;
        if( until < 1 ){
          until = 1;
        }         
        
        // 동심원의 갯수만큼 동심원을 그린다.
        while( jnx <= until ){              
          drawParticle( cx, 130, val * ( jnx * ( 1/until) ), sort );
          jnx++;
        }
      }
    }
    setTotal( Number(totalUsers).toFixed(2) );
  }
  
  // Total 영역을 그린다.
  function setTotal( totalUsers ){
    svg.append("svg:text")
    .attr("fill", "#000")
    .attr("x", 250 )
    .attr("y", 65)
    .attr("text-anchor", "end")
    .style("font-size", "30px")
    .text( totalUsers )
    .transition()
    .duration(5200)
    .style("stroke-opacity", 1e-6)
    .remove();
  }
  
  // 소숫점을 포함하여 더하기를 하는 함수
  function plus() {
    var argsLength = arguments.length;
     
    if(argsLength == 0) return 0;
     
    var result = 0.0;  //결과값 
    var argString = "";  //문자열로 변환된 파라미터
    var pointIndex = 0;  //소수점의 위치
    var decimalSize = 0; //소수의 자리수
    var maxDecimalSize = 0; //파라미터 중 가장 소수점이 큰 수의 소수 자리수
    var maxPower = 1.0;  //파라미터 중 가장 소수점이 큰 수의 소수 자리수 * 10
     
    for(var inx = 0; inx < argsLength; inx++) {
     
      argString = arguments[inx] + "";
      pointIndex = argString.indexOf(".");
      decimalSize = (pointIndex < 0) ? 0 : argString.length - pointIndex - 1;
      
      if(maxDecimalSize < decimalSize) {
        result = result * Math.pow(10.0, decimalSize - maxDecimalSize);
        maxDecimalSize = decimalSize;
        maxPower = Math.pow(10.0, maxDecimalSize);
      }
      
      result += eval((parseFloat(arguments[inx]) * maxPower).toFixed(0));
    }
    return (result / maxPower);
  }
</script>

<div class="row">
  <div >

    <!-- Title and button-->
    <div class="alert alert-block alert-success">

      <i class="icon-ok green"></i>
      Welcome to
      <strong class="green">
        XPUSH Dashboard
      </strong>
      <button type="button" class="btn btn-primary" id="btnToggle" onclick="toggleMonitor()">On</button>
    </div>

    <div class="row">
      <!-- CPU Gauge Chart -->
      <div class="col-sm-6">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h4 class="lighter">
              <span class="glyphicon glyphicon-stats"></span>&nbsp;CPU Stats
            </h4>
          </div>

          <div class="panel-body">
            <div id="chart_cpu" ></div>               
          </div>
        </div>
      </div>
      <!-- Memory Line Chart -->
      <div class="col-sm-6">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h4 class="lighter">
              <span class="glyphicon glyphicon-stats"></span>&nbsp;Memory Stats
            </h4>
          </div>
          <div class="panel-body">
            <div id="chart_mem" ></div>  
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <!-- Horse Chart -->
      <div class="col-sm-6">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h4 class="lighter">
              <span class="glyphicon glyphicon-stats"></span>&nbsp;Connected Users
            </h4>
          </div>
          <div class="panel-body">
            <div id="transaction" style="text-align:center;">
            </div>
          </div>
        </div>
      </div>

      <!-- Ranking Chart -->
      <div class="col-sm-6">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h4 class="lighter">
              <span class="glyphicon glyphicon-stats"></span>&nbsp;Site Ranking : Active Users
            </h4>
          </div>
          <div class="panel-body" id="site_body">
            <div style="min-height:225px;">
              <h2>Click button to view</h2>
            </div>
          </div>
          <ul class="list-group" id="site_list" style="display:node;">
            <li class="list-group-item" style="display:none">
              <span class="badge" >0</span><span></span>
            </li>                
          </ul>
          <li class="list-group-item" id="template" style="display:none">
            <span class="badge" >0</span><span></span>
          </li>              
      </div>
    </div>
  </div>
</div>

<script type="text/javascript">
  prettyPrint();
</script>
