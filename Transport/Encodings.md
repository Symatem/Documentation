# Encodings
A special feature of our encodings is that the size can not only
be statically defined in the encoding and dynamically in the data, but also automatically inferred.
So when nesting multiple encodings using composites, the responsibility to manage the size can be in the children, in the parent or the encoding.
The outermost container is always a BitVector which has a dynamically defined size.

## PODs
- BinaryNumber: Natural numbers
- TwosComplement: Integers
- IEEE754: Floats
- UTF8: Text

These encodings do not have a defined size which means that their size has to be managed from the outside (by a composite of the BitVector).


## Composite
Composites unite multiple encodings by representing them in a sequence (using a slot for each child encoding).

### Slot Encoding
- Index: Specifies the encoding for a specific slot
- Default: Specifies the default encoding for all other slots (used for array like composites)

### Slot Size and Count
- SlotSize: Specifies the length in bits of each slot
- Count: Specifies the number of slots in the composite

Both can have a Value of:
- Void: Not defined, automatically infer from children (SlotSize) or own size (Count)
- Dynamic: Defined inside the composites data
- Any other Symbol containing a natural number: Defined statically in the encoding

If the Count is defined and either the SlotSize is defined too or all children have a defined size,
then the size of the entire composite is defined too.

### Common Use Cases
- Array
    - Use a static SlotSize to have slots of equal size (inner alignment)
    - Specify a Default encoding for the children

- Define the size of another encoding
    - Create an array with exactly one element (the SlotSize defines the size of the child)

- Struct
    - Use a static Count
    - Specify the encodings of the children using the Index Attributes
    - Use children which have a defined size

- Stream
    - Same as Struct but use children without a defined size


## Reasoning

### Encodings vs. Data Types
It is important to keep in mind that encodings are not data types.
Encodings are formats and define the binary representation at the transport layer,
while data types are protocols and define the interaction to other data types.
E.g. arithmetics: You can add two integers (data type) independent of whether they are encoded in twos complement or decimal digits.
Sadly, many other systems and programming languages confuse and mix up encodings and data types all the time.

### Inlining of Dynamically Growing and Shrinking Composites
Many other systems and programming languages can only define the size in two ways:
Statically in the encoding / data type or dynamically once per object / allocation.
So if a data structure changes its size at different places,
you have to split it up in parts which can change their size independently and use references / pointers in between them.

This approach is also possible using BitVectors (as allocations) and Triples (as references / pointers),
but in case your data structure is read far more often than changed in its size, you can use a new concept: Dynamic composite inlining.
Inlining structs in structs in C is static composite inlining.
However in C these structs can not change their size.
But our BitVectors can because BitVectors are implemented as [ropes](https://en.wikipedia.org/wiki/Rope_(data_structure)).
Read and write operations are faster because of better better cache coherency,
but size changing operations cost more as they have to update the size fields of the entire hierarchy instead of just one.
