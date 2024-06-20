
# Mesa

A programming language that incorporates **imperative** code with **functional** constructs such as *combinators*.

Can be interacted with using a REPL, or by giving a `.mesa` file.

```bash
mesa                # enter repl with a blank environment

mesa -e test.mesa   # evaluate
mesa -r test.mesa   # enter repl
```

## Motivation

A big detractor to why most imperative programmers nowadays avoid or just
never consider combinatory logic as a viable pattern for their programs is
how higher-order functions are just that, functions that take functions.

Mesa aims to have combinators that are present in the language as *primitive*
constructs/keywords, while having most of the features modern imperative
languages provide.

## TODO

<!-- [[ TODO ]] {{{-->
<details>
<summary> TODO List </summary>

- [ ] Grammar
- [ ] Lexer
    - [ ] Arithmetic
    - [ ] Assignment
        - [ ] to variables
            - [ ] Integers
            - [ ] Doubles
            - [ ] Strings
            - [ ] Booleans
        - [ ] to tuples
        - [ ] to lists
    - [ ] Functions
        - [ ] One-line
            - [ ] Void
            - [ ] Parameterized
        - [ ] Multi-line
            - [ ] Void
            - [ ] Parameterized
        - [ ] Function Calls
    - [ ] Combines
        - [ ] I
        - [ ] K
        - [ ] KI
        - [ ] B
        - [ ] BB
        - [ ] Psi
        - [ ] Phi
        - [ ] S
    - [ ] Control Flow
        - [ ] If
        - [ ] Switch
        - [ ] Loops
            - [ ] For
            - [ ] While
- [ ] Parser
    - [ ] Arithmetic
    - [ ] Assignment
        - [ ] to variables
            - [ ] Integers
            - [ ] Doubles
            - [ ] Strings
            - [ ] Booleans
        - [ ] to tuples
        - [ ] to lists
    - [ ] Functions
        - [ ] One-line
            - [ ] Void
            - [ ] Parameterized
        - [ ] Multi-line
        - [ ] Function Calls
    - [ ] Control Flow
        - [ ] If
        - [ ] Switch
        - [ ] Loops
            - [ ] For
            - [ ] While
    - [ ] Combines
        - [ ] I
        - [ ] K
        - [ ] KI
        - [ ] B
        - [ ] BB
        - [ ] Psi
        - [ ] Phi
        - [ ] S
- [ ] Evaluator
    - [ ] Assignments
    - [ ] Function Calls
    - [ ] Combines
    - [ ] Error-handling
- [ ] REPL
    - [ ] Single-line input
    - [ ] Multi-line input
    - [ ] File input
        - [ ] -e mode
        - [ ] -r mode
- [ ] Unit-tests
    - [ ] Lexer
    - [ ] Parser
    - [ ] Evaluator
- [ ] Optimizations
    - [ ] Built-in functions
        - [ ] Type-casting
        - [ ] Length
        - [ ] Range
        - [ ] Map
            - [ ] Inplace
            - [ ] Copied
        - [ ] Fold
            - [ ] Left
            - [ ] Right
        - [ ] Scan
            - [ ] Left
            - [ ] Right
        - [ ] Reverse
            - [ ] Inplace
            - [ ] Copied
        - [ ] Sort
            - [ ] Inplace
            - [ ] Copied
        - [ ] Math
            - [ ] Abs
            - [ ] Floor
            - [ ] Ceil
            - [ ] Trigonometry
        - [ ] String
            - [ ] Uppercase
            - [ ] Lowercase
            - [ ] Chars
- [ ] Optional
    - [ ] Pattern-matching
        - [ ] Unpacking

</details>
<!--}}}-->

## Language Features
### Function Declaration
- Functions are treated as regular assignment.
- Arguments are comma separated with *optional* parentheses, and replaced with an `_` in the case of a `void` function.
- One-line functions implicitly return their final value.
- Multi-line functions require an explicit `return`.

```lua
-- Void one-line function
let hello = _ : print("Hello, world!")

-- Function with arguments
let add = (a, b) : a + b

-- Multi-line function
let sum_of_squares = (a) {
    let b = map(sqr, a)
    return sum(b)
}

-- Function call
hello()
add(2,3)

-- Anonymous function
(a, b : a)(3, 4)
```

### Precedence
The only precedence Mesa follows is:

1. Groups: Expressions using `( )`
2. Expressions: Right-to-left

This is done for clarity when it comes to long strings of function compositions.
### Combinator Constructs

> Also known as *Combines*.

| Combine Name | Inputs                    | Syntax         | Behavior                                                                                              |
| ------------ | --------------------------| -------------- | ----------------------------------------------------------------------------------------------------- |
| Identity     | 1 generic                 | `<_> a`          | Takes a value on its immediate right and returns it                                                   |
| Kestrel      | 2 generic                 | `a <<> b`        | Takes a left and a right value, returning the left                                                    |
| Kite         | 2 generic                 | `a <>> b`        | Takes a left and a right value, returning the right                                                   |
| Bluebird     | 2 1-fn                    | `f . g`          | Takes a left and a right *function*, applying the left after the right                                  |
| Blackbird    | 1 1-fn, 1 2-fn            | `f .: g`         | Takes a left and a right *function*, applying the left after the right                                  |
| Phi/Fork     | 1 2-fn, 2 1-fn, 1 generic | `f <g : h> a`    | Takes two *functions*, `g` and `h`, applies them to `a` separately, and then sends the result to `f` as inputs  |
| Psi/Both     | 1 2-fn, 1 1-fn, 2 generic | `a <f . g> b`    | Takes one *function*, `g`, applies it to both `a` and `b` separately, then sends the result to `f` as inputs    |
| Starling     | 1 2-fn, 1 1-fn, 1 generic | `f <_ : g> a`    | Applies `f` to `a` and the result of applying `g` to `a`                                                      |

Names of Combines can be abbreviated to the first letter of their names with the exceptions being:
- Kite : KI
- Blackbird: BB
- Phi: Phi  (a greek letter)
- Psi: Psi  (a greek letter)

Examples:

```lua
let divide = (a, b) : a / b

-- divide(sum(a), length(a))
let average = (a) : divide <sum : length> a
```

```lua
let equal = (a, b) : a == b

-- equal(a, reverse(a))
let is_palindrome = (a) : equal <_ : reverse> a
```

```lua
let equal = (a, b) : a == b

let abs = (a) {
    if a > 0 {
        return a
    }
    return a * -1
} 

let is_equal_magnitude = (a, b) : a <equal . abs> b
```
