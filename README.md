**FLO - Distributed Hierarchical Dataflow**

# FLO - Project Overview

Flo is a graphical environment for building and executing hierarchical distributed dataflows.

You are probably already familiar with the concept of a dataflow, even if you haven't come
across the particular term "dataflow" before. The Boolean logic circuit shown below, for example, is a dataflow, because there is data (e.g. boolean values) flowing along wires between
logical processes (e.g. NOT, OR and AND gates):

In general, a dataflow is a collection of any computational processes (not just logic gates),
with inputs and outputs, that can be "wired" together. Just like the circuit diagram above, a
dataflow is a directed graph, with nodes corresponding to the processes and edges representing
the channels (from output to input) along which data flows. From the point of view of the graph,
the nodes are opaque black-boxes: we are only given their name (e.g. "NOT", "OR", "AND") and
their inputs and outputs (collectively called ports) - but we know nothing at all about their internal mechanisms. All we can be sure of, is that whenever a node outputs data, that data is transmitted along the relevant edges to the connected inputs of other nodes.

Since a dataflow is itself a process, a node can recursively encapsulate another nested dataflow, giving an overall hierarchical structure. Also, the processes represented by nodes may be local
(part of the dataflow mechanism per se), or remote (running on some external networked device), so dataflows can be distributed. In particular, nodes can represent embedded IoT devices, with arcs connecting to arbitrary networked computational processes, including other IoT devices.

Flo is both an application for graphically creating and editing hierarchical distributed dataflows, and a runtime dataflow execution environment. Indeed, it is a live-coding environment in which dataflows run continuously even while being modified.

Flo is an application written in Swift. It's application statck comprises the following packages:
* [FloBox](https://github.com/kk-0129/FloBox)
* [FloGraph](https://github.com/kk-0129/FloGraph)
* [Flo2D](https://github.com/kk-0129/Flo2D)
* [FloApp](https://github.com/kk-0129/FloApp)
