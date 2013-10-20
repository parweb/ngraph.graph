ngraph.graph
============

Base [graph](http://en.wikipedia.org/wiki/Graph_(mathematics\)) structure in ngraph.X. Library implments API to modify graph structure and supports event-driven notifications when graph changes.

[![build status](https://secure.travis-ci.org/anvaka/ngraph.graph.png)](http://travis-ci.org/anvaka/ngraph.graph)

## Creating a graph
Create a graph with no edges and no nodes:

``` js
var createGraph = require('ngraph.graph');
var g = createGraph();
```

## Growing a graph
The graph `g` can be grown in two ways. You can add one node at a time:

``` js
g.addNode('hello'); 
g.addNode('world'); 
```

Now graph `g` contains two nodes: `hello` and `world`. You can also use `addLink()` method to grow a graph. Calling this method with nodes which are not present in the graph creates them:

``` js
g.addLink('space', 'bar'); // now graph 'g' has two new nodes: 'space' and 'bar'
```

If nodes already present in the graph 'addLink()' makes them connected:

``` js
g.addLink('hello', 'world'); // Only a link between 'hello' and 'bar' is created. No new nodes.
```

### What to use as nodes and edges?
The most common and convenient choices are numbers and strings. You can associate arbitrary data with node via optional second argument of `addNode()` method:

``` js
g.addNode('world', 'custom data'); // Now node 'world' is associated with a string object 'custom data'
```

You can also associate arbitrary object with a link using third optional argument of `addLink()` method:

``` js
g.addLink(1, 2, x); // A link between nodes '1' and '2' is now associated with object 'x'
```

### Enumerating nodes and links
After you created a graph one of the most common things to do is to enumerate its nodes/links to perform an operation.

``` js
g.forEachNode(function(node){
    console.log(node.id, node.data);
});
```

The function takes callback which accepts current node. Node object may contain internal information. `node.id` and `node.data` represent parameters passed to the `g.addNode(id, data)` method and they are guaranteed to be present in future versions of the library.

To enumerate all links in the graph use `forEachLink()` method:

``` js
g.forEachLink(function(link) {
    console.dir(link);
});
```

To enumerate all links for a specific node use `forEachLinkedNode()` method:
``` js
g.forEachLinkedNode('hello', function(linkedNode, link){
    console.log("Connected node: ", linkedNode.id, linkedNode.data); 
    console.dir(link); // link object itself
});
```

This method always enumerates both inbound and outbound links. If you want to get only outbound links, pass third optional argument:
``` js
g.forEachLinkedNode('hello',
    function(linkedNode, link) { /* ... */ },
    true // enumerate only outbound links
  );
```

To get a particular node object use `getNode()` method. E.g.:

``` js
var world = g.getNode('world'); // returns 'world' node
console.log(world.id, world.data);
```

Finally to remove a node or a link from a graph use `removeNode()` or `removeLink()` correspondingly:

``` js
g.removeNode('space');
// Removing link is a bit harder, since method requires actual link object:
g.forEachLinkedNode('hello', function(linkedNode, link){
  g.removeLink(link); 
});
```

## Listening to Events
Whenever someone changes your graph you can listen to notifications:

``` js
g.on('changed', function(changes) {
  console.dir(changes); // prints array of change records
});

g.add(42); // this will trigger 'changed event'
```

Each change record holds information:

```
ChangeRecord = {
  changeType: add|remove|update - describes type of this change
  node: - only present when this record reflects a node change, represents actual node
  link: - only present when this record reflects a link change, represents actual link
}
```

Sometimes it is desirable to react only on bulk changes. ngraph.graph supports this via `beginUpdate()`/`endUpdate()` methods:

``` js
g.beginUpdate();
for(var i = 0; i < 100; ++i) {
  g.addLink(i, i + 1); // no events are triggered here
}
g.endUpdate(); // this triggers all listners of 'changed' event
```

If you want to stop listen to events use `off()` method:
``` js
g.off('changed', yourHandler); // no longer interested in changes from graph
```

For more information about events, please follow to [ngraph.events](https://github.com/anvaka/ngraph.events)

Install
=======

With [npm](http://npmjs.org) do:

```
npm install ngraph.graph
```

### Browser
Get [browserify](http://browserify.org/) if you don't have it yet (first line):

```
npm install -g browserify
npm install ngraph.graph
browserify index.js -s createGraph -o ngraph.graph.js
```

Now you are ready to use ```createGraph()``` within browser:
``` html
<script src="ngraph.graph.js"></script>
```

License
=======
BSD 3-clause
