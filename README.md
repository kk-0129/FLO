**FLO - Distributed Hierarchical Dataflow**

# FLO - Project Overview

Flo is a graphical environment for building and executing hierarchical distributed dataflows.
It is written in Swift for [macOS](https://en.wikipedia.org/wiki/MacOS), and comprises the following packages:
* [FloBox](https://github.com/kk-0129/FloBox)
* [FloGraph](https://github.com/kk-0129/FloGraph)
* [Flo2D](https://github.com/kk-0129/Flo2D)
* [FloApp](https://github.com/kk-0129/FloApp)

A [dataflow](https://en.wikipedia.org/wiki/Dataflow) is a collection of arbitrary computational processes (nodes),
with various inputs and outputs that can be "wired" together, with data flowing along these wires (arcs). 
Since a dataflow is itself a process, each node can itself be a dataflow, giving an overall **hierarchical** structure. 
The processes may be local or remote (running on some external networked device), so dataflows can also be **distributed**. 

Flo is both an application for graphically creating and editing these hierarchical distributed dataflows, and a runtime live-coding execution environment for dataflows.

(a video to show you what FLO looks like and some of the things it can do will be added shortly)
