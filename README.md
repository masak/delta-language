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

if <expr> { <stmt>... }
    ... else if <expr> { <stmt>... }          If statement
    ... else { <stmt>... }

while <expr> { <stmt>... }                    While loop

loop { <lclause>... }                         General-purpose loop (described in detail later)

func <identifier> (<paramlist>) { <stmt>... } Function declaration

mac <identifier> (<paramlist>) { <stmt>... }  Macro declaration

enum <identifier> (<member>...)               Enum declaration

label <identifier>;                           Jump target

goto <identifier>;                            Jump statement
```

Expressions:

```
-5                                            Int
"OH HAI"                                      Str
false                                         Bool
(42, "OH NOES", true)                         (Int, Str, Bool)      Tuple
func (<paramlist>) { <stmt>... }              (...) => ...          Func
mac (<paramlist>) { <stmt>... }               (...) => ...          Mac

a + b                                         Unary and binary arithmetic
                                              (Imagine a complete set here)

b1 && b2                                      Boolean operators
b ?? <e1> !! <e2>                             ...including Raku's ternary

let x = <expr>                                Variable declaration (a term, like in Alma and Raku)

f(<arg>...)                                   Function call
```
