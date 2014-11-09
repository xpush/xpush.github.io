---
layout: doc
title: Example of dashboard
date: April 31, 2014
---

## Dashboard Example

Let's implement a simple dashboard.

full source can be found in [here](https://github.com/xpush/lib-xpush-web/blob/master/example/dashboard.html).

Since XPUSH servers used in the demo below is a temporary test servers provided by XPUSH team, we can't guarantee performance and iIt may be temporarily unavailable. Therefore, please use the XPUSH you install it yourself.

### script import

Include js files : jquery, d3.js, google jsapi, xpush.js

<pre data-lang="javascript">
<code class="prettyprint">&lt;script type="text/javascript" src="https://www.google.com/jsapi"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="http://mbostock.github.com/d3/d3.js"&gt;&lt;/script&gt;

&lt;script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"&gt;&lt;/script&gt;
&lt;script src="http://xpush.github.io/lib/dist/xpush.js"&gt;&lt;/script&gt;
</code>
</pre>

### script code

Create an option for drawing and dataTable for google chart, and initialize the chart.

<pre data-lang="javascript">
<code class="prettyprint">// import corechart for CPU Stats, import gauge for Memory Stats
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

  // function for timestamp ( hh:min:ss )
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

  // function for M, K
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

  // function for M, K
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

  // Defines the functions of the circular queue.
  var CircularQueueItem = function (value, next, back) {
    this.next = next;
    this.value = value;
    this.back = back;
    return this;
  };

  // Defines the functions for the circular queue.
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

  // Draw the google chart
  function initGoogleChart() {

    // Defines the data type of the cpu gauge chart.
    cpuDataTable = google.visualization.arrayToDataTable([
      ['Label', 'Value'],
      ['stalk-front-c01', 0],
      ['stalk-front-c02', 0]
    ]);

    // Defines the data type of the memory line
    memDataTable = new google.visualization.DataTable();

    memDataTable.addColumn('string', 'Time');
    memDataTable.addColumn('number', 'stalk-front-c01');
    memDataTable.addColumn('number', 'stalk-front-c02');

    // Draw a gauge chart in chart_cpu area.
    cpuChart = new google.visualization.Gauge(document.getElementById('chart_cpu'));
    cpuChart.draw(cpuDataTable, options);

    // Draw a gauge chart in chart_mem area.
    memChart = new google.visualization.LineChart(document.getElementById('chart_mem'));
    memChart.draw(memDataTable, memOptions);

    // redraws memChart when window size is changed
    window.onresize = function(){
      memChart.draw(memDataTable, memOptions);
    };
  }
</code>
</pre>

Use D3 to draw the area for running horses. Horses will be run by changing the image source.

<pre data-lang="javascript">
<code class="prettyprint">// Set the serverlist as a variable.
  var hostNames = ["stalk-front-c01","stalk-front-c02"];
  var sorts = {"stalk-front-c01":0, "stalk-front-c02":1 };
  var serviceNames = ["server01", "server02"];

  var svg;
  var queues = [];
  var cw, z, w, h;

  // Initialize the horse chart using SVG.
  function initHorseChart(){
    w = 400,
    h = 250,
    cw = 200,
    z = d3.scale.category10();

    // Draw svg
    svg = d3.select("#transaction").append("svg:svg")
    .attr("width", w)
    .attr("height", h);
    
    var data = [0,1];

    // Draw a rectangle to show the server name.
    var sel = svg.selectAll("rect").data(data).enter();
    sel.append("rect")
    .attr("id", function(d) {return "rect_"+serviceNames[d];})
    .attr("fill", "white" )
    .attr("stroke", "#999")
    .attr("width", 100)
    .attr("height", 40)
    .attr("x", function(d) {return ( d * cw ) + 50;} )
    .attr("y", 210);
    
    // shows the server name to TEXT form.
    sel.append("svg:text")
    .attr("fill", "black")
    .attr("x", function(d) {return ( d * cw ) + 100;})
    .attr("y", 235)
    .attr("text-anchor", "middle")
    .style("font-size", "12px")
    .text(function(d) {return hostNames[d]});
    
    // Add the horse image.
    sel.append("image")
    .attr("id", function(d) {return "image"+d;})
    .attr( "xlink:href", "assets/0.png" )
    .attr("x", function(d) {return ( d * cw ) + 55;} )
    .attr("y", 80 )
    .attr("width", 100)
    .attr("height", 100)
    .attr("opacity", "1" );

    // Creates a Circle Queue.
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

Initialize the xpush object and registers the event listener to draw chart

<pre data-lang="javascript">
<code class="prettyprint">var mem01;
  var mem02;
  var memFormat;
  var xpush;

  var u01 = 0;
  var u02 = 0;

  // Initialize the xpush object and registers the event listener
  function initXpush(){
    xpush = new XPush('http://demo.stalk.io:8000', 'demo');
    xpush.createSimpleChannel('channel-dashboard', function(){
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      // receive incoming data is output to the screen in `message` event.
      xpush.on( 'message', function(channel, name, data){

        if( data.type && data.type == 'db' ) {
          drawTopSite( data.json );
        } else if ( data.type && data.type == 'sys' ) {
          cpuDataTable.setValue(sorts[data.json.H], 1, Math.round(data.json.C));
          cpuChart.draw(cpuDataTable, options);

          if( 0 == sorts[data.json.H] ){
            // Change the memory variables.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem01 = getNumberFormat( Number( data.json.M ), memFormat);

            // Change the user variables.
            u01 = data.json.U;
          } else if( 1 == sorts[data.json.H]  ){
            // Change the memory variables.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem02 = getNumberFormat( Number( data.json.M ), memFormat);

            // Change the user variables.
            u02 = data.json.U;
          }
        }
      });
    });
  };

  var startFlag = false;
  var monitor;
  var btnToggle;

  // Init horse chart and xpush
  $(document).ready( function(){
    initHorseChart();
    initXpush();
    btnToggle = $( "#btnToggle" );
  });
</code>
</pre>

Declare the function to update each chart. We can change the horse's running speed by adjusting the speed of the interval, because the image changes depending on the value.

<pre data-lang="javascript">
<code class="prettyprint">

  // Draw a memory line chart.
  var drawMemChart = function(){

    // Draw a chart every 5 seconds.
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

  // Turns on or off the monitoring function.
  var toggleMonitor = function(){
    if( !startFlag  ){
      // Sends the message to the server informing the monitoring begins.
      xpush.send('channel-dashboard', 'message', 'START_MONITOR' );
      startFlag = true;
      btnToggle.removeClass( "btn-primary" ).addClass( "btn-danger" );
      btnToggle.html( "Off" );
      drawMemChart();

      $( "#site_body" ).hide();
      $( "#site_list" ).show();
    } else {
      // Sends the message to the server informing the monitoring ends.
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

  // Draw a concentric circle every five seconds.
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
    // Copy the template to create a new DOM object
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

        // Modify the newly created DOM object.
        newSite.attr( "id", "template_"+ liId );
        newSite.children( "span:first" ).text( data.C );
        newSite.children( "span:last" ).text( data.S );

        // Add the newly created DOM object to ul DOM.
        newSite.appendTo( "#site_list" );
        newSite.show();
      }
    }
  };
 
  // The horses ran.
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
    
    // Adjust the right running cycle.
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
    // Convert the image reach cycle to run
    var t = setInterval(function(){
      img.attr( "xlink:href", "assets/"+queues[sort].pop() +".png" );
      if ( Date.now() - now > 5250 ){
        clearInterval(t);
      }
    }, speed );
  }
  
  // Draw concentric zones.
  function drawSysChart( json ){

    var totalUsers = 0;
    for (var inx = 0, len = json.length; inx < len ; inx ++){
      var val = json[inx].V;
      totalUsers = plus( totalUsers, Number( val ) );
      var sort = sorts[json[inx].H];

      // The horse ran with concentric circles.
      runHorse( val, sort );
      
      if( val != 0 ){
        // Position X
        var cx = ( sort * cw ) + 85;

       	// Calculate the number of concentric circles.
        var until = Math.floor( val / 200 ) * 5 ;
        
        var jnx = 1;
        if( until < 1 ){
          until = 1;
        }         
        
        // Draw a number of concentric circles as
        while( jnx <= until ){              
          drawParticle( cx, 130, val * ( jnx * ( 1/until) ), sort );
          jnx++;
        }
      }
    }
    setTotal( Number(totalUsers).toFixed(2) );
  }
  
  // Draw a total area
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
  
  // function for float
  function plus() {
    var argsLength = arguments.length;
     
    if(argsLength == 0) return 0;
     
    var result = 0.0;  
    var argString = "";
    var pointIndex = 0;
    var decimalSize = 0;
    var maxDecimalSize = 0;
    var maxPower = 1.0;
     
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
              &lt;h2&gt;Click on button to view&lt;/h2&gt;
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

### Code Result

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
  // import corechart for CPU Stats, import gauge for Memory Stats
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

  // function for timestamp ( hh:min:ss )
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

  // function for M, K
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

  // function for M, K
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

  // Defines the functions of the circular queue.
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

  // Draw the google chart
  function initGoogleChart() {

    // Defines the data type of the cpu gauge chart.
    cpuDataTable = google.visualization.arrayToDataTable([
      ['Label', 'Value'],
      ['stalk-front-c01', 0],
      ['stalk-front-c02', 0]
    ]);

    // Defines the data type of the memory line
    memDataTable = new google.visualization.DataTable();

    memDataTable.addColumn('string', 'Time');
    memDataTable.addColumn('number', 'stalk-front-c01');
    memDataTable.addColumn('number', 'stalk-front-c02');

    // Draw a gauge chart in chart_cpu area.
    cpuChart = new google.visualization.Gauge(document.getElementById('chart_cpu'));
    cpuChart.draw(cpuDataTable, options);

    // Draw a gauge chart in chart_mem area.
    memChart = new google.visualization.LineChart(document.getElementById('chart_mem'));
    memChart.draw(memDataTable, memOptions);


    // redraw memChart when window size is changed
    window.onresize = function(){
      memChart.draw(memDataTable, memOptions);
    };
  }

  // Set the serverlist as a variable.
  var hostNames = ["stalk-front-c01","stalk-front-c02"];
  var sorts = {"stalk-front-c01":0, "stalk-front-c02":1 };
  var serviceNames = ["server01", "server02"];

  var svg;
  var queues = [];
  var cw, z, w, h;

  // Initialize the horse chart using SVG.
  function initHorseChart(){
    w = 400,
    h = 250,
    cw = 200,
    z = d3.scale.category10();

    // Draw the svg
    svg = d3.select("#transaction").append("svg:svg")
    .attr("width", w)
    .attr("height", h);
    
    var data = [0,1];

    // Draw a rectangle to show the server name.
    var sel = svg.selectAll("rect").data(data).enter();
    sel.append("rect")
    .attr("id", function(d) {return "rect_"+serviceNames[d];})
    .attr("fill", "white" )
    .attr("stroke", "#999")
    .attr("width", 100)
    .attr("height", 40)
    .attr("x", function(d) {return ( d * cw ) + 50;} )
    .attr("y", 210);
    
    // shows the server name to TEXT form.
    sel.append("svg:text")
    .attr("fill", "black")
    .attr("x", function(d) {return ( d * cw ) + 100;})
    .attr("y", 235)
    .attr("text-anchor", "middle")
    .style("font-size", "12px")
    .text(function(d) {return hostNames[d]});
    
    // Add the horse image.
    sel.append("image")
    .attr("id", function(d) {return "image"+d;})
    .attr( "xlink:href", "assets/0.png" )
    .attr("x", function(d) {return ( d * cw ) + 55;} )
    .attr("y", 80 )
    .attr("width", 100)
    .attr("height", 100)
    .attr("opacity", "1" );

    // Create a Circle Queue.
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

  // Initialize the xpush object and registers the event listener to draw chart
  function initXpush(){
    xpush = new XPush('http://demo.stalk.io:8000', 'demo');
    xpush.createSimpleChannel('channel-dashboard', function(){
      xpush.send('channel-dashboard', 'message', 'STOP_MONITOR' );
      // receive incoming data is output to the screen in `message` event.
      xpush.on( 'message', function(channel, name, data){

        if( data.type && data.type == 'db' ) {
          drawTopSite( data.json );
        } else if ( data.type && data.type == 'sys' ) {
          cpuDataTable.setValue(sorts[data.json.H], 1, Math.round(data.json.C));
          cpuChart.draw(cpuDataTable, options);

          if( 0 == sorts[data.json.H] ){
            // Change the memory variables.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem01 = getNumberFormat( Number( data.json.M ), memFormat);

            // Change the user variables.
            u01 = data.json.U;
          } else if( 1 == sorts[data.json.H]  ){
            // Change the memory variables.
            memFormat = getNumberFormatString( Number( data.json.M ), false );
            mem02 = getNumberFormat( Number( data.json.M ), memFormat);

            // Change the user variables.
            u02 = data.json.U;
          }
        }
      });
    });
  };

  var startFlag = false;
  var monitor;
  var btnToggle;

  // Init horse chart and xpush
  $(document).ready( function(){
    initHorseChart();
    initXpush();
    btnToggle = $( "#btnToggle" );
  });

  // Draw a memory line chart.
  var drawMemChart = function(){

    // Draw a chart every 5 seconds.
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

  // Turns on or off the monitoring function.
  var toggleMonitor = function(){
    if( !startFlag  ){
      // Sends the message to the server informing the monitoring begins.
      xpush.send('channel-dashboard', 'message', 'START_MONITOR' );
      startFlag = true;
      console.log( btnToggle.length );
      btnToggle.removeClass( "btn-primary" ).addClass( "btn-danger" );
      btnToggle.html( "Off" );
      drawMemChart();

      $( "#site_body" ).hide();
      $( "#site_list" ).show();
    } else {
      // Sends the message to the server informing the monitoring ends.
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

  // Draw a concentric circle every five seconds.
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
    // Copy the template to create a new DOM object
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

        // Modify the newly created DOM object.
        newSite.attr( "id", "template_"+ liId );
        newSite.children( "span:first" ).text( data.C );
        newSite.children( "span:last" ).text( data.S );

        // Add the newly created DOM object to ul DOM.
        newSite.appendTo( "#site_list" );
        newSite.show();
      }
    }
  };
 
  // The horses ran.
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
    
    // Adjust the right running cycle.
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
    // Convert the image reach cycle to run
    var t = setInterval(function(){
      img.attr( "xlink:href", "assets/"+queues[sort].pop() +".png" );
      if ( Date.now() - now > 5250 ){
        clearInterval(t);
      }
    }, speed );
  }
  
  // Draw concentric zones.
  function drawSysChart( json ){

    var totalUsers = 0;
    for (var inx = 0, len = json.length; inx < len ; inx ++){
      var val = json[inx].V;
      totalUsers = plus( totalUsers, Number( val ) );
      var sort = sorts[json[inx].H];

      // The horse ran with concentric circles.
      runHorse( val, sort );
      
      if( val != 0 ){
        // Position X
        var cx = ( sort * cw ) + 85;

        // Calculate the number of concentric circles.
        var until = Math.floor( val / 200 ) * 5 ;
        
        var jnx = 1;
        if( until < 1 ){
          until = 1;
        }         
        
        // Draw a number of concentric circles
        while( jnx <= until ){              
          drawParticle( cx, 130, val * ( jnx * ( 1/until) ), sort );
          jnx++;
        }
      }
    }
    setTotal( Number(totalUsers).toFixed(2) );
  }
  
  // Draw a total area
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
  
  // function for float 
  function plus() {
    var argsLength = arguments.length;
     
    if(argsLength == 0) return 0;
     
    var result = 0.0;  
    var argString = "";
    var pointIndex = 0;
    var decimalSize = 0;
    var maxDecimalSize = 0;
    var maxPower = 1.0;
     
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
              <h2>Click on button to view</h2>
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
