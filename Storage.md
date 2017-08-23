# Storage
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

### BitMap
Each symbol has its own infinite address space in which one of three states (0, 1 or undefined) can be assigned to each index.
Only 0 and 1 are encoded while undefined is used to allow for sparsity.
By default a symbols BitMap is filled with undefined and is thus empty.
Also, they do not interfere with the Triples in any way.
These BitMaps are an abstraction of memory virtualization:
- To store numbers, text and files
- To represent hardware devices and physical memory

### Ontology
One instance of the engine can hold multiple Ontologies which are separate Symbol spaces.
Therefore, all Triples and BitMaps are separated between Ontologies as well.
Possible use cases include:
- Memoization / caching
- Isolation / security
- Temporary workspaces
- Virtualization / views
- [Version Control](VersionControl.md)


## Representations: Internal vs. External
The internal format is used while the system is running and can be used for in other instances of the engine as well,
as long as they have the same hardware (register bit length and endianness) and run the same software version.
The he external format comes into play if a migration to a different instance is needed (hardware or software version)
or a part of the data is extracted and compressed for backups or transport over network.

## Internal Format
The data of all SubOntologies is stored in one image, separated into two levels:
- Storage level: Basic data structures and BitVectors
- Ontology level: Triples and BitMaps

The RAM is used as a big cache (by mmaping it) and doesn't have its own address space instead the one of the disks is used.
So no data structure translation takes place and the memory hierarchy is homogeneous.
In the future a different approach might be used:
- All the time using MMU (current implementation)
    - Costs additional time and energy
    - Requires mapping for the entire disk address space
- On demand
    - Costs one bit pointer tag, which has to be checked upon every new access
    - Requires some meta structures
- None
    - NVM or RAM only

### Page
Pages of the size 2 to the power of 15 bits divide the entire memory.
The first page is called SuperPage and represents the root of all structures.
There is a pointer to the end of the currently allocated memory (immediately behind the highest page in use)
and a free pool of pages which were released but are not at the end.
New acquired pages are taken from the pool first and if it's empty then from the end.
This ensures locality of the images as it grows (but not as it shrinks).

### BitVector
BitVectors consist of a symbol (like an inode in filesystems) and a sequence of bits associated with it, addressable by an offset.
Both symbol and offset together span a virtual address space.
There is one associative array per Ontology which maps symbols to start addresses of the BitVectors payload / data.
BitVectors which are smaller than half a page are stored in buckets the rest is fragmented in the leaves of balanced trees
(currently [B+ trees](https://en.wikipedia.org/wiki/B%2B_tree), might be replaced by [Van Emde Boas trees](https://en.wikipedia.org/wiki/Van_Emde_Boas_tree) for example).

### Bucket BitVector
Sections:
- Header:
    - Type: Index of the alignment size / how big each data segment is
    - Count: Number of BitVectors in this bucket / how many data segments are in use
    - FreeIndex: Entry to a linked list of free segment indices (stored in symbols section)
- Sizes: How many bits of the aligned data segments are in use per BitVector (minus minimum size in this bucket)
- Data: Actual data segments / payload of each BitVector
- Symbols of the BitVector to enable redistribution between buckets

### Fragmented BitVector
Instead of using the addresses explicitly as key (indices) and data bits as values in a associative array (like it is done by most filesystems)
we use implicit addresses called ranks by keeping order statistics which create a data structure called [rope](https://en.wikipedia.org/wiki/Rope_(data_structure).
[Order statistic trees](https://en.wikipedia.org/wiki/Order_statistic_tree) are implemented by storing the element count of the sub trees spanned by the children
(these counts are integrated in the parent node to allow binary search) and enable local updates of the now implicit addresses,
thus efficient range insert / delete without moving the entire rest of the data and updating all addresses.

### BitMap
BitMaps consist of a virtualization header followed by all the defined payload slices without any gaps.
The header is an associative array mapping virtual offsets to offsets inside the BitVector of the BitMap.
BitMaps are simply the extension of BitVectors with sparsity support.

### Triple Index Modi
The following modes are supported which are strict subsets of one another:
- Single: EAV
- Tri: EAV, AVE, VEA
- Hexa: EAV, AVE, VEA, EVA, AEV, VAE

The letters E(ntity), A(ttribute) and V(alue) represent their semantic position in a Triple.
The order is important, e.g. in tri-mode each letter (E/A/V) is first, second and last once (this is intended).

### Triples Index Structure
Obviously a two level index is needed: One for the first to the second letter and one from the second to the third.
But 6 two level indices are unnecessary. One top level index which contains 6 sub level indices (AV, VE, EA, VA, EV, AE) is sufficient,
because the functionality of each index is decided by the last two letters and the first letters can be merged into one.
All 6 subindices together with the BitMap of a Symbol are concatenated into one BitVector per Symbol.
