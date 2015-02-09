# Embed-API
*JavaScript API to embedded Plotly graphs*

## Overview

Plotly is making its JavaScipt API to Plotly graph embeds available and documented for  users. This API can be used build custom controls that change anything about an embedded Plotly graph - the graph type, colors, data, etc. 

The Embed API is the basis for communication between Plotly graphs and [IPython notebook widgets](http://moderndata.plot.ly/widgets-in-ipython-notebook-and-plotly/), as well as  planned support for [RStudio's Shiny](http://shiny.rstudio.com/) product.

If you want to play with the API, the first step is to create a Plotly graph or use someone else's. There are plenty of graphs with which you can experiment on the [https://plot.ly/feed/](Plotly feed).

Questions? Twitter [@plotlygraphs](https://twitter.com/plotlygraphs) or support[at]plot[dot]ly. Share with us what you've built. [Codepen](http://codepen.io/) is a great resource for messing with the API.

## Plotly's JSON representation

Plotly encodes all graphs into a text-based JSON format from which the graph is drawn and accessed in languages other than JavaScript (Python, MATLAB, R, etc). This is the "DNA" of the graph.

For example, the JSON representation of the the graph with URL [https://plot.ly/~PlotBot/80](https://plot.ly/~PlotBot/80) is [https://plot.ly/~PlotBot/80.json](https://plot.ly/~PlotBot/80.json).

Here's the JSON representation for an even simpler graph, [https://plot.ly/~PlotBot/201](https://plot.ly/~PlotBot/201):

```
{
    "data": [
        {
            "x": [
                1, 
                2, 
                3, 
                4
            ], 
            "y": [
                0, 
                2, 
                3, 
                5
            ], 
            "fill": "tozeroy", 
            "type": "scatter"
        }, 
        {
            "x": [
                1, 
                2, 
                3, 
                4
            ], 
            "y": [
                3, 
                5, 
                1, 
                7
            ], 
            "fill": "tonexty", 
            "type": "scatter"
        }
    ], 
    "layout": {
        "autosize": false, 
        "width": 500, 
        "height": 500, 
        "margin": {
            "l": 65, 
            "r": 50, 
            "b": 65, 
            "t": 65
        }
    }
}
```

If you're curious about the JSON representation for more complicated  Plotly graphs, try the [https://plot.ly/feed/](Plotly feed).

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

<a name="restyle">#</a> task: **restyle**

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

<a name="relayout">#</a> task: **relayout**

The relayout task changes the size, margins, and axes of a graph. See [Plotly's JSON representation](#plotlys-json-representation) above for how to pinpoint these attributes. The keys that can be changed through the relayout task are in the "layout" object. 

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

<a name="hover">#</a> task: **hover**

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
