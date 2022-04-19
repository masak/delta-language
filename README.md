# Delta

**Summary**:
A program is compiled into a CFG.
The language distinguishes itself by fully exposing the CFG construction process, and inviting the language user to be an active participant in it.
Built-in control-flow keywords (such as `if` and `while`) are actually macros from the core library, providing an example of this participatory process.
The general-purpose `loop` (also a macro) provides a rich toolkit for building custom loops.

## Language syntax

Statements:

```
c = a ** 2 + b ** 2;                          Expression statement

if <expr> { <stmts> }
    ... else if <expr> { <stmts> }
    ... else { <stmts> }                      If statement

while <expr> { <stmts> }                      While loop

loop { <lclauses> }                           General-purpose loop (described in detail later)

func <identifier> (<param>...) { <stmts> }    Function declaration
```

Expressions:

```
a + b                                         Unary and binary arithmetic
                                              (Imagine a complete set here)

b1 && b2                                      Boolean operators
b ?? <e1> !! <e2>                             ...including Raku's ternary

let x = <expr>                                Variable declaration (a term, like in Alma and Raku)

f(<arg>...)                                   Function call
```
