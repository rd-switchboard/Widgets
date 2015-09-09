# RD-Swicthboard Graph Widget

### Requiriements

RD-Swicthboard Graph Widget require [D3 Java Scripr Library](http://d3js.org/) (3.5.5 or better). [jQuery Library](https://jquery.com/) can also be used to initalize a widget. 

Program has been tested on Ubuntu Linux 14.04 and should work on any other linux as well.

### Structure

RD-Swicthboard Graph Widget consists from a several files:

* graph_widget/bower.json : Bower file, enabling automatic download of D3 and jQuery libraries
* graph_widget/demo.html : Demo web page
* graph_widget/assets/js/graph_widget.js : main Java Script file
* graph_widget/assets/css/graph_widget.css : widget CSS file
* graph_widget/assets/images/ : images, used by widget.

### Installiation

The current widget installiation will require [Node.Js](https://nodejs.org/en/) [NPM](https://www.npmjs.com/) (2.13.2 or better) and [Bower](http://bower.io/) (1.4.1 or better) to be installed locally. The Bower will be only used to install D3 and jQuery libraries,  so you are free to use any other installiation method, you would preffer.

To install widget on any Ubutu-like Linux via bower, copy the widget `assests` folder and `bower.json` file into location where you wish it to use, then execute from that location:

```
# Install Node.js and npm:
sudo apt-get install npm

# Make sure you have got the latest npm version:
sudo npm install -g npm

# Install Bower globally:
sudo npm install -g bower

# Install depending packages (D3 and jQuery)
bower install
```

After that, Bower will create new folder `bower_components` where D3 and jQuery library will be located.

### Ussage

To use the widget, you must first load D3 library, Widget CSS and JS files into browser. The D3 library must be loaded first. The graph will never use jQuery library, but you can load it as well to have access for jQuery `$` functions. You will also need to create special `div` element with `graph` class. This div will be used to display the graph. Last, you will need to make a call to the graph_init function providing request and access keys. A demo.html file has been provided to illustrate how to do that:

```html
<html><head>

<link rel="stylesheet" type="text/css" href="assets/css/graph_widget.css">

</head><body>

<script src="bower_components/d3/d3.min.js"></script>
<script src="bower_components/jquery/dist/jquery.min.js"></script>
<script src="assets/js/graph_widget.js"></script>

<script>
$(document).ready(function() {
    var reqkey = "2007-08-voyage-mineralogy-biota-433724"
    var accesskey = "demo"

    graph_init(reqkey, accesskey);
});

</script>

<div class="graph" style="visibility: visible;"/>

</body></html>
```

The request key is an actual record key and the access key is a RD-Swicthboard API key assingned to you. You can apply for this key via [reqistration page](http://rd-switchboard.net/api/registration/).

For a demonstration and a testing purposes, a special `demo` key has been created. This key will let you to browser only one graph, accesible via `2007-08-voyage-mineralogy-biota-433724` request key.

### Graph Class:

The Graph class will be part of D3 object and can be used directly. `graph_init` function will setup and enable Graph object. To get access to a Graph JSON object you will need to construct a request for RD-Swicthboard Graph API and pass reqkey and accesskey as a parameters. You can use d3.json() function to load the graph JSON. The graph class can be accessed via d3.graph() function.

```javascript
// construct the request for RD-Swicthboard Graph API 
var jsonName = "http://rd-switchboard.net/api/graph/?reqkey=" + reqkey + "&accesskey=" + accesskey;

// load the JSON
d3.json(jsonName, function(error, json) {
    // check for possible errors
    if (null == error) {
        // The graph can be displayed
	// Create graph object
	var graph = d3.graph()
	// configure graph object if needed
        // display the graph in the div with 'graph' class
        d3.select(".graph")
            .style("visibility", "visible")
            .call(graph);
        graph.load(json).update();
    } else {
        // The graph can not be displayed  
        // display error message in the div with 'graph' class
        d3.select(".graph")
            .attr("class", "graph-void")
            .style("visibility", "visible")
            .append("span")
            .text("Switchboard Widget: This collection has no related party record, grant or connected dataset.");
    }
);

```

### Graph Object functions

#### graph.width()

Gets or sets the width of the Graph. The default width will be equals to the width of the provided div.

```java script
var w = graph.width() // to get the graph width
graph.width(w * 2) // to set the graph width

```

#### graph.height()

Gets or sets the height of the Graph. The default height will be equals to the width of the provided div.

```java script
var h = graph.height() // to get the graph height
graph.height(h * 2) // to set the graph height

```

#### graph.imagePath()

Gets or sets the path to the folder with ghraph images. The default path is `assets/images/`.

```java script
var p = graph.imagePath() // to get the graph image path
graph.imagePath(myPath) // to set the graph image path

```

#### graph.load()

Load the graph data from a provided JSON file

```java script
graph.load(json)
```

#### graph.update()

Start graph upadte process. This function will need to call only once. If any node will be moved by user, the process will be resumed automatically.

```java script
graph.update()
```

### JSON format

The JSON object must have specific format. It should have two arrays of objects, one for nodes and one for relationships:

```javascript
{
    "nodes": [
	// Nodes array
    ],
    "relationships": [	
	// Relationships array
    ]
}

```

The Node object must have integer node id, node type, map object, containing node properties and optional array with extra node information:

```javascript
{
    "id": 1 , // Node ID
    "type": "dataset", // Node type. The possible options are: "institution", "researcher", "grant", "dataset" or "publication
    "extras": [ "incomplete" ], // Optional array with extra information. The "root" will indicate root node and "incomplete" will indicate a node with incomplete information
    "properties": { // Object with node properties. The required properties are: 
         "key": // Node resource URL
         "node_source": // Source of the node 
         "node_type": // Type of the node, at this moment it will match actual type of the node
         "title": // The title of the node
    }
}

```

The Relationship object must have integer relationsip id, relationship type, and id of start and end nodes:

```javascript
{
    "id": 1, // Relationship ID
    "type": "relatedTo", // Relationship type
    "from": 1, // Start node id
    "to": 2, // End node id
}

```

