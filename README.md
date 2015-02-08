# Embed-API
Docs for JavaScript API to Plotly Embeds

## Synoposis

JavaScipt API to Plotly graph embeds. You can use this to build custom controls that change a graph's type, colors, and data. There are plenty of examples hosted in Codepen below. 

The Embed API is the basis for communication between Plotly graphs and IPython notebook widgets and planned support for RStudio's Shiny package.

If you want to play with the API, the first step is to create an Plotly graph or grab someone else's. There are plenty to chose from on Plotly's feed.

Questions? Twitter @plotlygraphs or support[at]plot[dot]ly. Share with us what you've built.

## restyle

Restyle can change attributes of the data traces in the graph - line colors, and the data itself. 

If you have several traces (several lines for a line chart, a stacked bar chart, etc), you can specify a trace index to target the data trace you want to restyle.

See [Trace objects](https://plot.ly/javascript-graphing-library/reference/#Trace_objects) and [Trace auxilliary objects](https://plot.ly/javascript-graphing-library/reference/#Trace_auxiliary_objects) for an exhaustive list of accepted key-value pairs that can be passed to the restyle task.

You can also inspect the JSON representation of the embedded graph by appending ".json" to any graph URL. The keys that can be changed through the restyle task are in the "data" array. For example, the JSON representation of the the graph with URL [https://plot.ly/~PlotBot/80](https://plot.ly/~PlotBot/80) is [https://plot.ly/~PlotBot/80.json](https://plot.ly/~PlotBot/80.json). There are three traces with data array - they can be restyled separtely by specifying their index (0, 1, or 2) in the postMessage call (see below for syntax).

### Syntax example

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
