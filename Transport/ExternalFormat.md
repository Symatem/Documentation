# External Format
The interchange format encodes a differential of the version control.
An entire Ontology can be serialized and deserialized back again by encoding it as a differential from the empty version to the current state.
There is a human readable and an equivalent binary representation.

## Human Readable Representation
NOTE: This is not implemented yet.

This representation uses the popular JSON format with lots of line-breaks as newlines.
These do not only improve the readability, but also enable line-based VCS like GIT to merge two such files.

## Binary Representation
NOTE: This is not implemented yet.

This representation supports additional features like:
- Compression
- Encryption
- Cryptographic Signatures
