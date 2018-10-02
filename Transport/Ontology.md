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
The internal format is used while the system is running and can be used in other instances of the engine as well,
as long as they have the same hardware (register bit length and endianness) and run the same software version.
It also contains a lot of redundancy in acceleration data structures.
The external format comes into play if a migration to a different instance of the engine is needed (hardware or software version)
or a part of the data is extracted and compressed for backups or transport over the network e.g. for version control.


## Outer Interface

### Symbol
They act as pointer, references or object identifiers.
Because they don't encode any information, every distinguishable thing does the job.
We use natural numbers (unsigned integers) as our current computers can handle them the best.

### Triple
A Triple is a tuple composed of 3 Symbols:
<span style="color: red;">Entity</span>, <span style="color: green;">Attribute</span> and <span style="color: blue;">Value</span>.

Natural Languages often call them <span style="color: red;">Subject</span>, <span style="color: green;">Predicate</span> and <span style="color: blue;">Object</span>:
- English: A <span style="color: red;">car</span> <span style="color: green;">has</span> an <span style="color: blue;">engine</span>.
- German: Ein <span style="color: red;">Auto</span> <span style="color: green;">hat</span> einen <span style="color: blue;">Motor</span>.
- Japanese: <span style="color: red;">車</span>は<span style="color: blue;">エンジン</span>が<span style="color: green;">あります</span>.

[Binary relations](https://en.wikipedia.org/wiki/Finitary_relation):
(<span style="color: red;">a</span>, <span style="color: blue;">b</span>) ∈ <span style="color: green;">R</span>

There are some more which can not reuse the Entity and thus lead to hierarchical structures like trees and lists:

JSON: <span style="color: red;">{</span><span style="color: green;">"key"</span>: <span style="color: blue;">"value"</span><span style="color: red;">}</span>

XML: &lt;<span style="color: red;">tag</span> <span style="color: green;">key</span>=<span style="color: blue;">"value"</span> /&gt;

LISP: <span style="color: red;">(</span><span style="color: green;">car</span> <span style="color: blue;">cdr</span><span style="color: red;">)</span>

### Data
Each symbol has its own infinite address space in which a binary state can be assigned to each index.
The highest defined index is called its length.
They do not interfere with the Triples in any way.
They are an optimization and abstraction of memory virtualization:
- To store numbers, text and files
- To represent hardware devices and physical memory

### Namespace
One instance of the engine holds multiple separate Namespaces.
Symbols belong to a Namespace (in which they have an unique ID) and Triples belong to the Namespace of their Entity.
Triples can use Symbols from different Namespaces and are unique globally.
Each Namespace has an ID: A Symbol of a special meta Namespace which is used to manage relations between different Namespaces.
Furthermore, a Namespace is associated with a driver which handles all requests concerning this Namespace.

Possible use cases include:
- Distribution / Relocation: Handle Symbol collision and ambiguity to integrate foreign ontologies
- Sharing / Remote content
- Modularity / decomposition / package management
- Virtualization / views / linking / mounting
- Isolation: Security (malicious) / robustness (accidental)
- Memoization / caching
- Transparency: Materialized or virtual does not matter
- Reification: e.g. hardware interfaces
- Induction: e.g. infinite sets
- Temporary workspaces / concurrency
- Transactions / [Version Control](VersionControl.md)


## Reasoning

### DISRP Pyramid
Our interpretation and modification to the model of the [DIKW pyramid](https://en.wikipedia.org/wiki/DIKW_pyramid):

- Data: BitMaps are unprocessed raw data. They have to be processed and structured to become useful. But they still have a purpose as this is the only layer which has a physical manifestation and it is thus necessary for the transport (storage and transmission). The indent of encodings is to bring all upper layers down to this one for practical reasons.
- Information (What): Triples represent the interconnection and structure. They can answer specific questions to ”who”, ”what”, ”where”, ”how many”, ”when”. Information alone is not enough as it would only provide a database with a query interface.
- Strategy (How): A system also needs to know how to do process information and what questions to ask: Algorithms, applications, encodings etc.
- Reasoning (Why): Knowing the reason why things are the way they are, how they came to be and how they could be (simulation of alternatives) can help to further improve the own system by preventing the repetition of errors and learning from them, which might lead to new insights again.
- Purpose (Axiom): A utility function evaluates what should and what shouldn’t be. All layers below are rational, but this one is an axiom and has to be defined from the outside. A system can not define its own purpose (without a purpose).

### Why we are using Triples
Mathematically you could base it all on sets (the Zermelo-Fraenkel-Axiom-System),
but it is quiet annoying when it comes to ordering things.
So using tuples (which can be formed out of sets only) is a more elegant way.

I considered different orders and dimensions too:
- 0 and 1-Tuples make no sense as they don't create any relation
- Pure 2 Tuples are possible but need lots of blank nodes as proxies and end up emulating Triples
- Fixed n-Tuples have to assign a fixed meaning for each position (scheme) and end up being a database table with sparse values
- Dynamic n-Tuples have to define a meaning for each position too (Attribute) and end up emulating triples again

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

### Renaming Symbols
In a decentralized system each instance has to assign new Symbols unique identities independent of the other instances.
However if they try to converge and merge their ontologies they can encounter two types of problems:
- Symbol collision: Two instances assigned the same Symbol but meant different things
- Symbol divergence: Two instances meant the same thing but assigned different Symbols
To overcome this at least one instance needs to rename its Symbols upon merging.
We didn't go for random UUIDs as they must be very long and thus take lots of memory.
Also, they can not solve the divergence problem.
Therefore, it needs to be possible to rename Symbols.

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
