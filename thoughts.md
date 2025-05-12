# Design

1. Interpreted imperative language with dynamic grammar
2. Support for inline assembly and ability to call functions on interrupts
3. Minimal standard library / binary bootstrap. Language constructs like FOR loops are 
defined in Tony.
4. It will be very inefficient, so the only way to make it work well would be
to include good support for concurrency, e.g. Erlang-like coroutines
5. Should encourage event-driven approach suitable for interactive programs
6. In essence, in runtime Tony is nothing more than a large dict of lists
7. C-like types. Simple Pascal-like strings, no UTF.
8. Nested calls will be problematic and likely not supported. Replace with sequential
calls, referring to e.g. `update result`.
9. We might need to support "calls" and "accessors" semantics by chaining nouns, e.g. 
`Play alarm sound`, `Print alarm sound duration`, ``.
10. Text in brackets is ignored as `(comments)`. All other punctuation, including quotes, 
is ignored.
11. Symbols are lists, LISP-like.

Tech. diff -- language grammar, including regexes, needs to be additive, i.e. recompile 
incrementally. Implement a "dynamic regex compiler", which would support "cheap" frequent 
updates and very large regexes with lots of ORs.

# Built-ins

1. `Define <pattern>: ...` - defines new pattern. Can be used for creating
keywords, defining new types, etc.
2. `Evaluate <symbol>` - evaluates expression -- calls a function, or all expressions
in a list.
3. `Assembly <asm>` - runs an assembly snippet.

Misc.

```
Call function: <Verb> <noun>, e.g. Minimize window
Access member: <Noun1> <noun2> ... <nounN>, e.g. Window width

Define (pattern) "When <expression> is <symbol>":
    (E.g. "When close is clicked")

Lists and executable expressions: 
    <el1>, 
    <el2>, 
    <el3>. (for functions elements are expressions)

```

# Arithmetics

```
Define <a: [0-9]+> + <b: [0-9]+>:
    Assembly:
        mov a, ax
        mov b, bx
        add bx, ax
        mov bx, result
```

# Function

```
Define <verb> <noun>:
    Assembly:
        mov a, ax
        mov b, bx
        add bx, ax
        mov bx, result
```

Return values can be accessed via `<verb> result`.

Local lexical scoping.

Local symbols from `<noun>` can be accessed even if they are not defined in the global scoope,
e.g. `When window is closed: ...` -- `window` is an object / noun, `when` is a function / verb,
`closed` is a parameter. In this case `closed` is a constant defined on the `window` object.

Prepositions are ignored for readability, i.e. `as`, `is`, `to`, `from`, `by`, `in`, `of`.

# Window system examples

```
Declare:
    Button "close" (to exit the entire application),
    Label "header title".

When close is clicked:
    Close window,
    Exit program with code 0.

When pomodoro duration value is changed:
    Set settings > pomodoro duration to new value,
    Save settings.

When tick timer is fired:
    (re)schedule tick timer in 5 seconds,
    declare string "new title",
    if pomodoro timer is ticking: 
        assign "Work {format pomodoro timer elapsed time as mm:ss}" to new title.
    else if pomodoro timer is resting: 
        assign "Rest {format pomodoro timer elapsed time as mm:ss}" to new title.
    else:
        assign "Idle" to new title.,
    set header title value to new title.
```
