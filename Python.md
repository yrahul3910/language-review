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
