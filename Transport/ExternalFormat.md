# External Format
The interchange format encodes a diff of the version control.
The entire graph database can be serialized and deserialized back again by encoding it as a diff from the empty version to the current state.
There is a JSON based representation and an equivalent binary representation is also planned.

## JSON Representation
This representation is generally compressed in the sense that redundancy was minimized by design, but binary compression algorithms are used.
However, to enable interoperation with line-based VCS like GIT and to merge the JSON files in them, lots of line-breaks as newlines can be added optionally.

## Symbol Maps
There are many maps of symbols in this format so they share a common scheme.
Symbols are pairs of identities, one for the namespace the symbol is in and one for the symbol itself inside its namespace.
All symbols in the map are sorted, first by their namespace identity then by their symbol identity.
Because many symbols are in the same namespaces it makes sense to compress the namespace identity by not repeating it and only marking changes.
Negative numbers indicate a change of the namespace identity for all following symbols to the next negative number.
Because 0 is a valid symbol identity, but also a valid namespace identity, the namespace identities are shifted to start at -1 to distinguish this case.
All other (non integer) datatypes are considered values / payload and associated with the most recent symbol.

## Operations
There is a list with five slots for each symbol in the outer symbol map, which has the JSON key 'symbols', to describe its related operations:
- 0: True means it was manifested, false means it was released and a placeholder null is used if it was neither (remained unchanged)
- 1: The triple operations represented by a double nested symbol map. True means a triple was linked and false means it was unlinked. If the entity symbol was manifested or released the true / false flags are skipped and implicitly become true for manifested and false for released.
- 2: The list of replace operations with the source being the data source symbol of the diff. Each operation is represented by three numbers:
    - Destination offset
    - Source offset
    - Length
- 3: Same as the previous but for all other source symbols and thus grouped by their source symbol and nested in a symbol map.
- 4: The list of crease length operations. Each operation is represented by two numbers:
    - Destination offset
    - Length

## Data Source & Restore
The two data stores of a diff are treated separately and placed at the root of the JSON object with the keys 'dataSource' and 'dataRestore' respectively, if present (length > 0).
Each is an object with a hexadecimal string containing the data at the JSON key 'data'.
Additionally, the data restore object has a list of replace operations (like one at index 3 in the operations structure) at the JSON key 'replaceOperations'.

## Binary Representation
NOTE: This is not implemented yet.

This representation supports additional features like:
- Compression
- Encryption
- Cryptographic Signatures
