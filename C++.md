Table Of Contents
=================
   * [Type Casting](#type-casting)
      * [Static Cast](#static-cast)
      * [C-style Cast](#c-style-cast)
   * [Getting type of an expression](#getting-type-of-an-expression)
      * [The <code>auto</code> keyword](#the-auto-keyword)
      * [<code>typedef</code>](#typedef)
      * [Enumerations](#enumerations)
   * [Pointers](#pointers)
      * [Pointer to char](#pointer-to-char)
   * [<code>sizeof</code>](#sizeof)
   * [Pointers and Constants](#pointers-and-constants)
   * [Pointer Arithmetic](#pointer-arithmetic)
   * [Dynamic Memory Allocation](#dynamic-memory-allocation)
      * [Dynamically Allocating 2D Arrays](#dynamically-allocating-2d-arrays)
   * [Functions](#functions)
      * [Returning Pointers](#returning-pointers)
      * [Static Variables](#static-variables)
      * [Function Overloading](#function-overloading)
      * [Pointers to Functions](#pointers-to-functions)
      * [Default Parameters](#default-parameters)
      * [Function Templates](#function-templates)
      * [The <code>decltype</code> Operator](#the-decltype-operator)
      * [Variadic Template Functions (C++11)](#variadic-template-functions-c11)
      * [Lambda Expressions (C++11)](#lambda-expressions-c11)
   * [Structures](#structures)
      * [Initializing Structures](#initializing-structures)
   * [Classes](#classes)
      * [Defining Member Functions Outside](#defining-member-functions-outside)
      * [Inline Functions](#inline-functions)
      * [Constructors](#constructors)
         * [Initialization Lists](#initialization-lists)
         * [Accessing Private Members Outside the Class - Friend Functions](#accessing-private-members-outside-the-class---friend-functions)
         * [The <code>explicit</code> Specifier](#the-explicit-specifier)
      * [Copy Constructors](#copy-constructors)
      * [Destructors](#destructors)
      * [Resource Acquisition is Initialization (RAII)](#resource-acquisition-is-initialization-raii)
      * [Operator Overloading](#operator-overloading)
         * [Overloading the Increment and Decrement Operators](#overloading-the-increment-and-decrement-operators)
   * [Miscellaneous](#miscellaneous)
      * [ADL - Argument Dependent Lookup (Koenig Lookup)](#adl---argument-dependent-lookup-koenig-lookup)
      * [Swap functions should be public friend](#swap-functions-should-be-public-friend)
      * [The copy-and-swap idiom](#the-copy-and-swap-idiom)
      * [Move Semantics](#move-semantics)
         * [Rvalue References](#rvalue-references)
         * [Move Assignment Operator](#move-assignment-operator)
         * [<code>std::move</code>](#stdmove)
         * [xvalues, prvalues, glvalues](#xvalues-prvalues-glvalues)
         * [Forwarding References](#forwarding-references)
   * [Other C++11 Features](#other-c11-features)
      * [Range-Based For Loops](#range-based-for-loops)
      * [<code>override</code> and <code>final</code>](#override-and-final)
      * [The <code>constexpr</code> Specifier](#the-constexpr-specifier)
      * [Concurrency Support](#concurrency-support)
         * [Mutexes](#mutexes)
         * [Automatic Lock Management](#automatic-lock-management)


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
## Destructors
Used to deallocate resources used by the class. Essential in case of dynamic allocation.
## Resource Acquisition is Initialization (RAII)
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
## Operator Overloading
Lets you define how an operator behaves when used with objects of your classes. The operators that cannot be overloaded are :: (scope resolution), ?: (ternary conditional operator), . (member selection operator), sizeof, and .\* (dereference pointer to class member).  
Example:
```
class CBox {
public:
    bool operator> (CBox& box) const {
        return this->Volume() > box.Volume();
    }
    // Overloading for more support
    bool operator> (const double& val) {
        return this->Volume() > val;
    }
};
bool operator> (const double& val, const CBox& box) {
    return val > box.Volume();
}
```
### Overloading the Increment and Decrement Operators
```
class Length
{
private:
	double len; // Length value for the class

public:
	Length & operator++() // Prefix increment operator
	{
		++(this -> len);
		return *this;
	}

	const Length operator++(int) // Postfix increment operator
	{
		Length length = *this;     // Copy the current object
		++*this;                // Increment the current object
		return length;          // Return the original copy
	}

	Length & operator--() // Prefix decrement operator
	{
		--(this->len);
		return *this;
	}

	const Length operator--(int) // Postfix decrement operator
	{
		Length length = *this;     // Copy the current object
		--*this;                // Decrement the current object
		return length;          // Return the original copy
	}
	// rest of the class...
};
```

# Miscellaneous
## ADL - Argument Dependent Lookup (Koenig Lookup)
You don't need to qualify the namespace for functions if any of the arguments are defined in the same namespace as the function.
Example:
```
namespace Foo {
    void foo();
    class Bar { };
}
Foo::Bar obj;

int main() {
    foo(obj); // OK, Foo::foo() is found using ADL
}
```

## Swap functions should be `public friend`
Source: [Stack Overflow](http://stackoverflow.com/questions/5695548/public-friend-swap-member-function)
Since the canonical way of swapping is using `std::swap`, and the swap function cannot be partially specialized in namespace std, the best solution is to make the swap function a friend, and find it via ADL. Example in next section.

## The copy-and-swap idiom
Example:
```
class Foo {
public:
    friend void swap(Foo& a, Foo& b) {
        // enabling ADL
	using std::swap;
	
	// swap data members
    }
    
    Foo& operator=(Foo foo) {
    	// Object is copied to the parameter, now swap
	swap(*this, foo);
	return *this;
    }    	
};
```
The `*this` object refers to the LHS of the assignment statement. This object is swapped with the temporary value taken by value as the argument, effectively copying the data from the object on the RHS to the current one (on the LHS).

## Move Semantics
Source: [Stack Overflow](http://stackoverflow.com/questions/3106110/what-are-move-semantics/11540204#11540204)  

The copy constructor defines what happens when an lvalue is copied. An expression like `x + y` is an rvalue. When a constructor is called using a statement like
```
Foo foo(foo1 + foo2);
```
the argument is an rvalue, and is a temporary value that is destroyed at the semicolon. From C++11, rvalue references allow us to differentiate lvalues from rvalues, so a constructor can accept an rvalue reference as a parameter, and a deep copy of objects isn't required:
```
Foo(Foo&& param) {
    this->data = param.data;
    param.data = 0;
}
```
This constructor is called a move constructor and is responsible for moving resources from one object to another.  
  
> An rvalue of class type is an expression whose evaluation creates a temporary object. Under normal circumstances, no other expression inside the same scope denotes the same temporary object.  

### Rvalue References
Moving from lvalues is dangerous since it causes loss of data, but moving from rvalues is harmless since the temporary rvalue objects are destroyed. C++11 provides rvalue references to distinguish between lvalues and rvalues. An rvalue reference binds only to rvalues. Rvalue references of type X (`X&&`) can also bind to all value categories of type Y, if an implicit conversion from type Y exists.

### Move Assignment Operator
With the introduction of rvalues in C++11, the Rule of Three now extends to the Rule of Five, which states that if one of the following needs to be implemented by the programmer, the others probably should be implemented as well:
* Destructor
* Copy constructor
* Move constructor
* Copy assignment operator
* Move assignment operator
The move assignment operator releases the old resource(s) and acquires the new resource(s) from the argument:
```
Foo& operator=(Foo&& param) {
    if (this != &param) { // check for self assignment
        delete this->data;
	
	data = param.data;
	param.data = 0;
    }
    return *this;
}
```
This can be implemented using the move-and-swap idiom, which is similar to the copy-and-swap idiom, and simplifies the implementation.
```
Foo& operator=(Foo param) {
    std::swap(this->data, param.data);
    return *this;
}
```
Since the function is called with an rvalue actual argument, the move constructor initializes `param` by moving the actual argument. When control reaches the closing brace of the function, `param` goes out of scope and the old resource(s) are released automatically.

### std::move
The `std::move` function in `<utility>` casts an lvalue to an rvalue.

### xvalues, prvalues, glvalues
A _prvalue_ is an rvalue - a temporary object that can be assigned to an rvalue reference. The result of `std::move(lvalue)` does not create a temporary object, but can be assigned to an rvalue reference - such expressions are called _xvalues_. xvalues and lvalues are grouped as _glvalues_ or generalized lvalues.

### Forwarding References
In template functions, when an argument is an rvalue reference (`Type&&`), an lvalue (`X`) will also bind to it, unlike in regular functions, and `Type` is deduced to be `X&`.
Example:
```
void foo(int&& i);
 
template <typename T>
void bar(T&& i);

int k = 0;
foo(k); // Error
bar(k); // OK, T is deduced as int&
```
In the above example, `int&&` is an rvalue reference, and `T&&` is called a universal reference or forwarding reference (as it is called in the [proposal for the C++17 standard](https://isocpp.org/files/papers/N4164.pdf). The `auto&&` reference is also a forwarding reference.

# Other C++11 Features
## Range-Based For Loops
This syntax allows the use of for-each style loops, with the syntax:
```
int a[5] = {1, 2, 3, 4, 5};
for (int& x : a)
  a *= 2;
```

## `override` and `final`
The `override` specifier means that the compiler will check the base class(es) to see if there is a virtual function with the same signature. If not, the compiler gives an error.
```
class Base {
public:
  virtual void f(int);
};

class Derived : public Base {
  virtual void f(double) override; // Error.
};
```

C++11 also adds the `final` identifier. With this,  
* if `final` is applied to a class, no class can inherit from the current class
* if applied to a function, then it cannot be overriden in derived classes.

## The `constexpr` Specifier
Source: [CPP Reference](http://en.cppreference.com/w/cpp/language/constexpr)
The `constexpr` specifier declares that the value of the variable or function can be evaluated at compile time.  
* When used with an object or variable declaration, it implies `const`
* When used with a function, it implies `inline`

## Concurrency Support
C++11 introduces a new `<thread>` header file for concurrency. A `std::thread` object takes the code to be executed as a parameter to its constructor, like a functor, lambda or a function pointer. `std::this_thread` gives a reference to the currently running thread.

### Mutexes
Mutexes are in the `<mutex>` header file, and the `lock()` and `unlock()` methods are used to define a critical section in code. The `std::recursive_mutex` class allows the same thread to acquire the mutex multiple times. This is useful, for example, in cases where one member function calls another, and all of them use the same mutex (which would otherwise never work because the first function already acquired the lock).

### Automatic Lock Management
The `std::lock_guard<std::mutex>` is an RAII-style wrapper for a mutex. When the object is created, it calls `lock()` on the mutex automatically. When the object is destructed, it calls `unlock()`. Example:
```
class thread_safe_counter {
  std::mutex mutex;
  int value;

  void increment() {
    std::guard_lock<std::mutex> guard(mutex);
    ++value;
  }
};
```
