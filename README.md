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

return <expr>;                                Return statement (expr optional if empty tuple)

enum <identifier> (<member>...)               Enum declaration

label <identifier>:                           Jump target

jump <identifier>;                            Jump statement
jumptable <expr> (<jclause>...);              Jump table
fallthrough;                                  No-op -- allowed before a label for clarity
```

Expressions:

```
-5                                            Int
"OH HAI"                                      Str
false                                         Bool
(42, "OH NOES", true)                         (Int, Str, Bool)      Tuple
[1, 2, 3]                                     Array<Int>            Array
func (<paramlist>) { <stmt>... }              (...) => ...          Func
mac (<paramlist>) { <stmt>... }               (...) => ...          Mac

a + b                                         Unary and binary arithmetic
                                              (Imagine a complete set here)

x == y                                        Equality, comparison

b1 && b2                                      Boolean operators
b ?? <e1> !! <e2>                             ...including Raku's ternary

let x = <expr>                                Variable declaration (a term, like in Alma and Raku)

f(<arg>...)                                   Function call
```

## The `if` and `while` macros

```
mac if(
    cond: () => Bool,
    thenBlock: () => (),
    elseIfs: Array<(() => Bool, () => ())>,
    elseBlock?: () => ()
) {
label iterate:
    let c = cond();
    jumptable c (
        true => trueLabel,
        false => elseIfsLabel,
    );

label trueLabel:
    thenBlock();
    jump done;

label elseIfsLabel:
    if elseIfs.size() == 0 {    # the macro recursion here is well-founded
        (cond, thenBlock) = elseIfs.shift()
        jump iterate;
    }
    fallthrough;

label falseLabel;
    if elseBlock !== undefined {
        elseBlock();
    }
    jump done;

label done;
}
```

```
mac while(
    cond: () => Bool,
    body: () => (),
) {
label iterate:
    let c = cond();
    jumptable c (
        true => doBody,
        false => done,
    );

label doBody:
    body();
    jump iterate;

label done;
}
```
