# yon
Simple scripting language. Available. Accessible.
* Simplicity - basic set of functionality without complications.
* Availability - minimum possible set of motions required to run desired script.
* Accessibility - easy to learn, not hard to master. Everything is in plain sight.

## Introduction

## Getting started

## How-to guides

## Language reference
#### Lexical analysis
Program is read by parser, line-by-line.

#### Logical lines
Program is divided by set of logical lines. The end of a logical line is represented by NEWLINE token. Logical line can contain several statements, but no statement can cross boundaries of the logical line. Logical line is constructed from one or more `physical lines`, joined according to the `implicit line joining`.

#### Physical lines
Sequence of characters terminated by an end-of-line sequence.

#### Implicit line joining
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

#### Comments
Comments start with `//`. Yon does not have multiline comment constructions. Every comment set (one or several comment lines) above a logical line is considered a comment to that line, and can be fetched by documentation processing tools.

#### Indentation
Leading tabs is used to compute the indentation level of the line, which in turn is used to determine the statements grouping. Only tabs are supported by default, assuming that the program code must use them instead of spaces, but to make programming in Yon more accessible, an effort to convert leading spaces to indentation tabs will be made, but if it's not possible, an `IndentationError` will be raised.

#### Literals
Literals are notations for constant values of some built-in types.

* String: `"VALUE"`
    * Supports `escape sequences`
* Integer: `10`
* Float: `10.25`
* Operators: `+ - * ** / % > < >= <= == !=`
    * `%` serves for string as formatting, for integer and floats as modulo
* Delimiters: `( ) [ ] , = // "`

### Data model
All data in a Yon program is represented by objects and their relations. Every object can be a primitive or a container.

#### Primitives
Represented directly at the lowest level of the language.
* `number`
* `string`
* `boolean`
* `null`

#### Containers
Collection of references to other objects.
* `array`
* `map`
* `function` - special case of `map`, accepting input, having instruction set and giving output.

### Contracts
Yon programming language does not have custom types aside from built-in primitives adn containers, but it has `contracts`. A contract is an agreement of required fields and their values that an object should have to be considered compliant to the contract. There is a special contract - `enum` which defines a set of integer numbers which can be a sole value of an object compliant to the contract.

Contract compliance is checked with keyword `is`: `OBJECT is CONTRACT` will return `true` if `OBJECT` has all fields that `CONTRACT` defined as well as correct built-in types of values for them.

### Execution model
A Yon program is constructed from code blocks. A block is a piece of Yon program text that is executed as a unit. Blocks: module, function body. A block is executed line-by-line.

#### Naming and binding
Every object can be bind to a name:
* by assignment operator `=`: either inline or in the map
* by function definition operator `function`
* as an argument to a function
* by `import` operator as part of imported module

#### Scopes
Every block has own `local scope` - map of available in the scope objects and their assigned names. Every block can also access `global scope` - map of objects defined in parent blocks (most recent names overwrite older ones). Local scope has access priority over global scope.

### Import system
Yon program is organized in `modules`. A module is a `code block` contained within a single program file or represented by an executing environment (e.g. REPL). Every module has a name under which it can be accessed. The name for a module is defined by a filename, or as `main` in case of executing environment.

Module can import other modules using `import NAME` statement. The name is searched this way: `.y` files are searched in the executing module's directory (and all subdirectories recursively). In case of executing environment, the current working directory is considered as a starting point.

After importing a module, all it's defined objects are accessible via notation: `module.SomeObject`. Only objects which name starts with a capital letter are available in such access, for other cases `PrivateAccessError` is issued.

### Expressions
* literals (atoms)
* names
* array: `[obj1, obj2, obj3]`
* array subscription (access by index): `array1[0]`
* map: `(Name="Mark", Age=25)`
* map attribute reference: `mapname.Attribute1` - only attributes starting with a capital letter can be accessed
* function call: `someFunction(arg1, arg2, arg3)`
* arithmetic: `+ - * / ** %`
* formatting: `%`
* comparison: `> < >= <= == !=`
* conditional: `and or not`

### Simple statements
* `NAME = VALUE` - assignment
* `return VALUE` - return a value from a function body
* `error CODE` - return an error from a function body
* `break` - interrupt loop
* `continue` - continue to the next iteration of a loop
* `import NAME` - imports a module
* `log VALUE` - logs a value in the best possible format
* `input` - wait for user CLI input
* `call FFI_NAME arg1 arg2 arg3` - call FFI

### Compound statements
* `if CONDITION`, `elif`, `else` - conditional execution
* `while CONDITION` - repeated execution as long as `CONDITION` is true
* `for TARGET in ITERABLE` - loop through `ITERABLE`
* `function NAME(...args) RETURNVALUE \n\t BODY` - function definition
* `contract NAME \n\t BODY` - contract definition
* `enum NAME \n\t BODY` - enum definition

## Explanation
### Error handling
We use error codes. From any function, we can call `error CODE` to return an error. Error is represented by `contract Error`, which contains code, and stacktrace as well as other error information. An error can be logged at any time by calling statement `log VALUE`. Error codes are always >0. To be compliant with [orwynn] messaging, where errors are >0, OK is 0, and all negative values are OK messages. This may seem counterintuitive for orwynn, but well-known for errors, where >0 means any error in UNIX environment. In Yon, error codes in range 1-99 are reserved for the language errors, where code=1 is default Error.

The function return type remains the same, the fact of it being an error is enforced only on incorrent usage, for example:
```yon
function hello() int
    error 1

// function could return an error, but at this point we accept intention of `a` having a sole type of `int`
a = hello()

// if `a` is an error, and it is enforced in any operations which error does not support - like addition in
// this example, it is returned above the stack. If such happens at the top of the program - the panic is issued.
b = a + 2
```
If incorrect usage happens with other types, IncorrectUsageError code is issued.


