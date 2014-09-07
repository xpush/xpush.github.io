---
title: "Our own little computer language"
description: "A tutorial to writing our own programming language"
layout: post
tags: programming
category: languages
comments: yes
---

Let's write our own programming language. It'll be fun and we will learn some key tools for processing structured data, performing semantic analysis, how to make a computer execute code, and all this using modern JavaScript.

We will create five major components, each outlined in a separate article:

1. Lexical analyzer
1. Semantic parser
1. Code generator
1. Management and support
1. Runtime library

Today we will actually do two things: Define a first version of our language' lexicon, and write a lexical analyzer—or _lexer_—which performs _lexical analysis_ of our language. From [Wikipedia](http://en.wikipedia.org/wiki/Lexical_analysis):

> lexical analysis is the process of converting a sequence of characters into a sequence of tokens. A program or function which performs lexical analysis is called a lexical analyzer, lexer, or scanner.

Our _lexer_ will understand the basic building blocks of our language, similar to what a word and punctuation are in natural language text. Naturally we need to decide which these words and punctuation are. I think this is one of the most fun parts of writing your own language, as we are completely free to design our language.

A lexer is usually the first part in the _evaluation pipeline_:

_Source code → Lexer → Parser → Code generator → Execution_

## Language lexicon and syntax

Let's formulate the basic building blocks of our language. Before we write anything, let's take a minute and think about what kind of language we wish to design. We don't have to commit to anything yet, but it will be easier for us to make progress if we have an idea of what we are trying to accomplish.

If we have the answers to the following to questions, our work will be easier.

1. Are linebreaks significant?
2. Is leading whitespace significant?

A language like C does not care about whitespace at all, except for separating keywords, whilst a language like Python cares about both linebreaks and leading whitespace. Let's say "yes" to both these questions. Our language might care about both linebreaks and leading whitespace.

### Syntax

Here's a [BNF](http://en.wikipedia.org/wiki/Backus%E2%80%93Naur_Form)-style sketch of the language I have in mind for this tutorial. Feel free to make up your own version.

    #!-none
    Expression           = ExpressionGroup | Number | Text | Path | List | Map
    ExpressionGroup      = "(" Expression* ")"
    Line                 = Linebreak Space*

    Whitespace           = Linebreak | Space
    Linebreak            = U+000A..U+000D | U+0085 | U+2028 | U+2029
    Space                = U+0009 | U+0020 | U+00A0 | U+180E | U+2000..U+200B
                         | U+202F | U+205F | U+3000 | U+FEFF

    Number               = IntegerNumber | HexIntegerNumber | FractionalNumber
    IntegerNumber        = DecimalDigit+
    DecimalDigit         = 0..9
    HexIntegerNumber     = "0x" HexDigit+
    HexDigit             = 0..9 | A..F | a..f
    FractionalNumber     = DecimalDigit+ "." DecimalDigit* FractionExponent?
    FractionalExponent   = ("E" | "e") ("-" | "+")? DecimalDigit+

    Text                 = "'" TextCharacter* "'"
    TextCharacter        = U+0021..U+0026
                         | U+0028..U+005B
                         | "\" TextEscCharacter
                         | U+005D..U+FFEE
    TextEscCharacter     = "\" | "'" | "t" | "n" | "r" | UnicodeValue
    UnicodeValue         = "u" HexDigit{4} | "U" HexDigit{8}

    Path                 = Symbol ("." Symbol)*
    Symbol               = <SymbolCharacter except 0..9> SymbolCharacter*
    SymbolCharacter      = <VisibleCharacter except SingleTerminator>
    VisibleCharacter     = <UnicodeCharacter except Whitespace | ControlCharacter>
    UnicodeCharacter     = U+0000..U+FFFE
    ControlCharacter     = U+0000..U+0020 | U+007F..00A0 | U+FFEF..U+FFFF
    SingleTerminator     = "(" | ")" | "." | ":" | "[" | "]" | "{" | "}"

    List                 = "[" Expression* "]"

    Map                  = "{" MapPair* "}"
    MapPair              = MapKey ":" Expression
    MapKey               = Symbol | Text

This format, usually referred to as BNF (although not actual strict BNF), is a way of expressing a syntax in terms of simple reductions. A quick guide to general BNF format:

- `Foo = Bar | Baz` — "Foo" means either exactly one Bar or exactly one Baz.
- `Bar?` — Zero or one "Bar" (a trailing "?" means "optional")
- `Bar*` — Zero or more "Bar" in succession
- `Bar+` — One or more "Bar" in succession
- `Bar{4}` — Exactly 4 "Bar" in succession
- `A..F` — Exactly one of the items in the natural sequence (here, A, B, C, D, E or F)
- `"Bar"` — The literal "Bar"
- `(A | B) C` — "(" and ")" groups an anonymous reduction, here meaning "one of A or B, then one C"
- `U+2192` — A Unicode character value (in this case the RIGHTWARDS ARROW codepoint)
- `<Foo except A and C>` — Simplification of a reduction of "Foo". Generally not recommented but useful in early stages of design.

The above language is definitely incomplete (no functions or operators), but is a great start and to be honest it's more than we need to get started. It would allow source code like this:

    Hello = (bar baz) ->
      ה = can-haz [bar 'bar bara']
      54.8 * ה

The first step to interpreting this code is to perform _lexical analysis_. As discussed earlier this means that a stream of bytes is the input and a stream of tokens is the result. Given our partial language specification, the above source should produce the following tokens:

`Symbol("Hello")`,
`Symbol("=")`,
`LeftParen`,
`Symbol("bar")`,
`Symbol("baz")`,
`RightParen`,
`Symbol("->")`,
`Line(with 2 spaces)`,

`Symbol("ה")`,
`Symbol("=")`,
`Symbol("can-haz")`,
`LeftSquareBracket`,
`Symbol("bar")`,
`Text("bar bara")`,
`RightSquareBracket`,
`Line(with 2 spaces)`,

`FractionalNumber("54.8")`,
`Symbol("*")`,
`Symbol("ה")`,
`Line(with 0 spaces)`

With our current language lexicon and syntax we are able to formulate more complex structures that the above example, for instance:

    Text.join = (list-of-text glue) ->
      result = nil
      for text in list-of-text
        result = if (result == nil) text
                 else result + glue + text
      result

    (print (Text.join ['Yet another' 'Hello' 'world'] ' '))

However, let's stick to the previous simpler example for now.

This is an excellent time to take a break and play around with some different imaginary language syntax. Remember that the _lexer_ only needs to know about _what a word is_ not _all the possible words_ or even the sequence of words. When I say "words" here I mean the smallest building blocks of the syntax, or _tokens_ as they are usually called. Let's call each small unit of meaning a "token" from here on forward.

## Behold, a lexer

Remember the _evaluation pipeline_?

_Source code → Lexer → Parser → Code generator → Execution_

Parsing and dealing with code is inherently a flowing system with no specific beginning or end. Sure, if we read the source from a file the file will have a start and it will have an ending, but let's write a pipeline that is universal and has _incremental_ functionality. In contrary to traditional _batch_ lexers, an _incremental_ lexer is able to iteratively analyze a continuous stream of characters and with little delay produce tokens on-the-fly. The tokens produced by our lexer will even be able to retain a persistent "snapshot" of the lexer's state, for the abilitly to perform an incremental modification from a certain point. However, our lexer will merely make this possible but not directly provide this functionality.

Given these requirements of being able to pause, resume and change source as we are producing tokens, there are not many implementation approaches available to us. In a language that has functions with local variables (usually implemented with a stack) a _batch lexer_ can be efficiently implemented with a function-based _recursive decent_ approach, where each token reduction (see our BNF-style language definition) is represented by a function that calls another function depending on the source character at hand.

Here is pseudo-code implementing a recursive decent approach (this particular one is actually not recursive, but could very well become so), able to handle the `Text` part of our syntax:

    class Lexer:
      next_char = nil
      input = ""
      input_offs = 0

      def ReadToken():
        return this.ReadExpression()

      def ReadExpression():
        if this.next_char == "'":
          this.ReadNextChar() # Consume "'"
          return this.ReadText()
        else if ...

      def ReadText():
        token = {type = TEXT, value = ""}
        prev_was_escape = false
        while this.next_char:
          if prev_was_escape:
            prev_was_escape = false
            token.value = concat(token.value, this.next_char)
          else if this.next_char == "\\":
            prev_was_escape = true
            continue
          else if this.next_char == "'":
            break
          else:
            token.value = concat(token.value, this.next_char)
        this.ReadNextChar() # Consume "'"
        return token

      def ReadNextChar():
        this.next_char = this.input[this.input_offs++]

    L = Lexer()
    L.SetInput("'Hello world'")
    L.ReadToken()  # -> {type=TEXT, value="Hello world"}

This works fine for source input that is provided as one continuous sequence.
If we were to pause a batch lexer like this, it would either imply a very complex and probably inefficient code which records and stuffs away the current call stack (essentially co-routines or green threads), or abort and retry from the beginning again later.

Say the source `'Hello world'` arrives in two chunks and we try to apply the above batch lexer:

    1. SetInput("'Hello wo")
    2. ReadToken()  # -> nil
    3. # Some time passes...
    4. SetInput("rld'")
    5. ReadToken()  # -> something not a string

When `ReadText` is called, `next_char` will be `nil` before the loop sees a terminating "'" character, and since all state is kept on the stack it will be lost when the function returns. Thus the second call at line 4 and 5 will begin reading with a reset state. No cigar.

So we realized that the state of the lexer must be held by the lexer itself and in a way that can be represented persistently (e.g. as data in memory).

Looking again at the pseudo code above for a batch lexer, we will look at what actually happens by unrolling the code:

1. The lexer is asked to read the next token
1. At this point the lexer has no context so the only thing it can do is to parse an "Expression". Calling the "ReadExpression" function creates a new stack call entry. This is the state of what we are reading.
1. Okay, so we are reading an expression. Since we have no previous information about where in an expression we are, let's look at the current character of the source code (it's "'")
1. Since the character is a "'" we know that a "Text" token is coming up. Call "ReadText" to enter a state of "reading text".
1. Create a new token and store it into the variable `token` that is kept on the stack. We need to keep track of the previous character since "\c" and "c" means different things. This is also kept as a variable `prev_was_escape` living on the stack (or in the function's scope, depending on the language we write this in, but whatever).
1. Look at the next character, look at `prev_was_escape` and append `next_char` to the token value as we read.

Essentially each step is a certain code branch that flow into other code branches depending on certain branch-related values. So let's move those values into a "lexer context" object which we instead pass around between readings.

Let's rewrite the previous batch lexer using this strategy.

    def Lexer():
      return {input = "", input_offs = 0, token = nil, next_char = nil}

    def ReadToken(L):
      result = nil
      while L.next_char:
        if L.token == nil:
          # Entry state
          if L.next_char == "'":
            L.token = {type = TEXT, value = "", prev_was_escape = false}
            ReadNextChar(L) # Consume "'"
          else:
            ...
        else if L.token.type == TEXT:
          if L.token.prev_was_escape:
            L.token.prev_was_escape = false
            token.value = concat(token.value, L.next_char)
          else if L.next_char == "\\":
            L.token.prev_was_escape = true
          else if L.next_char == "'":
            result = L.token
            delete result.prev_was_escape
            L.token = nil
          else:
            token.value = concat(token.value, L.next_char)
          ReadNextChar(L)
        else:
          ...
      return result

    L = Lexer()
    SetInput(L, "'Hello wo")
    ReadToken(L)  # -> nil
    SetInput(L, "rld'")
    ReadToken(L)  # -> {type=TEXT, value="Hello world"}

We simply moved the state into a structure (referenced to as `L` above) which we pass around instead of storing the state values on the stack. The function calls were replaced by logical code branches. The `token` structure above is used to carry token code-branch specific state. This design not only performs better (in most languages) but it also meets our requirements of being able to be paused and resumed.

### A JavaScript implementation

For this tutorial article series I've chosen JavaScript as it's easily accessible, a very popular language, has built-in Unicode support (good since we are to be analyzing text) and runs almost anywhere.

Before writing or running any code, install Node.js — a free, modern and efficient JavaScript environment that let's you run JavaScript programs in a terminal session (just like Python, Ruby, Perl or any other regular program). It takes just a minute to download and install, so don't hesitate: [nodejs.org/download](http://nodejs.org/download/).

Now, let's have a look at the lexer. It's easiest if you [clone or fork the Git repository](https://github.com/rsms/prog-lang-tutorial.git) at <https://github.com/rsms/prog-lang-tutorial> and have a look in the "01-lexer" directory. There is a file called _[lexer-demo.js](https://github.com/rsms/prog-lang-tutorial/blob/master/01-lexer/lexer-demo.js)_ — a simple program that imports our lexer and applies some sample source. Essentially it imports the Lexer module and does something like this:

    var L = Lexer(), token;
    L.appendSource('Foo (bar baz)');
    while ((token = L.next(source_ended))) {
      console.log(Lexer.TOKEN_NAMES[token.type], token);
    }

The interesting part is in the _[lib/mylang/Lexer.js](https://github.com/rsms/prog-lang-tutorial/blob/master/01-lexer/lib/mylang/Lexer.js)_ file, so let's quickly look through the different sections of the Lexer code.

    // Create a new Lexer object.
    function Lexer() {
      return Object.create(Lexer.prototype, ...

This function creates new Lexer objects with initial state. We call this function each time we start reading a new source stream.

Next up is a set of token definitions. This is where we define each of the different token types that our language is constructed from.

    var LINE                 = deftok('LINE');
    var LEFT_PAREN           = deftok('LEFT_PAREN',           '(');
    var RIGHT_PAREN          = deftok('RIGHT_PAREN',          ')');
    var LEFT_SQUARE_BRACKET  = deftok('LEFT_SQUARE_BRACKET',  '[');
    var RIGHT_SQUARE_BRACKET = deftok('RIGHT_SQUARE_BRACKET', ']');
    var LEFT_CURLY_BRACKET   = deftok('LEFT_CURLY_BRACKET',   '{');
    var RIGHT_CURLY_BRACKET  = deftok('RIGHT_CURLY_BRACKET',  '}');
    var FULL_STOP            = deftok('FULL_STOP',            '.');
    var COLON                = deftok('COLON',                ':');
    var DECIMAL_NUMBER       = deftok('DECIMAL_NUMBER');
    var HEX_NUMBER           = deftok('HEX_NUMBER');
    var FRACTIONAL_NUMBER    = deftok('FRACTIONAL_NUMBER');
    var SYMBOL               = deftok('SYMBOL');
    var TEXT                 = deftok('TEXT');

The `deftok` helper function simply assigns the token a unique number identifier and if a second argument is given, associates that token id with a single character value (this is stored in the internal map `SINGLE_TOKENS`). The Lexer will be able to look up a character value in this map and if it's there, immediately terminate any currently reading token. This is useful for single-character tokens which might appear immediately before or after for instance a symbol.

The Lexer prototype is the prototype of the object returned by the `Lexer()` function:

    Lexer.prototype = {

`appendSource` is used to set/append source input:

      // Add input source.
      // The `chunk` object need to have the following properties:
      //
      //  .charCodeAt(N)   A function that return the Unicode character value at
      //                   position N, or a NaN if N is out of bounds.
      //
      //  .substring(A, B) A function that returns another chunk object that
      //                   represents the Unicode characters in the range [A,B)
      //
      appendSource: function (chunk) { ...

`makeToken` is mean to be used only internally by other Lexer functions. This is called every time a new token is being read:

      // Create a new token of `type` based on the internal source location state
      makeToken: function (type) { ...

`flushToken` is also meant as an internal function which is called each time a token is about to be returned as a result from the `next` function:

      // Returns the current token and clears the "reading token" state. This is
      // an appropriate place to perform some post processing on tokens before
      // they are returned to the caller of `next()`. If `terminated_by_eos` is
      // true, the token was terminated by end of source.
      flushToken: function (terminated_by_eos) { ...

`next` is called to give control to the lexer and returns either when the lexer has read a token (a token is returned), when an error occurs (an exception is thrown) or the input source ended (null is returned).

      // Reads the next token. If `source_ended` is true, then no more source is
      // expected to arrive and thus EOS acts as a token terminator.
      next: function (source_ended) { ...

That's it for a summary of the Lexer design. I suggest you look through the code which is thoroughly documented.

I've left one part unimplemented for you to write: Reading Text literals. Run the `lexer-demo.js`:

    #!-none
    $ node lexer-demo.js
    read_tokens(L)
    L.next() -> SYMBOL 'Hello' at 0:0
    L.next() -> SYMBOL '=' at 0:6
    ...
    Error: Lesson 1: Implement reading of Text tokens
    ;)

Again, the source is available at <https://github.com/rsms/prog-lang-tutorial>.

In the next article we will look at how semantic parsing fits into the evaluation pipeline.
