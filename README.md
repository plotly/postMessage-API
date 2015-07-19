# postMessage API
*JavaScript API to embedded Plotly graphs*

![Image of fractal widget](http://i.imgur.com/almE500.gif)

[JSFiddle here](http://jsfiddle.net/alexcjohnson/kagz3byL/)

![Image of data widget](http://i.imgur.com/XAl83lJ.gif?1)

[Codepen here](http://codepen.io/jackp/pen/ogpGbL?editors=101)

## postMessage API examples

**Mandelbrot explorer**<br />
Binds to zoom event to recalculate Mandelbrot set after zooming-in (click-drag to zoom) <br />
[http://codepen.io/alexcjohnson/full/KwQWYo](http://codepen.io/alexcjohnson/full/KwQWYo)

**IMDB movie explorer**<br />
Interactively explore the IMBD movie database <br />
[http://codepen.io/theengineear/pen/ogEzZO](http://codepen.io/theengineear/pen/ogEzZO)

**Changing colors**<br />
Change the fill color of an area plot <br />
[http://codepen.io/anon/pen/ZYrbwK](http://codepen.io/anon/pen/ZYrbwK)

**Rose plot explorer**<br />
Dynamically change the parameters of a rose plot <br />
[http://codepen.io/etpinard/pen/vEdmgg](http://codepen.io/etpinard/pen/vEdmgg)

**Plotly-Shiny integration**<br />
Integration with the popular Shiny framework for R <br />
[https://github.com/chriddyp/plotly-shiny](https://github.com/chriddyp/plotly-shiny)


## Overview

Plotly's JavaScipt API to graph embeds is now an officially supported feature for advanced users. 

The postMessage API can be used to build custom controls that change anything about an embedded Plotly graph - the graph type, colors, data, etc - *after* the graph is embedded in a webpage. You can even start with a blank Plotly graph, then insert data and style it throught the postMessage API.

The postMessage API is the basis for communication between Plotly graphs and [IPython notebook widgets](http://moderndata.plot.ly/widgets-in-ipython-notebook-and-plotly/), as well as with [RStudio's Shiny](https://github.com/chriddyp/plotly-shiny) product. We're excited to see what else will be built with it.

If you want to play with the API, the first step is to create a Plotly graph or use someone else's, then embed it in [Codepen](http://codepen.io/) or a webpage [as an iframe](https://plot.ly/how-to-embed-plotly-graphs-in-websites/#fallback). There are plenty of graphs with which you can experiment on the [Plotly feed](https://plot.ly/feed/).

Plotly embeds typically have this HTML format:

```
<iframe width="640" height="480" frameborder="0" seamless="seamless" scrolling="no" src="https://plot.ly/~Dreamshot/411.embed?width=640&height=480" ></iframe>
```

Questions? Tweet us at [@plotlygraphs](https://twitter.com/plotlygraphs) or write us at support[at]plot[dot]ly. Share with us what you've built. [Codepen](http://codepen.io/) is a great resource for messing with the API.

## Plotly's JSON representation

Plotly encodes all graphs into a text-based JSON format from which the graph is drawn and accessed in languages other than JavaScript (Python, MATLAB, R, etc). This is the "DNA" of the graph.

For example, the JSON representation of the the graph with URL [https://plot.ly/~PlotBot/80](https://plot.ly/~PlotBot/80) is [https://plot.ly/~PlotBot/80.json](https://plot.ly/~PlotBot/80.json).

You will need to reference the JSON representation in order to pinpoint the aspects of the graph you want to modify through the postMessage API.

If you're curious about the JSON representations of more complicated Plotly graphs, try appending ".json" to the graph URL's on the [Plotly feed](https://plot.ly/feed/).

## Getting Started
[Hello World](https://github.com/plotly/postMessage-API/blob/master/hello_world.html#L52-L117) contains boiler plate code for initalizing postMessage listeners and posting messages. [Interact with the example.](http://help.plot.ly/postMessage-API/)

## Tasks

The postMessage API communicates with embedded graphs via a *task* identifier. Supported tasks are:

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
* task: [newPlot](#newPlot) - idempotent plotting function, replaces existing Plotly plot with new one

<a href="#restyle" name="restyle">#</a> task: **restyle**

The restyle task changes attributes of the graph data - colors, type (ie bars versus points, scatter plot versus histogram), and the even values of the data. See [Plotly's JSON representation](#plotlys-json-representation) above for how to pinpoint these attributes. The keys that can be changed through the restyle task are in the "data" array. 

If you have several traces (several lines for a line chart, a stacked bar chart, etc), you can specify a trace index to target the data trace you want to restyle. 

See [Trace objects](https://plot.ly/javascript-graphing-library/reference/#Trace_objects) and [Trace auxilliary objects](https://plot.ly/javascript-graphing-library/reference/#Trace_auxiliary_objects) for an exhaustive list of accepted key-value pairs that can be passed to the restyle task.

This example changes the y-values data of the second trace to 2 and the color of the points to red.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            'task': 'restyle',
            'update': {'marker.color': 'red', 'y': [[2, 3, 1]]},
            'indices': [1]
        },
        'https://plot.ly');
```

Here's the syntax to update the data in a graph with 2 traces, where X, X1, Y, and Y1 are numeric arrays.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            task:'restyle',
            update:{x:[X,X1],y:[Y,Y1]}
        }, 'https://plot.ly');   
```

<a href="#relayout"name="relayout">#</a> task: **relayout**

The relayout task changes the size, margins, and axes of a graph. See [Plotly's JSON representation](#plotlys-json-representation) above for how to pinpoint these attributes. The keys that can be changed through the relayout task are in the "layout" object. 

See [Layout objects](https://plot.ly/javascript-graphing-library/reference/#Layout_and_layout_style_objects) and [Axes objects](https://plot.ly/javascript-graphing-library/reference/#Axis_objects) for an exhaustive list of accepted key-value pairs that can be passed to the restyle task.

This example changes the width of the graph to 500px and makes sure the legend is displayed.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow to delete traces at indices 0 and 1
plot.postMessage(
    {
        task: 'deleteTraces',
        indices: [0, 1]
    }, 'https://plot.ly');

// send a message to the contentWindow to delete the last trace
plot.postMessage(
    {
        task: 'deleteTraces',
        indices: [-1]
    }, 'https://plot.ly');
```

<a href="#moveTraces" name="moveTraces">#</a> task: **moveTraces**

Change the order of traces in a graph.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow
plot.postMessage(
    {
        task: 'getAttributes',
        attributes: ['layout.title'] }, 'https://plot.ly' );

window.addEventListener('message', function(e) {
    var message = e.data;
    console.log(message);
});
```

Here's the syntax to get x,y data arrays in a graph with 2 traces.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

var X, Y, X1, Y1;

// get current x, y data
plot.postMessage({
    task: 'getAttributes',
    attributes: [
        'data[0].x','data[0].y',
        'data[1].x','data[1].y'
    ] },
    'https://plot.ly/');

window.addEventListener('message', function(e) {
    var message = e.data;
    X = message.response['data[0].x'];
    Y = message.response['data[0].y'];
    X1 = message.response['data[1].x'];
    Y1 = message.response['data[1].y']; 
});
```

<a href="#setAutosize" name="setAutosize">#</a> task: **setAutosize**

If set to true, iframe will resize if placed in a container smaller than the original iframe dimensions.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {task: 'setAutosize', 'value': true}, 'https://plot.ly');
```

<a href="#ping" name="ping">#</a> task: **ping**

The ping task is just used to tell when the listener is active.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

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
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow
plot.postMessage({task: 'redraw'}, 'https://plot.ly');
```

<a href="#newPlot" name="newPlot">#</a> task: **newPlot**

Idempotent plot command to created new plotly Plot with `data` and `layout` attributes.

```javascript
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot').contentWindow;

// send a message to the contentWindow to replace the existing plot
plot.postMessage({
    task: 'newPlot',
    data: [{y: [2, 5, 1, 2], type: 'bar'}, {y: [2, 5, 1, 2], type: 'scatter'}],
    layout: {title: 'new plot'}
}, 'https://plot.ly');
```

