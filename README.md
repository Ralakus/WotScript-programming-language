# WotScript-programming-language
This is the best programming language of all time, no one can disagree. Other languages are sneks


# General Architecture

## Modular Design
Each module is a secion of the full WotScript language
### Modules / Phases
![Modules](/Architecture.png)

Each module will comunicate with Unix pipes with JSON strings so for example the entire run command may look like `scanner file.txt | preprocessor | lexer | parser | semantic | IR gen | optimiser | backend`

#### Source preparation phase
* scanner/preprocessor
    > these two modules can be lumped together into a single step because the scanner is relatively simple even for more complicated incremental scanning of files. the scanner just opens a file, reads the content and passes it to the preprocessor, the preprocessor handles replacements of escape sequences like '\n' or '\t', it also handles the inclusion of other files or code replacement etc. much like the preprocessor of C but stripped down.

* lexer
    > the lexer or lexical analyser takes in a stream of characters and assigns meaning to sequential groups of them. for example, the sequence int a = 5; means something to a human but to a computer its just a bunch of bytes, the job of the lexer is to look at these characters and use a combination of current context, surrounding characters etc. to combine various sequences into meaningful units of information. so for example, the lexer would take the stream of characters int a = 5; and output [type: int], [identifier: a], [assignment: =], [seperator: ;]

#### Grammar verification phase
* parser
    > the job of the parser is to make sure that tokens fit together in the right way, this means looking at a token, checking its neighbours and current context and making sure its a valid usage of the language's grammar. for example, take the following stream of tokens: int, a, =, 5, ; the lexer must check if these tokens are in the right order and that each token is valid in the current context, our parser might accept the previous token stream but it may not accept this one: int, =, 5, ;. so its up to us to define a bullet proof grammar that tells us what is or isnt allowed in relation to how our language may be used. this can be achieved by using extended backus-naur form or EBNF for short.

* semantic analyser 
    > the job of the semantic analyzer is to, for example, implement type-checking for static typing or to make sure variables are not used before they are declared etc. this step can be thought of as "everything else we need to verify that the parser cant".

#### Translation phase
* ir generator
    > the ir generator is a form of structure that represents the code in a more basic form than a syntax tree, it is usually flat and sequential like an array. this stage is used for optimisation and various other touch-ups to the code before it is converted into platform specific assembly or some other lower-level form. IR is commonly represented as a platform agnostic kind of assembly code. for an example of a common format: https://en.wikipedia.org/wiki/Three-address_code

* optimiser
    > may perform some reorganisation of the IR to cut down redundancy or null operations. it may also implement some platform specific improvements.

* backend
    > the backend is the point in which we ship off our IR and forget about it, at this point all of the important work is over for making sure the source code is error free (for compile time anyway) and that the language hasnt been misused or abused. the backend can be some kind of VM that directly runs the IR or converts it into a more basic kind of IR. it could be passed onto a backend with LLVM too for example where we convert our IR into LLVM's IR and then compile and link to form an executable.