# Embed-API
*JavaScript API to embedded Plotly graphs*

![Image of fractal widget](http://i.imgur.com/almE500.gif)

[JSFiddle here](http://jsfiddle.net/alexcjohnson/kagz3byL/)

## Overview

Plotly's JavaScipt API to graph embeds is now an officially supported feature for advanced users. 

The Embed API can be used to build custom controls that change anything about an embedded Plotly graph - the graph type, colors, data, etc - *after* the graph is embedded in a webpage. You can even start with a blank Plotly graph, then insert data and style it throught the Embed API.

The Embed API is the basis for communication between Plotly graphs and [IPython notebook widgets](http://moderndata.plot.ly/widgets-in-ipython-notebook-and-plotly/), as well as  planned support for [RStudio's Shiny](http://shiny.rstudio.com/) product. We're excited to see what else it built with it.

If you want to play with the API, the first step is to create a Plotly graph or use someone else's. There are plenty of graphs with which you can experiment on the [https://plot.ly/feed/](Plotly feed).

Questions? Twitter [@plotlygraphs](https://twitter.com/plotlygraphs) or support[at]plot[dot]ly. Share with us what you've built. [Codepen](http://codepen.io/) is a great resource for messing with the API.

## Plotly's JSON representation

Plotly encodes all graphs into a text-based JSON format from which the graph is drawn and accessed in languages other than JavaScript (Python, MATLAB, R, etc). This is the "DNA" of the graph.

For example, the JSON representation of the the graph with URL [https://plot.ly/~PlotBot/80](https://plot.ly/~PlotBot/80) is [https://plot.ly/~PlotBot/80.json](https://plot.ly/~PlotBot/80.json).

You will need to reference the JSON representation in order to pinpoint the aspects of the graph you want to modify through the Embed API.

If you're curious about the JSON representations of more complicated Plotly graphs, try appending ".json" to the graph URL's on the [https://plot.ly/feed/](Plotly feed).

## Tasks

The Embed API communicates with embedded graphs via a *task* identifier. Supported tasks are:

* task: [restyle](#restyle) - change attributes of the graph's data
* task: [relayout](#relayout) - change attributes of the graph size, margins, and axes
* task: [hover](#hover) - force a hover tag to display over a specific x,y point on the graph
* task: [listen](#listen) - specify a callback for a single event or group of events (zoom, hover, or click)
* task: [addTraces](#addTraces) - shortcut for adding new traces to the graph
* task: [deleteTraces](#deleteTraces) - shortcut for deleting traces from the graph
* task: [moveTraces](#moveTraces) - change the order of traces in a graph
* task: [getLayout](#getLayout) - return the layout object as JSON for the graph
* task: [getAttributes](#getAttributes) - return a part of the graph's JSON representation by key
* task: [setAutosize](#setAutosize) - does the graph redraw when the window is resized?
* task: [ping](#ping) - returns "pong" if the graph is loaded, just used to tell when the listener is active
* task: [redraw](#redraw) - forces a redraw of the graph

<a href="#restyle" name="restyle">#</a> task: **restyle**

The restyle task changes attributes of the graph data - colors, type (ie bars versus points, scatter plot versus histogram), and the even values of the data. See [Plotly's JSON representation](#plotlys-json-representation) above for how to pinpoint these attributes. The keys that can be changed through the restyle task are in the "data" array. 

If you have several traces (several lines for a line chart, a stacked bar chart, etc), you can specify a trace index to target the data trace you want to restyle. 

See [Trace objects](https://plot.ly/javascript-graphing-library/reference/#Trace_objects) and [Trace auxilliary objects](https://plot.ly/javascript-graphing-library/reference/#Trace_auxiliary_objects) for an exhaustive list of accepted key-value pairs that can be passed to the restyle task.

This example changes the y-values data of the second trace to 2 and the color of the points to red.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            'task': 'restyle',
            'update': {'marker.color': 'red', 'y': [[2, 3, 1]]},
            'indices': [1]
        },
        'https://plot.ly');
```

<a href="#relayout"name="relayout">#</a> task: **relayout**

The relayout task changes the size, margins, and axes of a graph. See [Plotly's JSON representation](#plotlys-json-representation) above for how to pinpoint these attributes. The keys that can be changed through the relayout task are in the "layout" object. 

See [Layout objects](https://plot.ly/javascript-graphing-library/reference/#Layout_and_layout_style_objects) and [Axes objects](https://plot.ly/javascript-graphing-library/reference/#Axis_objects) for an exhaustive list of accepted key-value pairs that can be passed to the restyle task.

This example changes the width of the graph to 500px and makes sure the legend is displayed.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            'task': 'relayout',
            'update': {'width': '500', 'showlegend': True},
        },
        'https://plot.ly');
```

<a href="#hover" name="hover">#</a> task: **hover**

Force a hover tag to display over a specific x,y point on the graph.

This example forces a hover tag to display at point (1,2) on the graph.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            'task': 'hover',
            'selection': {xval: 1, yval: 2},
            // 'subplot': 'xy' optional: for graphs with subplots, specify which subplot
        },
        'https://plot.ly');
```

<a href="#hover" name="hover">#</a> task: **hover**

Force a hover tag to display over a specific x,y point on the graph.

This example forces a hover tag to display at point (1,2) on the graph.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            'task': 'hover',
            'selection': {xval: 1, yval: 2},
            // 'subplot': 'xy' optional: for graphs with subplots, specify which subplot
        },
        'https://plot.ly');
```

<a href="#listen" name="listen">#</a> task: **listen**

Add a listener to a single event or group of events (click, hover, or zoom).

This example listens for a click, hover, or zoom then prints the event object in the browser console.
```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'listen',
        events: ['zoom','click','hover']
    }, 'https://plot.ly');

window.addEventListener('message', function(e) {
    var message = e.data;
    alert(message.type);
    console.log(message); // prints object for zoom, click, or hover event
});
```

<a href="#addTraces" name="addTraces">#</a> task: **addTraces**

Overwrites or adds data traces to a graph.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'addTraces',
        traces: [{y: [2,1,2]}, {y: [2,4,5]}],
        newIndices: [0, -1]
    }, 'https://plot.ly');
```

<a href="#deleteTraces" name="deleteTraces">#</a> task: **deleteTraces**

Removes traces from a graph by the trace index.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'deleteTraces',
        indices: [0, 1]
    }, 'https://plot.ly');
```

<a href="#moveTraces" name="moveTraces">#</a> task: **moveTraces**

Change the order of traces in a graph.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'moveTraces',
        currentIndices: [0, 1],
        newIndices: [1, 0]
    }, 'https://plot.ly');
```

<a href="#getLayout" name="getLayout">#</a> task: **getLayout**

Get the full layout object of the graph (see [Plotly's JSON representation](#plotlys-json-representation) above).

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'getLayout',
    }, 'https://plot.ly');

window.addEventListener('message', function(e) {
    var message = e.data;
    console.log(message);
});
```

<a href="#getAttributes" name="getAttributes">#</a> task: **getAttributes**

Return a specific attribute from the top-level layout or data objects (see [Plotly's JSON representation](#plotlys-json-representation) above).

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'getAttributes',
    },  attributes: ['layout.title']

window.addEventListener('message', function(e) {
    var message = e.data;
    console.log(message);
});
```

<a href="#setAutosize" name="setAutosize">#</a> task: **setAutosize**

If set to true, iframe will resize if placed in a container smaller than the original iframe dimensions.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
        { task: 'setAutosize', 'value': true } );
```

<a href="#ping" name="ping">#</a> task: **ping**

The ping task is just used to tell when the listener is active.

```
var pinger = setInterval(function(){
    plot.postMessage({task: 'ping'}, 'https://plot.ly')
}, 100);

window.addEventListener('message', function(e) {
    var message = e.data;
    if(message.pong) {
        console.log('Initial pong, frame is ready to receive');
        clearInterval(pinger);
        // Do some stuff, iframe is now ready to receive tasks
    }
});
```

<a href="#redraw" name="redraw">#</a> task: **redraw**

Force a redraw of the graph contents.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage( { task: 'redraw' } );
```

