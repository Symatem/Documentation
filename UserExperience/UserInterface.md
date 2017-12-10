# User Interface
The user interface unites visualizer, editor and debugger into one.
It is an integrated development environment which is not only meant for developers.


## Triples
There are 4 different ways to look at Triples:

### Graph
Every Symbol is a point in a arbitrary space (in you imagination it might be 2D or 3D),
Each BitMap is like a leave hanging from a Symbol
and Triples are a strange kind of edges but not between two Symbols but three of them,
having a direction from ... via ... through to ...

### Condensed Graph
Personally I think of this as the most appealing form.
It is a modification of the normal graph.
Entities and their Attributes are collected in a Block with edges leading to their Values.

### Table / Set
If you come from standard relational SQL databases,
this might be familiar to you:
All triples are in a big table with these three columns: Entity, Attribute, Value.

### Voxel Graphics
And if you come from a mathematical school, you might find this form reasonable.
Every triple is a point in a 3D cartesian coordinate system.
The three axises are: Entity, Attribute, Value.
Little extra: A slice from the Entity/Value plane can be seen as a
[logical matrix](https://en.wikipedia.org/wiki/First-order_logic#Examples)
of a Attribute.


## Reasoning

### Projectional Editing
If you are not familiar with the idea:
[Here](https://cloudalion.org/2016/05/29/whats-the-deal-with-projectional-editing/) is a good introduction.

Advantages:
- No scanning / lexing / parsing needed: No syntax errors and different styles anymore
- IDE / GUI / beautifier / linter etc. become projectors
- Can be far more customized / domain specific (like DSLs)
- More homogenous structures / interfaces
    - Far less primitives, most can be moved to the standard library
    - Multiple inputs and outputs per function
    - No need for names (usually called symbols) to connect the sequence / hierarchy into a graph
- Program is just data like any other: Better meta programming and reflection / reification
- Better for machines to operate on
- More and potentially better code complexity measure than LOC (lines of code)

Disadvantages:
- Typing with the keyboard can be faster and more compact than graphical programming using the mouse
- Geometry improves orientation inside the source code for some people
- Currently tools like GIT are optimized for sequences / text
