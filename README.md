# SYMbol relAtion sysTEM (Symatem)
This project is a different approach to many practical parts of software development.
If you give it a try, you will find out soon, that even though some things are familiar others are completely alien to you.
I recommend [this WBW Post](http://waitbutwhy.com/2015/11/the-cook-and-the-chef-musks-secret-sauce.html) about thinking differently in general.

An analogy I came to like, even though it is inspired by the heavily overused quote attributed to Henry Ford, is the following:
Imagine we would still live in a world of horses.
If I were to talk about how I think I can revolutionize the transport system
most people would expect some sort of genetically modified horses to run faster or something alike.

When I talk about my idea of a car people get interested and want to know more details.
And when I proceed to talk about the components and subsystems of the car, I get questions like this:
"Where does this transmission-thingy fit into my horse and how is that supposed to make it faster?"

So as hybrids of cars and horses obviously don't make any sense,
a tight integration of this project and most other software makes not so much sense either.
In fact I want to keep it standing for itself and only use a few popular technologies as a launch pad
similar to cars and horses sharing the same roads in the early days.

And before you get to hyped up about all the things you might read here,
keep in mind that the first cars were merely toys of their inventors and nothing like the ones we drive (or may soon drive us) today.
So even if I succeed in constructing and presenting my prototype system,
we still have a long journey ahead of us to make it really useful.

I tried to break things down to their basic elements and rearrange them in a useful way regardless of how most others are doing them nowadays.
The goal is to explore the possibility and develop a consistent system / framework uniting all of these components:
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
