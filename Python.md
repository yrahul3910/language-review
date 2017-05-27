# Basics
## Getting Started
* REPL - Read-Eval-Print-Loop
* In Python 3, // is the integer division operator
* Indentation is significant and is usually 4 spaces. Mixing spaces and tabs is not allowed since Python 3.
* Python Enhancement Proposals (PEPs) are guides for writing good code. Important ones are PEP8 (Python Style Guide), and PEP20 (The Zen of Python), which can be viewed by typing `import this`.
* Importing modules can be done in 3 ways:
 * `import module`
 * `from module import function`
 * `from module import function as alias`
* `help` gives help about modules and functions by reading the docstrings.
* Built-in scalar types: int, float, None, bool
* Conditional Statements: if...elif...else
* `input` is used to read input from the console.

## Strings and Collections
* Adjacent string literals are concatenated. Single or double quotes can be used.
* Raw strings suppress the escaping mechanism. These are written as `r" string "`
* Multiline strings are written using 3 quote characters.
* `byte` is a sequence of bytes, `str` is a sequence of Unicode codepoints.
* `byte` literals are prefixed with a b.
* Strings can be converted to bytes with `encode()`, and the reverse with `decode()`.
* Lists are mutable, heterogeneous sequences of objects.
* Square bracket assignments replace objects.
* You can grow lists with `append()`
* Dictionaries associate keys with values. Syntax: `{ k1: v1, k2: v2 }`

## Modularity
* Code is placed in .py files called modules
* They can be executed using import statements.
* Named functions can be defined with the `def` keyword.
* Omitted return parameter or implicit return at the end returns `None`
* Use `__name__` to determine how the module is being used.
* If `__name__ == '__main'`, then the module is being executed.
* Module code is executed only once on the first import.
* `def` is a statement that binds a function definition to a name.
* Command-line arguments can be accessed using `sys.argv`. The script filename is `sys.argv[0]`.
* Docstrings are a standalone literal as the first statement of a function or a module. They are delimited by triple quotes.
* Module docstrings should precede other statements.
* Comments begin with a `#`.
* A special comment on the first line beginning with `#!` called the shebang controls module execution by the program loader.

## Objects
* Think of named references to objects rather than variables
 * Assignment attaches a name to an object
 * Assigning from one reference to another puts two name tags on the same object
* The GC reclaims unreachable objects
* This `is` operator determines equality of identity
* Function arguments are passed by object reference
 * Functions can modify mutable arguments
* Reference is lost if a formal function argument is rebound
 * To change a mutable argument, change its contents
* `return` also passes by object reference
* Function arguments can be specified with defaults
* Python uses dynamic typing (we don't specify types in advance), and strong typing (types are not coerced to match)
* Names are looked up in 4 nested scopes: LEGB (local, enclosing, global, built-ins)
* Global references can be read from a local scope.
* Use `global` to assign to global references from local scope
* `import` and `def` result in binding to named references
* `type` can be used to check the type of an object
* `dir()` can be used to introspet object and get its attributes
* The name of a function or object can be accessed through its `__name__` attribute
* The docstring of a function or object can be accessed through its `__doc__` attribute
* Use `len()` to measure length of a string
* You can multiply a string with an integer

## Collections
Tuples are immutable sequence types
 * Literal syntax: Optional parentheses around a comma-separated list
 * Single element tuples must use trailing comma
 * Tuple unpacking--return values and idiomatic swap
* Strings are immutable sequences of Unicode codepoints
 * Concatenation most efficiently performed using join on an empty separator
 * The `partition()` method is a useful and elegant string parsing tool
 * The `format()` method provides a powerful way of replacing placeholders with values
* Ranges represent integer sequences with regular intervals
 * The `enumerate()` function is often a superior alternative
* Lists are heterogeneous mutable sequence types
 * Negative indexes work backwards from the end
 * Slicing allows us to copy all or part of a list
 * The full slice is a common idiom for copying lists, although the `copy()` method and `list()` constructor are less obscure
* List (and other collection) copies are shallow
* List repetition is shallow
* Dictionaries map immutable keys to mutable values
 * Order is arbitrary
 * The `keys()`, `values()` and `items()` methods provide views onto different aspects of a dictionary
* Sets store on unordered collection of unique elements
 * Support powerful and expressive set algebra operations and predicates
* `pprint` module supports pretty-printing of compound data structures
