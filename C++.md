# Type Casting
## Static Cast
Explicitly casts an expression to desired type. The compiler checks whether the cast is possible statically.
`static_cast<type>(expr)`

# Getting type of an expression
Use the `typeid` operator. `typeid(expr)` returns a `type_info` object.   
The `name()` function gets the type of the expression as a string:
```
std::cout << typeid(4*5.3f).name();
```
will print `float`. The `<typeinfo>` header file must be included.

## The auto keyword
You don't need to specify the data type of the expression on the right in this case

## typedef
Gives a new name to an existing data type. Syntax: `typedef <existing type> <alias>;`

## Enumerations
Syntax example:
```
enum months {January, February, March, April, May, June, July, August, September, October, November, December};
```
You can now make a `months` object like this:
```
months birth_month = March;
```
Each enumeration constants are given integer values, starting from 0. These can be custom modified within the definition as well.
```
enum punctuation {comma=',', period='.', question='?'};
```

# Pointers
Variables that hold the address of another variable of the type that they point to.
```
int x;
int *p = &x;
```
\* - the indirection operator, gives the value stored at the address contained by the pointer  
& - the address-of operator gives the address of a variable  

Uninitialized pointers have random values, called wild pointers. They must be initialized to `nullptr`.

## Pointer to char
A pointer to char can be initialized to a string literal.
```
char *sentence = "Hello, World!";
```
This makes an array of type `const char`, terminating with the `\0` character, and then stores the address of the first character literal in the pointer sequence.  
Pointers take 4 bytes on 32-bit machines and 8 bytes on 64-bit machines.

An array of pointers can be declared as shown:
```
char *ptr[] = {"Hello", "World!"};
```

# sizeof
Used to get the space taken in bytes by a data type or a variable. Syntax:
```
sizeof var
sizeof(data-type)
```
