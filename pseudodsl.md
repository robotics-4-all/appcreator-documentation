# PseudoDSL documentation

This the the PseudoDSL documentation, powered by [LocSys](https://locsys.issel.ee.auth.gr/). PseudoDSL enables the creation of modern applications with minimal lines of code.

The PseudoDSL is textual and resembles languages like Python, C, or Assembly. It has been created with a direct link to the [AppCreator DSL](https://github.com/robotics-4-all/appcreator-documentation/blob/main/README.md), since a program in PseudoDSL can be transformed to a program in AppCreator and vice versa.

# Language syntax

A general rule: all base commands must terminate with a semicolon (C style): `;` 

Furthermore, in front of each expression you can declare a tag as such:

```
[a] expression here
```

You can use this tag in the `goto` commands, as explained below.

## Variables and lists
PseudoDSL has two types of variables, the **variable**, which holds a single value, and the **list** which contains several values.

### `Declare variables`
The simplest way to declare a variable is by using the `var` keyword as such:

```js
var i;
```

This means that the variable `i` was generated with no declared initial value. In reality, `i` has been declared with 0 as its initial value. 

You can declare an initial value as such:

```js
var i 3;
```

Furthermore you can declare several variables in one line:

```js
var i, j 4, k 10, l "test";
```

Finally, you can even use other variables to participate in the initial value expression, as long as they have been declared previous to the assigned variable:

```js
var i 8, j i+2, k i*j;
```

### `Set variable value`

If a variable exists, you can redeclare its value by using `set` as such:

```js
var i 0;
set i 10;
set i i*3;
```

### `Declare lists`

You can declare lists the same way as you do with variables:

```js
var l [0, 5, 7, 10];
```

Or even:

```js
var i 8, j 9;
var list [i, j, 11, 10];
```

## List operations

List operations are divided into `ListReadOperations` and `ListWriteOperations`. The first are used among the `set` command to assign the output to a variable, while the latter are used as separate language builtin commands.

For example, if we want to get the length of a list, we must use the `llength` command to perform the operation, within a `set` command to assign the output value to a variable.

```javascript
var lst [1,4,7,9,3];
var i;

set i llength lst; // Sets i equal to the lists length (5)
```

### List Read Operations

- `ListLength`: Returns the length of a list
  - Syntax: `llength <List Name>`
- `ListMin`: Returns the minimum value from a list
  - Syntax: `lmin <List Name>`
- `ListMax`: Returns the maximum value from a list
  - Syntax: `lmin <List Name>`
- `ListAverage`: Returns the average of a list
  - Syntax: `laverage <List Name>`
- `ListStd`: Returns the standard deviations of a list
  - Syntax: `lstd <List Name>`
- `ListValueAtIndex`: Returns the list element at given index
  - Syntax: `lget <List Name> <Index (Expression)>`
- `ListIndexOf`: Returns the (first) index of given value
  - Syntax: `lindex <List Name> <Value (Expression)>`
- `ListContains`: Returns `True` if the list contains
  - Syntax: `lcontains <List Name> <Value (Expression)>`
- `ListCount`: Returns the number of times a value exists in a list
  - Syntax: `lcount <List Name> <Value (Expression)>`

Usage Examples:

```javascript
var f [1,2,3,4];
// Set list value by index
set f 0 5;
var g;
set g lget f 0;
set g llength f;
set g lcount f 1;
set g lmin f;
set g lmax f;
set g laverage f;
set g lindex f 3;
```

### List Write Operations

- `ListAppend`: Append (adds) a value to a list
  - Syntax: `lappend <List Name> <Value (Expression)>`
- `ListRemove`: Remove an element from a list given its' index
  - Syntax: `lremove <List Name> <Index (Expression)>`
- `ListPop`: Remove the last element from a list (pop)
  - Syntax: `lpop <List Name> <Index (Expression)>`
- `ListClear`: Clears a list. 
  - Syntax: `lclear <List Name>`
- `ListRemoveValue`: Remove the last elements with given values from a list.
  - Syntax: `lremval <List Name> <Value (Expression)>`
- `ListCopy`: Remove the last elements with given values from a list.
  - Syntax: `lremval <Source List Name> <Destination List Name>`

```javascript
// Set element at index
var i 9;
var l [1,4,5,6];
set l 3 i*2;

// Get list length
var len;
var lst [1,3,4];
set len llength lst ;

// Append to a list
var i 10;
var lst [1,4,5];
var lstb [3,7,4];
lappend lst 1+4+5+i;

// Append a list into a list
lappend lst lstb;

// Remove an element of a list by index
var lst [1,3,5,10];
lremove lst 0;

// Insert value in a list
var i 4;
var lst [3,5,7];
set lst i 1;

// Sort list
var lst [1,9,2,8];
var lst_b;
lsort lst asc;
lsort lst desc;
lsort lst asc lst_b;  // change target variable. Store sort result in lst_b

// Get list element by index
var lst [4,5,6];
set i lget lst 0;

// Find the index of an element in a list
var lst [5,8,10];
var i 10/2, exists;
set exists lindex lst i;

// Min/Max/Average/Std of values in a list
var lst [1,6,9,2,1];
var m;
set m lmax lst ;
set m lmin lst ;
set m laverage lst ;
set m lstd lst ;
set m lcount lst 1 ;

// Check wheather a list contains a value
var lst [5,8,10];
var i 5;
var exists;
set exists lcontains lst i;

// Copy List
var l_1 [0,1,3];
var tmp;
lcopy l_1 tmp;
```

## Conditions

You can declare a condition as such:

```js
var i 0;
if i == 0 {
  set i i+1;
};
set i i-1;
```

As evident, you must use the keyword `if`, followed by the expression you want, open curly braces, and put inside all other commands. If the condition is false, the next command, after the closing brace will be executed.

## Loops

You can implement loops as such:

```js
var i 0, j 10, k;
loop i 0..j {
  set k k+i;
};
```

This will iterate variable `i` from 0 to the value of `j-1` (as long as `i<j`).

## Tasks & Concurrency

PseudoDSL also supports parallel execution of blocks of expressions, implementing threads.

### `Create and join threads`

In order to use threads, you must have two Tasks (or more) that will be deployed in seperate threads, in parallel. Here is an example:

```js
var i 0, j 0, sum1 0, sum2 0, sum;

// Task Split Block -------------->
split 2;
// -----------Task 1 block ------
[T1] ((
  loop i 1..501 {
    set sum1 sum1+1;
  };
));
// -----------Task 2 block ------
[T2] ((
  loop j 501..1001 {
    set sum2 sum2+1;
  };
));

join;
// --------------------------------;
set sum sum1+sum2;
```

This piece of code calculates the sum of numbers from 1 to 1000 using two threads, one that calculates the sum from 1 to 500 and another that calculates the sum from 501 to 1000.
The threads are created and the `join` command waits until both threads are finished.
Then the total sum is the sum of the two other sums :).

### `Kill thread`

You also have the option to kill a Task from another Task. You can do this as such:

```js
var i 0, j 0, sum1 0, sum2 0, sum;

// Task Split Block -------------->
split 2;
// -----------Task 1 block ------
[T1] ((
  loop i 1..501 {
    set sum1 sum1+1;
  };
  kill [T2];
));
// -----------Task 2 block ------
[T2] ((
  loop j 501..5001 {
    set sum2 sum2+1;
  };
));

join;
// --------------------------------
set sum sum1+sum2;
```

Here we have the two previous threads, but the second iterates up to 5000. We have added the `kill task2;` command at the end of the iteration in the first Task, thus the task2 will be abruptly stopped at that time. This of course will lead to unexpected output value due to timing uncertainties.

## Other commands

### `Print`

The print command prints whatever expression you add next to it. Some examples are:

```js
var i 0, j 10, k i*j;
print i;
print "This is i:" + i;
print k*i*j;
```

### `Delay`

The delay command adds a delay in seconds whenever the user wants. Example:

```js
var i 0;
[ls] set i i+1;
if i<10 {
  delay i;
  goto [ls];
};
```

Here, the program will delay more and more when i increases.

### `Exit`

Exit is a simple command: it exits the program when called!

Example:

```js
var i 0;
[ls] set i i+1;
if i<10 {
  delay i;
  goto [ls];
};

exit;
print i;
```

Here, the final print will not be executed.

### `Go to`

You can use the Go to command to move the execution to another point in the program. For example we could implement a loop using go to as such:

```js
var i 0;
[ls] set i i+1;
if i<10 {
  goto [ls];
};
```

This code increases i by one, until it is greater than 9. The `set` command is assigned a tag, so that the `goto` command can use it to jump whenever the user desires.
