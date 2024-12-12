# yon
Simple scripting language. Available. Accessible.
* Simplicity - basic set of functionality without complications.
* Availability - minimum possible set of motions required to run desired script.
* Accessibility - easy to learn, not hard to master. Everything is in plain sight.

## Introduction

## Getting started

## How-to guides

## Language reference
### Lexical analysis
Program is read by parser, line-by-line.

### Logical lines
Program is divided by set of logical lines. The end of a logical line is represented by NEWLINE token. Logical line can contain several statements, but no statement can cross boundaries of the logical line. Logical line is constructed from one or more `physical lines`, joined according to the `implicit line joining`.

### Physical lines
Sequence of characters terminated by an end-of-line sequence.

### Implicit line joining
Expressions in parentheses, square brackets or quotes can be split over more than one physical line without using backslashes:
```yon
clubs = [
    "Kreath City",
    "Blackberry United",
    "Morning Star",
]
longStory = "
Once upon a time
i've walked down
the street
"
```
Indentation in such cases is not important, except for the case of joining inside
quotes - all newline characters starting from first `"` are brought to the final string.

### Comments
Comments start with `//`. Yon does not have multiline comment constructions. Every comment set (one or several comment lines) above a logical line is considered a comment to that line, and can be fetched by documentation processing tools.

### Indentation
Leading tabs is used to compute the indentation level of the line, which in turn is used to determine the statements grouping. Only tabs are supported by default, assuming that the program code must use them instead of spaces, but to make programming in Yon more accessible, an effort to convert leading spaces to indentation tabs will be made, but if it's not possible, an `IndentationError` will be raised.

### Literals
Literals are notations for constant values of some built-in types.

* String: `"VALUE"`
    * Supports `escape sequences`
* Integer: `10`
* Float: `10.25`
* Operators: `+ - * ** / % > < >= <= == !=`
    * `%` serves for string as formatting, for integer and floats as modulo
* Delimiters: `( ) [ ] , = // "`

## Explanation
### Error handling
We use error codes. From any function, we can call `error CODE` to return an error. Error is represented by `struct Error`, which contains code, and stacktrace as well as other error information. An error can be logged at any time by calling statement `log VALUE`. Error codes are always >0. To be compliant with [orwynn] messaging, where errors are >0, OK is 0, and all negative values are OK messages. This may seem counterintuitive for orwynn, but well-known for errors, where >0 means any error in UNIX environment. In Yon, error codes in range 1-99 are reserved for the language errors, where code=1 is default Error.

The function return type remains the same, the fact of it being an error is enforced only on incorrent usage, for example:
```yon
fn hello() int
    error 1

// function could return an error, but at this point we accept intention of `a` having a sole type of `int`
a = hello()

// if `a` is an error, and it is enforced in any operations which error does not support - like addition in
// this example, it is returned above the stack. If such happens at the top of the program - the panic is issued.
b = a + 2
```
If incorrect usage happens with other types, IncorrectUsageError code is issued.


