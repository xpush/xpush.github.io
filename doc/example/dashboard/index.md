---
layout: doc
title: Example of dashboard
date: April 31, 2014
---

## Dashboard Example

간단한 dashboard 기능을 구현해 보겠습니다.

full source는 [여기](https://github.com/xpush/lib-xpush-web/blob/master/example/dashboard.html)에서 확인할 수 있습니다.

아래 데모에서 사용한 XPUSH 서버는 XPUSH 개발팀에서 제공하는 임시 테스트용 서버이므로, 일시적으로 동작하지 않거나 성능을 보장할 수 없습니다. 그러므로 여러분이 직접 설치하신 XPUSH 를 사용하시기 바랍니다.

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
</style>

<script type="text/javascript" src="https://www.google.com/jsapi"></script>
<script type="text/javascript" src="http://mbostock.github.com/d3/d3.js"></script>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>
<script type="text/javascript">
  google.load("visualization", "1", {packages:["gauge","corechart"]});
  google.setOnLoadCallback(drawChart);

  var cpuChart;
  var memChart;

  var options;
  var cpuDataTable;
  var memDataTable;

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

  function getNumberFormat(number, string){
    if(string.length==6){
      if( "#,###M" ==string ){
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

  function drawChart() {
    cpuDataTable = google.visualization.arrayToDataTable([
      ['Label', 'Value'],
      ['stalk-front-c01', 0],
      ['stalk-front-c02', 0],
    ]);

    memDataTable = new google.visualization.DataTable();

    memDataTable.addColumn('string', 'Time');
    memDataTable.addColumn('number', 'stalk-front-c01');
    memDataTable.addColumn('number', 'stalk-front-c02');

    options = {
      width: 400, height: 200,
      redFrom: 90, redTo: 100,
      yellowFrom:75, yellowTo: 90,
      minorTicks: 5
    };

    cpuChart = new google.visualization.Gauge(document.getElementById('chart_cpu'));
    cpuChart.draw(cpuDataTable, options);

    memChart = new google.visualization.LineChart(document.getElementById('chart_mem'));
    memChart.draw(memDataTable, memOptions);

    window.onresize = function(){
      memChart.draw(memDataTable, memOptions);
    };      
  }
</script>

<div class="row">
  <div >
    <!--PAGE CONTENT BEGINS--> 
    <div class="alert alert-block alert-success">

      <i class="icon-ok green"></i>
      Welcome to
      <strong class="green">
        XPUSH Dashboard
      </strong>
      <button type="button" class="btn btn-primary" id="btnToggle" style="float:right;margin-top:-6px;" onclick="toggleMonitor()">On</button>
    </div>

    <div class="row">
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
      <div class="col-sm-6">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h4 class="lighter">
              <span class="glyphicon glyphicon-stats"></span>&nbsp;TPS View
            </h4>
          </div>
          <div class="panel-body">
            <div id="transaction" style="text-align:center;">
            </div>
          </div>
        </div>
      </div>

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
  var hostNames = ["stalk-front-c01","stalk-front-c02"];
  var sorts = {};
  sorts["stalk-front-c01"] = 0;
  sorts["stalk-front-c02"] = 1;
  var serviceNames = ["server01", "server02"];
  
  var CircularQueueItem = function (value, next, back) {
    this.next = next;
    this.value = value;
    this.back = back;
    return this;
  };

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
  
  var mem01;
  var mem02;
  var memFormat;
  var xpush;

  var u01 = 0;
  var u02 = 0;
  function initialize(){
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
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem01 = getNumberFormat( Number( data.json.M ), memFormat);
            u01 = data.json.U;
          } else if( 1 == sorts[data.json.H]  ){
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem02 = getNumberFormat( Number( data.json.M ), memFormat);
            u02 = data.json.U;
          }
        }
      });
    });
  } 
  initialize();

  var startFlag = false;
  var monitor;
  var btnToggle = document.getElementById( "btnToggle" );

  var drawMemChart = function(){
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

      viewParticle( [{ 'H':'stalk-front-c01','V':u01 },{ 'H':'stalk-front-c02','V':u02 }]);
    }, 5000 );
  };

  var topSite = [];
  var drawTopSite = function( json ){
    // template을 복사하여 새로운 DOM 객체를 생성합니다..
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
  }

  var toggleMonitor = function(){
    if( !startFlag  ){
      xpush.send('channel-dashboard', 'message', 'START_MONITOR' );
      startFlag = true;
      btnToggle.className = "btn btn-danger" ;
      btnToggle.innerHTML = "Off";
      drawMemChart();

      $( "#site_body" ).hide();
      $( "#site_list" ).show();
    } else {
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      startFlag = false;
      mem01 = 0;
      mem02 = 0;
      u01 = 0;
      u02 = 0;
      btnToggle.className = "btn btn-primary" ;
      btnToggle.innerHTML = "On";

      $( "#site_body" ).show();
      $( "#site_list" ).hide();

      if( monitor ){
        clearInterval( monitor );
      }
    }
  }
</script>

<script type="text/javascript">
  var w = 400,
    h = 250,
    z = d3.scale.category10();
  var cw = 200;

  var svg = d3.select("#transaction").append("svg:svg")
  .attr("width", w)
  .attr("height", h);
  
  var data = [0,1];
  var sel = svg.selectAll("rect").data(data).enter();
  sel.append("rect")
  .attr("id", function(d) {return "rect_"+serviceNames[d];})
  .attr("fill", "white" )
  .attr("stroke", "#999" )
  .attr("width", 100)
  .attr("height", 40)
  .attr("x", function(d) {return ( d * cw ) + 50;} )
  .attr("y", 210);
  
  sel.append("svg:text")
  .attr("fill", "black")
  .attr("x", function(d) {return ( d * cw ) + 100;})
  .attr("y", 235)
  .attr("text-anchor", "middle")
  .style("font-size", "12px")
  .text(function(d) {return hostNames[d]});
  
  sel.append("image")
  .attr("id", function(d) {return "image"+d;})
  .attr( "xlink:href", "assets/0.png" )
  .attr("x", function(d) {return ( d * cw ) + 55;} )
  .attr("y", 80 )
  .attr("width", 100)
  .attr("height", 100)
  .attr("opacity", "1" );
  
  var queues = [];
  for( var key in data ){
    var queue = new CircularQueue(6); // a circular queue with 3 items
    queue.push("5");
    queue.push("4");
    queue.push("3");
    queue.push("2");
    queue.push("1");
    queue.push("0");        
    queues.push( queue );
  }

  function particle( cx, cy, radius, sort ) {     
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
    var t = setInterval(function(){
      img.attr( "xlink:href", "assets/"+queues[sort].pop() +".png" );
      if ( Date.now() - now > 5250 ){
        clearInterval(t);
      }
    }, speed );
  }
    
  function viewParticle( json ){

    var totalTps = 0;
    for (var inx = 0, len = json.length; inx < len ; inx ++){
      var val = json[inx].V;
      totalTps = plus( totalTps, Number( val ) );
      var sort = sorts[json[inx].H];
      runHorse( val, sort );
      
      if( val != 0 ){           
        // Position X
        var cx = ( sort * cw ) + 85;
        var until = Math.floor( val / 200 ) * 5 ;
        
        var jnx = 1;
        if( until < 1 ){
          until = 1;
        }         
        
        while( jnx <= until ){              
          particle( cx, 130, val * ( jnx * ( 1/until) ), sort );
          jnx++;
        }
      }
    }         
    setTotal( Number(totalTps).toFixed(2) );
  }
  
  function changeRectColor( serviceNm, isActive ){
    var rect = svg.select("#rect_"+serviceNm);
    if( isActive ){
      rect.attr( "fill", "#87B87F" );
    } else {
      rect.attr( "fill", "#ABBAC3" );
    }
  }
  
  function setTotal( totalTps ){
    svg.append("svg:text")
    .attr("fill", "#000")
    .attr("x", 250 )
    .attr("y", 65)
    .attr("text-anchor", "end")
    .style("font-size", "30px")
    .text( totalTps )
    .transition()
    .duration(5200)
    .style("stroke-opacity", 1e-6)
    .remove();
  }
  
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