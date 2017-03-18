# Basic Rules
* Variable declarations begin with the `var` keyword, or `let` (ES6)
* The data types are `undefined`, `null`, `boolean`, `string`, `symbol`, `number`, `object`
* Strings can be enclosed in single or double quotes, and are immutable. `s.length` gives the length of the string
* Arrays are heterogeneous collections of values
* `push()` and `pop()` insert and delete at the end of an array
* `shift()` and `unshift()` insert and delete at the beginning of an array
* The `==` operator only checks for value equality, while `===` checks for type equality as well. Example:
```
console.log(7 == '7'); // true
console.log(7 === '7'); // false
```
* String concatenation works with multiple data types
* Different cases in a switch-case statement can have different types of values
* An object can be created as follows:
```
var dog = {
  legs: 4,
  tails: 1,
  friends: ["nobody"] //  poor dog
};
```
* Properties can be accessed by the array index operator or the dot operator
* Objects can be nested
* Different math functions, including `random()`, can be accessed as `Math.functionName()`
* Objects can be created via constructors as well:
```
var Car = function(wheels, seats) {
  // these are public
  this.wheels = wheels;
  this.seats = seats;
  
  // this is private
  var speed = 10;
  
  // public method
  this.setSpeed = function(s) {
    speed = s;
  }; // semicolon is needed here
};
```
# Regex
Example:
```
var exp = /\d+/gi;
var str = "There are 4 apples and 2 bananas";
var digitCount = str.match(exp).length;
```
* Using capital letters in Regex inverts their meaning

# Array Operations
## Map
```
var array = [1, 2, 3, 4];
var timesFour = array.map(function(val) {
  return val * 4;
});
```

## Reduce
### Sum of array elements
```
var array = [1, 2, 3, 4];
var sum = array.reduce(function(accumulator, value) {
  return accumulator + value;
}, 0);
```
### Flattening array
Example from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
```
const flatten = function(arr) {
  return arr.reduce(
    function(accumulator, value) { // accumulator contains the flattened array so far
      return accumulator.concat(
        Array.isArray(val) ? flatten(val) : val
      );
    }, 
  []); // [] is the initial value
};
```

## Filter
```
array = oldArray.filter(function(val) {
	return val !== 5;
}); // contains elements except those which are 5
```
## Slice and Splice
* The `slice(start, end)` function returns the elements in the range [start, end).
* The `splice` function modifies an array, removing elements and optionally adding them.
```
array.splice(start); // removes all elements from index start, inclusive
array.splice(start, deleteCount); // removes deleteCount number of elements starting from index start
array.splice(start, deleteCount, item1, item2, ...); // same as above, but adds item1, item2, ... in the deleted position
```