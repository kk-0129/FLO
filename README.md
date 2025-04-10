**FLO - Distributed Hierarchical Dataflow**

# FLO - Project Overview

Flo is a graphical environment for building and executing hierarchical distributed dataflows.
It is written in Swift for [macOS](https://en.wikipedia.org/wiki/MacOS), and comprises the following packages:
* [FloBox](https://github.com/kk-0129/FloBox)
* [FloGraph](https://github.com/kk-0129/FloGraph)
* [Flo2D](https://github.com/kk-0129/Flo2D)
* [FloApp](https://github.com/kk-0129/FloApp)

Flo is both an application for graphically creating and editing these hierarchical distributed dataflows, and a runtime live-coding execution environment for dataflows.

* Installation details are provided [here](TODO)

## What is a Dataflow?

A [dataflow](https://en.wikipedia.org/wiki/Dataflow) is a collection of arbitrary [black box](https://en.wikipedia.org/wiki/Black_box) computational processes, with various inputs and outputs that can be "wired" together, with data flowing along these wires. Since a dataflow is itself a process, each node can also be a dataflow, giving an overall **hierarchical** structure. The processes may be local or remote (running on some external networked device), so dataflows can also be **distributed**. 

In Flo, the [black box](https://en.wikipedia.org/wiki/Black_box) processes are referred to as **boxes**, and the wires along which data flows between boxes are called **arcs**. Flo supports 6
types of data, and 10 kinds of box (4 user-defined, and 6 built-in "widgets") - all described below.

### Data

Flo defines 4 primitive data-types:
* `BOOL` : for Boolean (true or false) values
* `FLOAT` : for numeric values
* `STRING` : for unicode character sequences
* `DATA` : an opaque (i.e. uninterpreted) sequence of bytes

Flo also permits arbitrary user-defined 'struct' types:
<ul>
<li>A <code>STRUCT</code> type is specified as a (key-value) dictionary of named types. 
  <ul>
  <li>As example, Flo has a built-in Date type, to represent a point in time, which is defined as:

<pre><code>Date = STRUCT{
    year: Float, month: Float, day: Float,
    hour: Float, min: Float, sec: Float
}
</code></pre>
  </li>
  <li>An instance of a <code>STRUCT</code> type is a dictionary with the same keys, but with the type names
replaced by values. So, for example, the time 11:53.04 on the 2nd Nov in the year 2023, is
represented by:
<pre><code>Date{
    year = 2023, month = 11, day = 2,
    hour = 11, min = 53, sec = 4
}
</code></pre>
  </li>
  <li>Flo has 4 other built-in <code>STRUCT</code> types for representing 2D & 3D Euclidean coordinates, Euler angles (pitch, yaw & roll) and quaternions (specified by angle & axis):
<pre><code>XY = STRUCT{ x: Float, y: Float }
XYZ = STRUCT{ x: Float, y: Float, y: Float }
Euler = STRUCT{ pitch: Float, yaw: Float, roll: Float }
Quat = STRUCT{ angle: Float, axis: XYZ }
</code></pre>
  </li>
  <li>Users can define new <code>STRUCT</code> types as they need, and the Flo APIs provide
methods to register, discover and instantiate <code>STRUCT</code> types. The system also automatically detects and registers any novel <code>STRUCT</code> types as they are instantiated.
  </li>
  </ul>
</li>
</ul>
Flo also allows arrays of any of the above:
<ul>
<li><code>ARRAY</code>: an array of a specific primitive (<code>BOOL</code>, <code>FLOAT</code> or <code>STRING</code>) or <code>STRUCT</code> type
  <ul>
  <li>e.g. an array of <code>BOOL</code>, which we will write as <code>[BOOL]</code></li>
  <li>Arrays of arrays are not supported, but arrays can be used in <code>STRUCT</code>
types, and so nested indirectly</li>
  </li>
  </ul>
</li>
</ul>

### User-Defined Boxes

User defined processes in Flo are either:
* Local nested dataflows, represented by a "graph" box,
* Remote processes accessed over the network, and represented locally by a "proxy" box.

#### Graph Box (Nested Dataflow)

A graph box (encapsulating a local nested dataflow), is represented graphically by a coloured rectangle which has a name and various named inputs and outputs. The example in the figure below has the name "A Nested Dataflow", with 2 inputs (named "Input1" and "Input2"), and 2 outputs (named "Output1" and "Output2"), collectively called "ports". 

<table style="border: none;">
  <tr>
    <td align="center">
      <img title="A Graph Box" alt="Example of a Graph Box" src="/images/graph_box_outside.png" width=50%>
    </td>
  </tr>
</table>

* The inputs are always placed on the left of the box, and the outputs are always on the right. 
* The small black dots shown next to the ports are the graphical 'hooks' to which arcs connect.
* Arcs are added to a dataflow just by clicking on, and dragging away from, a dot.
* Users can also edit the name of the box, change its colour, and move it around within in the dataflow, as they see fit.</ul>

The figure above shows the graph box from the outside - i.e. as an opaque process - with only its
name and ports, but no indication at all of what is inside. Opening the box (with a double-click) displays the nested dataflow that it encapsulates, which might look like this: 

<img title="Inside a Graph Box" alt="Inside a Graph Box" src="/images/graph_box_inside.png">

* Note that each of the box's external inputs and outputs are still visible, but are now themselves displayed as named boxes, with dots that can be connected to other boxes in the nested graph.
* The user can edit the names of the ports, change their colour and move them around, as they wish.
* The small capital letters on the port boxes denote the port's data type, in this case, respectively: `B` for Bool, `F` for Float and `S` for String - which the user can also change (via a context menu).
* The other boxes shown are a "meter" widget and a "text" widget (described
later) and another graph box named "Another Nested Dataflow" - just to illustrate that nested
dataflows can themselves contain nested dataflows, with the hierarchy potentially expanding to
many levels.

Users can define graph boxes, and construct nested dataflows as they wish. They
can cut, copy and paste arbitrary selections of graph elements to and from diﬀerent levels of the
hierarchy, or between diﬀerent dataflows, and the entire (top-level) dataflow can be saved in
document form to be re-opened when needed and/or transferred between computers.

#### Proxy Box (Remote Process)
A proxy box represents a remote process instantiated on a networked device. In the figure below, for example, the device is a small mobile robot with left/right wheel motors and an onboard
IMU. A running process on the robot accepts control inputs for wheel velocity, and outputs the robot's orientation (in Euler angles) and acceleration (as XYZ vectors) as captured by the IMU. This process is made available to the network by a small publish-subscribe server, which can host
multiple processes simultaneously. The server (and its published processes) can be written in any
programming language, but must conform to a Flo-specific network protocol (defined in [FloBox](https://github.com/kk-0129/FloBox)).

<img title="A Proxy Box" alt="A Proxy Box" src="/images/proxy_box.png">

* Strictly speaking, the term 'device' here refers to the software server - which has a distinct network address - and not the hardware on which it runs, which could in principle run several servers. A remote process published by a server is represented in the dataflow by a proxy box, which is similar in appearance to a graph box, but with the addition of labels giving the remote device's name (e.g. 'My Robot') and network address (10.99.1.116:9929). The name of the box (e.g. 'Body' in the example above) identifies the process.

* Data flowing into the proxy box, from arcs connected to its inputs, is transparently transmitted over the network to the actual process running on the physical device. Likewise, data generated by the remote process is sent over the network to the box's outputs. The box is literally a proxy to the remote process.
* When the remote process is unavailable - for example, if the robot is turned oﬀ - the graphical representation fades and becomes somewhat transparent. It will automatically return to its normal 'active' colour when the remote device becomes available again.
* A process is identified by its name and its ports (i.e. their names and types): so if any of these change while the device is unavailable, the process will not be recognised, and its box will remain faded out.
* Users can change the colour of a proxy box, move it around the graph, and connect its ports to other boxes. But unlike graph boxes, the name of a proxy box can not be modified, and double-clicking (to 'open' it) has no eﬀect.

### Built-in 'Widget' Boxes

In addition to the graph and proxy boxes (above), which are user-defined and can encapsulate
any arbitrary process, Flo also provides 6 built-in "widgets" - named: *annotation*, *clock*, *expression*, *meter*, *switch* and *text* - with have fixed, specialised functions, as described here:

#### Annotation
An annotation widget is just a resizable label. It has no inputs or outputs, and serves just to annotate and provide some visual structure or semantics to a dataflow. In the figure below, the annotation helps indicate the purpose of a switch.

<img title="Annotation Widget" alt="Annotation Widget" src="/images/annotation_widget.png">

#### Clock
A clock widget, shown below, displays a date and (analog) time. It has one input and one output, both of type `Date`. When the clock has an incoming arc its output is a copy of its input. Otherwise it outputs the current system time. The widget always displays its own output.

<img title="Clock Widget" alt="Clock Widget" src="/images/clock_widget.png">

#### Expression
An expression widget is a small text field, into which the user can type arbitrary computational
expressions, which the widget interprets as functional procedures. Any variables in the expression become the widget's inputs, over which the expression is evaluated to calculate the output. The expression language is unique to Flo (and is defined in [FloGraph](https://github.com/kk-0129/FloGraph)). A few illustrative examples are shown in the figure below:

<img title="Expression Widget" alt="Expression Widget" src="/images/expression_widget.png">

#### Meter
A meter widget, shown below, provides a visual representation of a numeric (Float) value. It
has one input and one output, both of type Float. The value displayed by the meter is always the
same as its output. By default, when the meter has incoming arcs, its output tracks the last input received. Otherwise, the output varies either automatically (as described shortly), or by dragging the mouse vertically over the meter's dial.

<img title="Meter Widget" alt="Meter Widget" src="/images/meter_widget.png">

In addition to its input & output, the meter also has 4 customisable parameters, named `rate`,
`max`, `step` and `min` (accessed by double clicking the widget):

<img title="Meter Widget Parameters" alt="Meter Widget Parameters" src="/images/meter_widget_params.png">

* The values of these parameters determine the meter's behaviours:
  * `min` and `max`, are the lower and upper bounds on the meter's value (the default range is -1.0 to +1.0).
  * the `step` parameter constrains the output to values that are multiples of *step*, e.g. *step* = 1 will constrain the output to integer values only (the default, *step* = 0, is to vary continuously)
  * the `rate` parameter has various eﬀects:
    * if the meter has incoming arcs, then:
      * if *rate* > 0, the output will move incrementally towards the input value, changing by step every *rate* seconds, otherwise, the output follows the input (modulo the *step*).
    * otherwise:
      * if *rate* < 0, the meter periodically switches, every -*rate* seconds, to a new random number (in the range *min* to *max* modulo the *step*).
      * if *rate* = 0: the output follows mouse dragging (modulo the *step*).
      * if *rate* > 0, the meter increments by the value of the *step* parameter every *rate* seconds, with *min* and *max* as periodic boundaries.

#### Switch
A switch widget, shown below, provides a visual representation of a Boolean value. It has one
input and one output, both of type Bool. The value displayed by the switch is always the same as
its output. By default, when the switch has incoming arcs, its output is the same as the last input received. Otherwise, the output toggles either automatically (below), or by clicking on the switch.

<img title="Switch Widget" alt="Switch Widget" src="/images/switch_widget.png">

The switch has a single customisable rate parameter, non-zero values of which will automatically toggle the switch every *abs(rate)* seconds.

<img title="Switch Widget Parameters" alt="Switch Widget Parameters" src="/images/switch_widget_params.png">


#### Text
A text widget, shown below, is an editable text label with an input and output, both of type
String. The widget's output always follows its displayed text value. By default, when the widget
has an incoming arc, its displayed value is constrained to the last input received. Otherwise, the widget's label is editable.

<img title="Text Widget" alt="Text Widget" src="/images/text_widget.png">



