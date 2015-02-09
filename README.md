# Embed-API
Docs for JavaScript API to Plotly Embeds

## Synoposis

For the first time, Plotly is making its JavaScipt API to Plotly graph embeds availlable and documented for the public. You can use this API to build custom controls that change anything about an embedded Plotly graph - the graph type, colors, data, etc. 

The Embed API is the basis for communication between Plotly graphs and [IPython notebook widgets](http://moderndata.plot.ly/widgets-in-ipython-notebook-and-plotly/), as well as  planned support for [RStudio's Shiny](http://shiny.rstudio.com/) product.

If you want to play with the API, the first step is to create an Plotly graph or grab someone else's. There are plenty to chose from on the [https://plot.ly/feed/](Plotly feed).

The Embed API communicates with embedded graphs via a *task* identifier. Allowed tasks are:

* **restyle** change attributes of the graph's data
* **relayout** change attributes of the graph size, margins, and axes
* **hover** force a hover tag to display over a specific x,y point on the graph
* **listen** specify a callback for a single event or group of events (zoom, hover, or click)
* **addTraces** shortcut for adding new traces to the graph
* **deleteTraces** shortcut for deleting traces from the graph
* **moveTraces** change the order of traces in a graph
* **getLayout** return the layout object as JSON for the graph
* **getAttributes** return a part of the graph's JSON representation by key
* **setAutosize** does the graph redraw when the window is resized?
* **ping** returns "pong" if the graph is loaded, just used to tell when the listener is active
* **redraw** forces a redraw of the graph

Questions? Twitter [@plotlygraphs](https://twitter.com/plotlygraphs) or support[at]plot[dot]ly. Share with us what you've built. [Codepen](http://codepen.io/) is a great resource for messing with the API.

## A bit about Plotly's JSON representation

Plotly encodes all graphs into a text-based JSON format from which the graph is drawn and accessed in languages other than JavaScript (Python, MATLAB, R, etc). This is the "DNA" of the graph.

You can  inspect the JSON representation of an embedded Plotly graph by appending ".json" to any graph URL. The keys that can be changed through the restyle task are in the "data" array. 

For example, the JSON representation of the the graph with URL [https://plot.ly/~PlotBot/80](https://plot.ly/~PlotBot/80) is [https://plot.ly/~PlotBot/80.json](https://plot.ly/~PlotBot/80.json). 

There are three traces within the data array in this graph - they can be restyled separately by specifying their index (0, 1, or 2) when calling an Embed API task.

If you're curious about the JSON representation of other Plotly graphs, try the [https://plot.ly/feed/](Plotly feed).

See [Trace objects](https://plot.ly/javascript-graphing-library/reference/#Trace_objects) and [Trace auxilliary objects](https://plot.ly/javascript-graphing-library/reference/#Trace_auxiliary_objects) for an exhaustive list of accepted key-value pairs that can be passed to the restyle task.

## restyle task

The restyle task changes attributes of the graph data - colors, type (ie bars versus points, scatter plot versus histogram), and the even values of the data. See **A bit about Plotly's JSON representation** above for how to pinpoint these attributes.

If you have several traces (several lines for a line chart, a stacked bar chart, etc), you can specify a trace index to target the data trace you want to restyle. 

### Syntax

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

## restyle task

The restyle task changes attributes of the graph data - colors, type (ie bars versus points, scatter plot versus histogram), and the even values of the data. See **A bit about Plotly's JSON representation** above for how to pinpoint these attributes.

If you have several traces (several lines for a line chart, a stacked bar chart, etc), specify an index to target the data trace you want to restyle. 

### Syntax

This example changes the y-values of the second trace to [2,3,1] and the color of the points to red.

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

## relayout task

The relayout task changes the size, margins, and axes of a graph. See **A bit about Plotly's JSON representation** above for how to pinpoint these attributes.

### Syntax

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

## hover task

Force a hover tag to display over a specific x,y point on the graph.

### Syntax

This example forces a hover tag to display at point (1,2) on the graph.

```
// Grab the embed's contentWindow by the iframe id
var plot = document.getElementById('plot')[0].contentWindow;

// send a message to the contentWindow
plot.postMessage(
        {
            'task': 'hover',
            'selection': {xval: 1, yval: 2},
            'subplot': 'xy' // optional: for graphs with subplots, specify which subplot
        },
        'https://plot.ly');
```
