# SYMbol relAtion sysTEM (Symatem)
This project is a different approach to most practical parts of computer science.
If you give it a try, you will find out soon, that even though some things are familiar others are completely alien to you.
I recommend [this WBW Post](http://waitbutwhy.com/2015/11/the-cook-and-the-chef-musks-secret-sauce.html) about thinking differently in general.

I tried to break things down to their basic elements and rearrange them in a useful way regardless of how most others are doing them nowadays.
The goal is to develop a consistent unified system / framework replacing all of these components:

- Processing
    - [Programming "Language"](Processing/Programming.md)
    - [Compiler](Processing/Compiler.md)
    - [Concurrency](Processing/Concurrency.md)
    - [Standard Library](Processing/StandardLibrary.md)
    - [Runtime Environment](Processing/RuntimeEnvironment.md)
- Transport (Encode / Decode)
    - Transport in time: Storage (Write / Read)
    - Transport in space: Transmission (Send / Receive)
    - [Ontology Engine](Transport/Ontology.md) / Database / File System
        - [Internal Format](Transport/InternalFormat.md)
        - [External Format](Transport/ExternalFormat.md)
    - [Encodings](Transport/Encodings.md)
    - [Version Control](Transport/VersionControl.md)
    - Distribution
- Toolchain
    - [User Interface](Toolchain/UserInterface.md)
