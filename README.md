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

for <decl> in <expr> { <stmt>... }

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
x = <expr>                                    Assignment
x += <expr>                                   Assignment metaoperators

f(<arg>...)                                   Function call
```

## The `if` and `while` macros

These three end up being mutually recursive, although in a way that bottoms out on the syntax.
You can also spot from these definitions that `if` is all about conditionally jumping forwards, whereas `while` is all about jumping backwards.

```
mac if(condBlockPairs: Array<(() => Bool, () => ())>, elseBlock?: () => ()) {
    for let (cond, block) in condBlockPairs {
        let b = cond();
        jumptable b (
            true => matched,
            false => nextIter,
        );
label matched:
        block();
        return;
        
label nextIter:
    }

    elseBlock?();
}

mac while(cond: () => Bool, body: () => ()) {
label iterate:
    if cond() {
        body();
        jump iterate;
    }
}

mac for<T>(array: Array<T>, body: (e: T) => ()) {
    let i = 0;
    while i < array.size() {
        let element = array[i];
        body(element);
    }
}
```
