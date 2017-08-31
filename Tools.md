# Tool Chain & Human Computer Interaction


## Visualization
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

### Projectional editing
Text-based:
    - Disadvantages:
        - One long sequence of characters which has to be tokenized and parsed first: Unnecessary error source
        - Is hard for machines to process and generate
        - Use hierarchies: Only one output per instruction, need additional concepts like names and symbols to link the tree to a graph
        - Semantic structures are hidden below geometry and syntax, this disables projectional programming
    - Advantages:
        - Typing with the keyboard can be faster and more compact than graphical programming using the mouse
        - Geometry improves orientation inside the source code for some people
        - Currently tools like GIT are optimized for this
