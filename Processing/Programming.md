# Programming
Almost all programming technologies today are arranged in some sort of spectrum. Usually we call the extremes low- / high-level and associate them with opposites like AOT vs. JIT, compiler vs. interpreter and virtual machines. Most software systems are specialized in one tiny area on the spectrum. Nothing covers the entire spectrum. And so we have to combine different systems and architectures to a huge inhomogeneous patchwork rug. This increases overall complexity and comes with a lot of problems: People have to learn all those implementations, they don't work well together, we have lots of adaptors instead of nice modularity and thus even more bugs. Programming languages come and go all the time, some stay longer than others. But most of them move around a few paradigms in circles and just paint the same ideas with new colors.


## Components
Our approach to programming is derived from graphical data flow models and similar to functional programming and the way we design logic circuits today.
Control flow is implemented by passing operators / lambdas around as data flow and then optionally executing them like in LISP.
The names of the components are not decided yet.

- Operator: unit, block, method, component, module, node
    - Has couplings => Interface
    - Has operations and carriers => Implementation
    - Not a sequence (total order) of steps => But a DAG
    - Additional constraints can be installed for different behavior
    - Primitives are atomic / fundamental and can not be subdivided => their boundaries are sync fences

- Operation: instruction, instance, element

- Carrier: wire, connection, belt, cable, thread, line, edge, conveyor, tube mail, tube, pipe, track, railway, road, way, path
    - Two ends, one direction only
    - Located between units
    - Data flow is well defined and variables become obsolete: Data is passed directly and can not be overwritten (functional style)
    - Execution order is implicitly defined as well, stateful operations can use empty data connections

- Coupling: socket, input/output, entry/exit, gate, portal, port
    - Two ends, one direction only
    - Located at an operator boundary

- Package, token, data, train, cart, bubble, message, telegram
    - Contains independent / temporary data

- Activation, record, process, thread, program, application, task, call, execution instance


## Reasoning

### Programming Paradigms
- Imperative
    - Disadvantages
        - Sequence of instructions (a total order)
        - Strict evaluation of functions (sync-fences)
        - Data flow is implicit and obscure, e.g. "side effects"
        - Control flow is explicit and not integrated into data flow
        - State interference: Iterate while modify, race conditions, ...
        - Strict consistency, locking: Bad for multithreading
        - Cached values become outdated if their dependencies change
    - Advantages
        - Close to the physical world and the machines it is running on
        - Useful for actual implementations

- Functional
    - Disadvantages
        - Immutable: No stable object identity (over time), object orientation is impossible
        - Stateless: Interaction with physical world is impossible
    - Advantages
        - Useful to describe ideas and algorithms
        - Closer to math and more abstract

- Descriptive
    - Disadvantages
        - Control flow is non existent: They aren't programs in that sense
        - Static: No concept of time at all, far from physical world
    - Advantages
        - Implementation and optimization independent
        - Useful for storing data

### Uni- vs bidirectional
- Unidirectional is simpler and two connections can be combined to be bidirectional
- Bidirectional is sometimes used in electronics like buses

### Connection cardinality
- Many-to-many would be pointless as it would be the same semantic as variables and introduce the same problems
- One-to-many leaves the exact scatter and gather semantic undefined
- One-to-one can model everything and groups of the same name can act like one-to-many connections

### Evaluation
- Push forward and execute next block as soon as all needed values arrive (Forward / Eager Evaluation)
- Pull values from previous blocks what is needed (Backward / Lazy Evaluation)

### Lifetime Management
- Manual: Like new and delete
- Automatic reference counting (ARC)
- Garbage collector (GC)
