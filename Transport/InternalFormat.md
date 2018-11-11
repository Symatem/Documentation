# Internal Format
NOTE: This page is outdated and only describes the C++ implementation, not the ES6 and future ones.


All Namespaces are stored in one image, separated into two levels:
- Storage level: Basic data structures
- Ontology level: Triples and Symbols data fields

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

### Data Field
Data fields consist of a symbol (like an inode in filesystems) and a sequence of bits associated with it, addressable by an offset.
Both symbol and offset together span a virtual address space.
There is one associative array per Ontology which maps Symbols to their data fields start address.
Data fields which are smaller than half a page are stored in buckets the rest is fragmented in the leaves of balanced trees
(currently [B+ trees](https://en.wikipedia.org/wiki/B%2B_tree), might be replaced by [Van Emde Boas trees](https://en.wikipedia.org/wiki/Van_Emde_Boas_tree) for example).

### Bucket of Data Fields
A bucket has the size of a page and is divided into the following sections:
- Header:
    - Type: Index of the alignment size / how big each data segment is
    - Count: Number of data fields in this bucket / how many data segments are in use
    - FreeIndex: Entry to a linked list of free segment indices (stored in symbols section)
- Sizes: How many bits of the aligned data segments are in use per data field (minus minimum size in this bucket)
- Data: Actual data segments / payload
- Symbols (back reference) to enable redistribution between buckets

### Fragmented Data Field
Instead of using the addresses explicitly as key (indices) and data bits as values in a associative array (like it is done by most filesystems)
we use implicit addresses called ranks by keeping order statistics which create a data structure called [rope](https://en.wikipedia.org/wiki/Rope_(data_structure)).
[Order statistic trees](https://en.wikipedia.org/wiki/Order_statistic_tree) are implemented by storing the element count of the sub trees spanned by the children
(these counts are integrated in the parent node to allow binary search) and enable local updates of the now implicit addresses,
thus efficient range insert / delete without moving the entire rest of the data and updating all addresses.

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
All 6 subindices together with the data field of a Symbol are concatenated into one data field of the storage layer below.
