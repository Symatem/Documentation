# Ontology
When I started this project I asked my self: "What is information?"
This seemed to be the central question, as many terms are gathered around it:
Computer, mathematics, other sciences, the word intelligence ...
everything is about storing and evaluating information.

A little thought experiment: Take a dictionary and assign an integer to each word.
Send a copy of this dictionary to a friend and then encode a letter using these word-to-integer translations.
Except from the additional work overhead, nothing has changed, when compared to writing normal letters.
The information remains exactly the same.
So information does not arise form the Symbols but only from the relations between them.
In computer science we use and apply this principle everyday and still often forget about it.


## Representations: Internal vs. External
The internal format is used while the system is running and can be used for in other instances of the engine as well,
as long as they have the same hardware (register bit length and endianness) and run the same software version.
It also contains a lot of redundancy in acceleration data structures.
The external format comes into play if a migration to a different instance of the engine is needed (hardware or software version)
or a part of the data is extracted and compressed for backups or transport over network.


## Outer Interface

### Symbol
They act as pointer, references or object identifiers.
Because they don't encode any information, every distinguishable thing does the job.
We use natural numbers (unsigned integers) as our current computers can handle them the best.

### Triple
A Triple is a tuple composed of 3 Symbols: Entity, Attribute and Value.
Small analogy: In natural languages the Entity is called subject,
Attribute predicate, Value object and a Triple forms a sentence.

| Name | Description |
| ---- | ----------- |
| Entity | More or less the actual object |
| Attribute | Like a property or a key in a dictionary / map |
| Value | Like the value of a property or the value in a dictionary / map |

### Data
Each symbol has its own infinite address space in which a binary state can be assigned to each index.
They do not interfere with the Triples in any way.
They are an optimization and abstraction of memory virtualization:
- To store numbers, text and files
- To represent hardware devices and physical memory

### Symbol Space
One instance of the engine can hold multiple separate Symbol spaces.
All Triples and BitMaps are separated between them.
Possible use cases include:
- Performance: Memoization / caching
- Context isolation
    - Modularity / distribution
    - Security (malicious) / robustness (accidental)
    - Temporary workspaces
    - Relocation to avoid Symbol collision and ambiguity
- Virtualization / views
    - Inductive: To enable infinite sets for example
    - Transparent: Materialized or virtual should not matter
    - [Version Control](VersionControl.md)


## Reasoning

### DIKWE Pyramid
Our interpretation and modification to the model of the [DIKW pyramid](https://en.wikipedia.org/wiki/DIKW_pyramid):
- Data / know-nothing: BitMaps are unprocessed raw data. They have to be processed and structured to become useful. But they still have a purpose as this is the only layer which has a physical manifestation and it is thus necessary for the transport (storage and transmission). The indent of encodings is to bring all upper layers down to this one for practical reasons.
- Information / know-what: Triples represent the interconnection and structure. They can answer specific questions to ”who”, ”what”, ”where”, ”how many”, ”when”. Information alone is not enough as it would only provide a database with a query interface.
- Knowledge / know-how: A system also needs to know how and what to ask in order to process information in a useful way. This domain specific intelligence enables combining and optimizing the available resources with a specific task in mind. It could be provided by something like automatic ontology merging.
- Wisdom / know-why: Knowing the reason why things are the way they are or how they came to be can help to further improve the own system by preventing the repetition of errors and learning from them, which might lead to new insights again. This is a fundamental part of general intelligence.
- Enlightenment / know-yourself: The final step towards general intelligence is a utility function, a motivation which evaluates what should and what shouldn’t be. Having goals gives us a reason to think about the future, predict multiple outcomes and plan a strategy to be acted out.

### Why we are using Triples
Mathematically you could base it all on sets (the Zermelo-Fraenkel-Axiom-System),
but it is quiet annoying when it comes to ordering things.
So using tuples (which can be formed out of sets only) is a more elegant way.

For example LISP and Scheme both seem to use only 2-tuples (CAR, CDR).
Actually these are 3-tuples too, because each one has its own address (an additional implicit data field).
But it usually promotes thinking in hierarchies like trees, lists and so on.
Using a homogeneous tuple (Entity, Attribute, Value)
gives you a even better foundation and promotes thinking in networks and graphs, though.
It is like the difference between living in a 2D or a 3D world.

I considered higher orders and dimensions too, but I decided to go with the number three because:
- You can't always assign useful semantics to each position of a longer tuple
- Or you would have to use varying lengths, but that could be done with Triples in first place
- And the complexity of search indices increases with the factorial function

### Why we don't use Semantic Web Technologies
Some of the flaws are described in [these blog posts](http://milicicvuk.com/blog/2011/07/14/problems-of-the-rdf-model-blank-nodes/):
- Too many different technologies: RDF, OWL, Turtle, N3, ...
- 3 types of symbols (called terms): IRIs (Internationalized Resource Identifiers), Literals, Blank Nodes
- Literal encodings / data types are a special feature and not represented in Triples
- Literal values define the identity of the token, thus can not be context specific
- EAV is asymmetric: Each element of a triple can only host certain types of symbols

Additionally we identified these:
- No usable tool chain
- No virtualization
- No version control
- Only a simple query engine (SPARQL) but no arbitrary programs / execution

### Why we are using BitMaps
Ontologies could work without BitMaps as pure EAV stores but then all literals and binary data would be expressed in an ugly and inefficient way.
For example: Numbers would be represented like we humans do using a sequence of symbols, so called digits, forming a positional notation.
Let alone storing huge files like audio or video data would be very impractical.

### Order of Values with the same Entity and Attribute
- User defined order
    - Ordered containers like arrays would not generate any overhead
    - Values gain a special role and this is thus asymmetric
    - Would be inconsistent with the rest of the model in which all Triples are an unordered set
- Natural order
    - Order has to be represented using Triples, thus generating overhead but only for sequences
    - Search indices can be optimized
- Virtualized Index Attributes (We use this option)
    - Stays consistent with the rest of the model
    - Can be optimized internally (no overhead)
    - Limited by the available Symbols

### Referencing triples
- Triple-ID: Just store a reference to an existing Triple
    - Requires the Triple to actually exist, no virtual or hypothetical Triples possible
    - A new type of Triple-Symbol would have to be introduced
    - Would be inconsistent with the rest of the model which uses only Symbols and BitMaps
- Triple Attribute: Specialize the Attribute and let it inherit from the original Attribute as prototype
    - Attributes gain a special role and this is thus asymmetric
    - Harder to look for specific Attributes: They have to be derived first
- Triple BitMap: A BitMap storing the tree Symbols
    - Semantics can not be found by the search indices
    - Generates a smaller storage overhead
- Triple Entity: An Entity storing the three Symbols as Attribute-Value-pairs (We use this option)
    - Semantics can still be found by the search indices
    - Must be explicitly queried
    - Symmetric and blends in nicely with the rest of the model
    - Generates some storage overhead

### Bits not Bytes
I tried to understand the reason, why we are using 8 bits = 1 byte for
many calculations such as the pointer addresses. But the longer I searched, the less I found.
Apart form 8 bits there were other absurd numbers of bits forming a byte for example: 7, 9, 12, 36,
which are not even powers of two and machines implemented them on hardware level.
Even ASCII uses only 7 bit, which is a lucky coincidence, because adding one bit allowed for a
variable length encoding (called UTF-8) and fitting in 8 bits = 1 byte.
Today the byte is almost absent from the machines hardware
except that it is the smallest integer operation often.
But pointers could be measured at bit level. It would only add 3 bits to them
and because 64 bit machines just use up to 48 bits, this would not even make a difference.
There is also no way to address a single byte on the hardware, as it would be way to inefficient.
The registers, caches, RAM and page-tables all use far higher numbers of bits.

The question is if there is anything to gain, when going back to bit level.
And yes there is: Many compression algorithms, networking, video / audio streaming ...
all use bit level operations. So these applications can be implemented far nicer
while others which rely on bytes do not have to suffer, as they can still just use 8 bits.
All in all it makes things more consistent and less confusing,
so we just abandoned the byte as you know it.

### Endianness
There are some big misconceptions about endianness:
- "It is about left and right"
Which is totally wrong, as you could write numbers up or down too.
It is actually just about the indices of the digits in a number.
When they rise with the significance of the digits it is "little" the other way is "big".
The historic reason endianness arose might be the following:
The decimal system was invented in the east, where some cultures write from right to left.
So they wrote the numbers in the same direction as the rest of their texts (little endian).
But when merchants brought this invention to Europe / the west (where we write from
left to right) they just kept the direction of the eastern cultures,
most likely for compatibility reasons not to confuse their business partners.
Thus they (and now we too) are writing the numbers in the opposite direction as the rest of the text (big endian).
- "It is like driving on the left or right side of the road"
Unlike the side of the road a country might have decided to drive on,
where there is no actual better or worse way of doing it, this is not the case with endianness.
Little and big endian are not symmetrical opposites such as left and right.
Therefore there are reasons for both, but one is better: little.
As it is more consistent, uses less operations when reinterpret casting and
has simpler equations to calculate the indices of digits for example.
When using big endian, you always need to know and take the total length of your number into account.
You might have noticed this disadvantage when trying to read out a huge number in a text:
You need to count the digits and think before you can start moving your lips.
- "It has to do with bits and bytes"
Fist of all it is independent of the base your positional system uses. So it is not a binary only problem.
It comes to existence the moment you have some sort of addressing memory words.
This is the reason why endianness only exists when it comes to bytes, not on bit level.
Simply because most architectures start addressing on byte level.
To cut a long story short we only use little endian.