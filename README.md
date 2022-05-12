# Delta

**Summary**:
A program is compiled into a CFG.
The language distinguishes itself by fully exposing the CFG construction process, and inviting the language user to be an active participant in it.
Built-in control-flow keywords (such as `if` and `while`) are actually macros from the core library, providing an example of this participatory process.
The general-purpose `loop` (also a macro) provides a rich toolkit for building custom loops.

## Worked example: binary search

tbd: Show how to build this one as a CFG of suites.

```
enum NotFound (NOT_FOUND)

func search(array: Array<Int>, target: Int): Int | NotFound {
    let low = 0;
    let high = array.length;

    while (low < high) {
        let mid = (low + high) // 2;

        if (array[mid] >= target) {
            high = mid;
        }
        else {
            low = mid + 1;
        }
    }

    return array.length > 0 && array[low] === target
        ? low
        : NOT_FOUND;
}
```

## Language syntax

Statements:

```
c = a ** 2 + b ** 2;                          Expression statement

if <expr> { <stmt>... }
    ... else if <expr> { <stmt>... }          If statement
    ... else { <stmt>... }

while <expr> { <stmt>... }                    While loop

repeat { <stmt>... } while <expr>;            Repeat-while loop (called "do-while" in C)

for <expr> -> <var> { <stmt>... }             For loop

loop { <lclause>... }                         General-purpose loop (described in detail later)

func <identifier> (<paramlist>) { <stmt>... } Function declaration

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

## The `if`, `for`, `repeatWhile` and `while` functions

These four end up being mutually recursive, although in a way that bottoms out on the syntax.
You can also spot from these definitions that `if` is all about conditionally jumping forwards, whereas `repeatWhile` is all about jumping backwards.

```
func if(
    @parse(/ "if" <expr> <block> ["else" "if" <expr> <block>]* /)
    condBlockPairs: Array<(() => Bool, () => ())>,

    @parse(/ ["else" <block>]? /)
    elseBlock = parseDefault(quote { else {} }),
) {
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

    elseBlock();
}

func repeatWhile(
    @parse(/ "repeat" <block> /),
    body: () => (),

    @parse(/ "while" <expr> ";" /),
    cond: () => Bool,
) {
label iterate:
    body();
    if cond() {
        jump iterate;
    }
}

func while(
    @parse(/ "while" <expr> /)
    cond: () => Bool,

    @parse(/ <block> /)
    body: () => (),
) {
    if cond() {
        repeat {
            body();
        } while cond();
    }
}

func for<T>(
    @parse(/ "for" <decl> /)
    iterVar: Decl<T>,
    
    @parse(/ "in" <expr> /)
    array: Array<T>,

    @parse(/ <block> /)
    body: (e: T) => (),
) {
    let i = 0;
    while i < array.size() {
        let element = array[i];
        body(element);
    }
}
```
