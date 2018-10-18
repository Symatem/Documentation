# Programming
Almost all programming technologies today are arranged in some sort of spectrum. Usually we call the extremes low- / high-level and associate them with opposites like AOT vs. JIT, compiler vs. interpreter and virtual machines. Most software systems are specialized in one tiny area on the spectrum. Nothing covers the entire spectrum. And so we have to combine different systems and architectures to a huge inhomogeneous patchwork rug. This increases overall complexity and comes with a lot of problems: People have to learn all those implementations, they don't work well together, we have lots of adaptors instead of nice modularity and thus even more bugs. Programming languages come and go all the time, some stay longer than others. But most of them move around a few paradigms in circles and just paint the same ideas with new colors.

## Goals
- Symmetric / Uniform / Homogenous
- Concurrent / Parallel by default
- Minimalistic: Small compiler, move everything else into Standard Library
- Easy refactoring: Partitioning / capsulation and inlining
- Abstraction mechanisms: Reflection / meta programming
- No structural difference of compile-time and runtime
- Not based on sequences or textual representation: Projectional editing

## Design Decisions
- No macros or lookups
- Axiomatic / DAG Layers vs Cyclic Graphs: No fundamental primitives (primitives are just an optimization)
- Control flow is expressed by data flow (using lambdas for conditional execution)
- Parameter / arguments are not ordered only named (mapping)
- Execution order is implicitly defined by data flow (partial ordering)
- Stateful operations can use void carriers for explicit (total) ordering
- Operator boundaries are only for abstraction and not an implicit sync fence
- Dynamic typing with automatic type deduction
- Variables are split into transport declarations and placeholders
- Concolic: Const expressions (Concrete Execution) => Deferring => TypedPlaceholders (Symbolic Execution)

## Components
- Operator:
    - Consists of
        - Operations
        - Carriers
        - Operands
    - Is called by operations (an operator can contain an operation calling itself, leading to recursion)
    - Is not a sequence (total order) of steps => But a DAG (partial order)
    - Boundaries are not sync fences (except for primitives)
- Operation:
    - Executes / calls an operator received from the respective operand (tag is "Operator")
    - Receives and sends operands via carriers
- Operand:
    - An operand is the data which is passed over at an operators boundary as input or output
    - Operands are not explicitly modeled but implicitly defined by carriers using the same operand tag
    - Special operand tags:
        - Constant: See carrier
        - Operator: Specifies which operator is called by the receiving operation
        - Operands: Can pack and unpack all operands of a bundle at a boundary
- Carrier:
    - Carriers are directional and have two ends (source and destination)
        - Both ends are associated with an operand tag
        - Both ends can be an operation or the outer operator
        - The source can be a constant value (using the "Constant" operand tag)
    - They can be bundled in other carriers hierarchically
    - Carriers between operations contained by different operators are invalid
    - Data is replicated if multiple carriers are fed by one operand as source
- Operator Instance:
    - An operator which is called with a certain set of operands creates an instance (like templates / generics)
    - Are cached and results / outputs are reused if another call with the same inputs occurs


## Reasoning

### Inspiration
- LISP
    - Meta-programming / reflection / homoiconicity: Programs are of the same structure as other objects / data
    - Recursion instead of iteration
    - Minimalistic syntax: Almost projectional editing
    - Primitives are just an optimization and can be constructed using others
    - Few primitives, move everything into Standard Library
- ECMA Script / JavaScript
    - Dynamic Attributes / Properties
    - Inheritance: Prototyping instead of classes
    - Array indices are just normal Attributes
- C++
    - Const Expressions
    - Templates
- Smalltalk / Squeak
    - Super IDE / All-in-one VM image: Operating System, Database, Editor, Compiler, Live Debugger, Version Control
- UNIX / POSIX
    - Inodes (Ontology Symbols)
    - Mounting (Virtualization / Namespaces)
    - Reification: Everything is a file
- LLVM IR
    - Basic blocks
    - SSA
    - DAG of data flow and execution order
- Semantic Web: RDF
    - Triples
    - EAV model
    - Reflection
- GIT
    - Branches
    - Distribution

### Programming Paradigms
There are three major approaches to programming:
- Imperative
    - Total order: Sequence of instructions
    - Strict evaluation of functions (sync-fences)
    - Data flow is implicit and obscure, e.g. "side effects"
        - Cached values become outdated if their dependencies change
        - State interference: Iterate while modify, race conditions, ...
    - Control flow is explicit and not integrated into data flow
    - Strict consistency, locking: Bad for multithreading
- Functional
    - Partial order: Data flow graph
    - Implicit control flow in data flow
    - Concurrent by default
    - Immutable: No stable object identity (over time), object orientation is impossible
- Declarative / Logic
    - Control flow and data flow are non existent: They aren't programs in that sense
    - Static: No concept of time at all, far from physical world
    - Constraint and relation based: Similar to Transport layer

Properties they share:
- Describe
    - Imperative and Functional: Describe how things are done, without necessarily describing the result
    - Declarative / Logic: Describing how valid results look like, without describing how to obtain them
- Single static assignment
    - Imperative can be transformed into functional using SSA
    - Functional can be seen and executed as imperative without any changes
- Referential transparency
    - Results and expressions are interchangeable.
    - No side effects: Always reproducible
    - Stateless: Interaction with physical world is impossible
    - Functional and declarative share this property

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

### Control Flow
- Continuation: Cycles, PHI Nodes
- Lambda: DAG, Recursion

### Lifetime Management
- Manual: Like new and delete
- Automatic reference counting (ARC)
- Stack / scope bound
- Garbage collector (GC)
