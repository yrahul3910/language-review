# Type Casting
## Static Cast
Explicitly casts an expression to desired type. The compiler checks whether the cast is possible statically.
`static_cast<type>(expr)`

## C-style Cast
A C-style cast is basically identical to trying out a range of sequences of C++ casts, and taking the first C++ cast that works, without ever considering `dynamic_cast`. It's unsafe to use because it ignores `dynamic_cast`.

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

# Pointers and Constants
If we look at the array of pointers defined above, `*words[0] = "fist"` won't compile since it's an array of `const char`. `*words[0] = 'F'` will compile, though it crashes at runtime. This can be avoided by making the pointer constant.  

The below code defines an array of __pointers to constants__. 
```
const char* p[] = {"First", "Second", "Third"};
```
You could still write
```
p[0] = p[1]
```
since this modifies the pointers and not the values stored. To make the pointers constant as well, we write
```
const char* const p[] = {"First", "Second", "Third"};
```
# Pointer Arithmetic
If we have an array `arr` and a pointer `word = arr`, then `word++` makes the pointer point to the second element. Because of this,
```
arr[2]
*(word + 2)
```
are both the same.  
With 2D arrays, a double pointer is required:
```
int x[2][2] = {1, 2, 3, 4};
int** ptr = x; // Correct
```
The following expressions are equivalent:
```
x[i][j]
*(*(ptr + i) + j)
```
# Dynamic Memory Allocation
You can dynamically allocate memory for an array as shown:
```
int* ptr = new int[n];
```
where `n` is any variable of type int. To deallocate this, you use
```
delete[] ptr;
```
The same statement can be used for any dimensioned array. After deallocating, it is important to re-assign `ptr` to `nullptr`.
## Dynamically Allocating 2D Arrays
```
int **matrix, rows, cols;
cin >> rows >> cols;
matrix = new (std::nothrow) int *[row]; // if compiler cannot allocate memory, matrix = nullptr
if (!matrix)
  return -1;
for (int i = 0; i < rows; ++i) {
  matrix[i] = new (std::nothrow) int[cols];
  if (!matrix[i]) return -1;
}
```
# Functions
* Arrays are always passed by reference, and not by value. The array name is converted to a pointer, and a copy of the pointer is passed to the function.  
* When taking a multidimensional array as a parameter, only the first dimension value may be omitted.  
* Even though the rvalue reference parameter may refer to an rvalue, the parameter itself is an lvalue.
## Arguments to main
The syntax is:
```
int main(int argc, char *argv[]) {
  ...
}
```
`argc` is the number of arguments passed. `argv[0]` is the program name itself.
## Returning Pointers
A pointer to a local variable must never be returned, since the variable itself will be destroyed. Instead, we must use dynamic memory allocation to create a variable in the free store and return a pointer to that.  
Similarly a reference to a local variable must never be returned.

## Static Variables
The value of static variables persist from one function call to the next. Initialization only occurs the first time it is called. It continues to exist for the duration of the program execution.

## Function Overloading
Multiple functions with the same name. The compiler chooses the correct version for the function call.  
A different return type does not distinguish a function adequately, only by the parameter list. You cannot overload on a type and an lvalue reference to that type. However, the compiler can distinguish between the following:
```
void f(int& arg);
void f(int&& arg);
```
The first will always be selected when the argument is an lvalue. The second will only be selected when the argument is an rvalue.

## Pointers to Functions
They allow you to call a function using the pointer. The pointer must also contain data of the parameters of the function it can point to. It can then only point to functions with the same parameters. The syntax is:
```
return_type (*name_of_pointer)(argument_types)
```
An array of pointers to functions can be used as shown:
```
double square(double);
double cube(double);
double sqrt(double);
double (*ptr[3])(double) = {square, cube, sqrt};
```
## Default Parameters
Must be kept at the end of the function declaration.

## Function Templates
Templates let the compiler generate functions from a template that you define. The generated functions will have the same code, customized by the data type you give.
```
template<classT,
	class U> inline
	T Find(T First,T Last, const U& Val)
	{	// find first matching Val
	for (; First != Last; ++First)
		if (*First == Val)
			break;
	return (First);
	}
  ```
 The above is a slightly simplified version of a linear search implemented in `<xutility>`. You can write `typename` instead of class as well. The creation of a function from a template is called *instantiation*.
 
## The decltype Operator
 Lets you find the type of an expression. Example:
 ```
 float a = 5.3f;
 int b = 3;
 decltype(a * b) c = a * b;
 ```
 The main use is in deciding the return type of a function template as shown:
 ```
template < class T1, class T2 >
auto product(T1 v1[], T2 v2[], size_t count) -> decltype(v1[0] * v2[0])
{
	decltype(v1[0] * v2[0]) sum(0);
	for (size_t i = 0; i < count; i++) sum += v1[i] * v2[i];
	return sum;
}
```

## Variadic Template Functions (C++11)
Let you define function templates that accept any number of arguments. For this, a base function must be used. These are generally recursive. A simple example, taken from MSDN, is below.
```
template <typename T> void print(const T& t) {  
    cout << t << endl;  
}  
  
template <typename First, typename... Rest> void print(const First& first, const Rest&... rest) {  
    cout << first << ", ";  
    print(rest...); // recursive call using pack expansion syntax  
}
```

## Lambda Expressions (C++11)
It is an anonymous function that can be used as a predicate to functions that accept a function pointer as an argument.
Example:
```
sort (arr, arr + length, 
    [](int a, int b) {
      return a > b;
    }
);
```
sorts in descending order. The square brackets is for *lambda capture* expressions. Specifying an = captures all variables in the surronding scope by value, and a & captures by reference. You can capture some values by value and some by reference, using a comma-separated list. For example,
```
[=, &var]
```
captures all variables except var by value.
