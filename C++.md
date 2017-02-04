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

# Structures
A collection of variables in one user-defined data type. All members are public by default, and a `struct` cannot have member functions. Example:
```
struct book {
  char title[80];
  char author[20];
  int year;
};
```
## Initializing Structures
```
book novel = { "How to be Ferociously Happy and other essays", "Dushka Zapata", 2016 };
std::cout << novel.author;
```
# Classes
Syntax:
```
class class_name {
public:
  // member functions and data
protected:
  // ...
private:
  // ...
};
```
Members are *private* by default.
## Defining Member Functions Outside
Example:
```
class Foo {
  int Bar();
};
int Foo::Bar() {
  // ...
}
```
The `::` operator is called the *scope resolution operator*
## Inline Functions
The `inline` keyword is a suggestion to the compiler that the function definition should be copied to the location of the function call for faster execution. The compiler, however, is free to ignore this suggestion, and may make a function inline even if the keyword is not specified.

## Constructors
A member function with the same name as its class, with no return type, used to initialize member variables. It should be kept in the public section.

### Initialization Lists
```
class Foo {
  int Boo, Bar;
public:
  Foo(int x, int y) : Boo(x), Bar(y) {}
};
```
### Accessing Private Members Outside the Class - Friend Functions
Friend functions are not member functions, but can access the private members of a class. This is done by including the keyword `friend` before its prototype, which must be included in the class, in the public section.
```
class Cube {
  int side;
public:
  Cube() {
    side = 0;
  }
  friend int getVolume(Cube x);
};
int getVolume(Cube cube) {
  return cube.side * cube.side * cube.side;
}
```
### The explicit Specifier
It specifies that a constructor does not allow implicit conversions. Example (taken from [CPPReference](http://en.cppreference.com/w/cpp/language/explicit))
```
struct A
{
    A(int) { }      // converting constructor
    A(int, int) { } // converting constructor (C++11)
    operator bool() const { return true; }
};
 
struct B
{
    explicit B(int) { }
    explicit B(int, int) { }
    explicit operator bool() const { return true; }
};
 
int main()
{
    A a1 = 1;      // OK: copy-initialization selects A::A(int)
    A a2(2);       // OK: direct-initialization selects A::A(int)
    A a3 {4, 5};   // OK: direct-list-initialization selects A::A(int, int)
    A a4 = {4, 5}; // OK: copy-list-initialization selects A::A(int, int)
    A a5 = (A)1;   // OK: explicit cast performs static_cast
    if (a1) ;      // OK: A::operator bool()
    bool na1 = a1; // OK: copy-initialization selects A::operator bool()
    bool na2 = static_cast<bool>(a1); // OK: static_cast performs direct-initialization
 
//  B b1 = 1;      // error: copy-initialization does not consider B::B(int)
    B b2(2);       // OK: direct-initialization selects B::B(int)
    B b3 {4, 5};   // OK: direct-list-initialization selects B::B(int, int)
//  B b4 = {4, 5}; // error: copy-list-initialization does not consider B::B(int,int)
    B b5 = (B)1;   // OK: explicit cast performs static_cast
    if (b2) ;      // OK: B::operator bool()
//  bool nb1 = b2; // error: copy-initialization does not consider B::operator bool()
    bool nb2 = static_cast<bool>(b2); // OK: static_cast performs direct-initialization
}
```
## Copy Constructors
Needed when dynamic allocation is used. Example:
```
class CMessage {
public:
    char* message;
    CMessage(const char* m) {
        message = new char[strlen(m) + 1];
	strcpy(message, m);
    }    
};
```
If the default copy constructor is used, the second object's pointer will be set by simply copying the pointer, so both will now point to the same string. If any object is modified, the other one gets modified too. For this reason, a custom copy constructor must be implemented. To avoid infinite calls to the copy constructor, a const reference must be used:
```
CMessage(const CMessage& str) {
    size_t len = strlen(str.message) + 1;
    message = new char[len];
    strcpy_s(message, len, str.message);
}
```
# Destructors
Used to deallocate resources used by the class. Essential in case of dynamic allocation.
# Resource Acquisition is Initialization (RAII)
According to this concept, objects of a class must own resources, and are responsible for releasing them for exception safety.
Example [from StackOverflow](http://stackoverflow.com/questions/2321511/what-is-meant-by-resource-acquisition-is-initialization-raii)
```
RawResourceHandle* handle=createNewResource();
handle->performInvalidOperation();  // Oops, throws exception
...
deleteResource(handle); // oh dear, never gets called so the resource leaks
```
This can be fixed with RAII:
```
class ManagedResourceHandle {
public:
   ManagedResourceHandle(RawResourceHandle* rawHandle_) : rawHandle(rawHandle_) {};
   ~ManagedResourceHandle() {delete rawHandle; }
   ... // omitted operator*, etc
private:
   RawResourceHandle* rawHandle;
};

ManagedResourceHandle handle(createNewResource());
handle->performInvalidOperation();
```
