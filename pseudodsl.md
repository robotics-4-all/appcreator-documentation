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
var list [i, j, 11, i+j];
```

## List operations

If we  have declared a list, we can perform several operations on it. These operations are shown in a full example:

```javascript
var lst [1,4,7,9,3];
var i;

set i llength(lst); // Sets i equal to the lists length (5)

set i lvalue(lst 2); // Sets i equal to the element of the list at index=2 (third place)

set i lindex(lst 9); // Sets i equal to the index where the value 9 exists

set i lcontains(lst 7); // Sets i=true if value 7 exists in the list

set i lmin(lst); // Sets i equal to the minimum element of the list (1)

set i lmax(lst); // Sets i equal to the maximum element of the list (9)

set i laverage(lst); // Sets i equal to the mean value of the elements of the list

set i lstd(lst); // Sets i equal to the standard deviation of the elements of the list

set lcount(lst 8); // Sets i equal to the number of occurences of 8 in the list (0)

var j 5;

lappend lst j; // Appends the value of variable j at the end of the list

lremove lst 1; // Removes the element with index=1 from the list

linsert lst 0 15; // Inserts the number 15 at the first place of the list (index = 0)

lsort lst asc; // Sorts the list in an ascending manner (or dec for a descending)

lreverse lst; // Reverses the elements of the list

lpop lst 1; // Removes the element at index = 1 **(isn't this the same as lremove??)**

lclear lst; // Removes all elements from the list

lremval lst 5; // Removes all occurences of the number 5 from the list

var lst2;

lcopy lst lst2; // Copies the elements of lst to lst2

lappend lst lst2; // Appends all elements of lst2 to the end of lst **(klpanagi check this)**
```

## Conditions

You can declare a condition as such:

```js
var i 0;
if i == 0 {
  set i i+1;
}
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

## Threads and tasks

PseudoDSL also supports parallel execution of blocks of expressions, implementing threads.

### `Task declaration`

In order to execute a number of expressions in parallel, you must declare them inside a `Task`. You can create a Task as such:

```js
var i 0, k;
T - task1 ((
  loop i 0..10 {
    set k k+1;
  }
));
```

Here `task1` is the ID of this task and you will use it in order to reference it in thread creation.

### `Create and join threads`

In order to use threads, you must have two Tasks (or more) that will be deployed in seperate threads, in parallel. Here is an example:

```js
var i 0, j 0, sum1 0, sum2 0, sum;

T - task1 ((
  loop i 1..501 {
    set sum1 sum1+1;
  };
));

T - task2 ((
  loop j 501..1001 {
    set sum2 sum2+1;
  };
));

split task1, task2;
join task1, task2;

set sum sum1+sum2;
```

This piece of code calculates the sum of numbers from 1 to 1000 using two threads, one that calculates the sum from 1 to 500 and another that calculates the sum from 501 to 1000.
The threads are created and the `join` command waits until both threads are finished.
Then the total sum is the sum of the two other sums :).

### `Kill thread`

You also have the option to kill a Task from another Task. You can do this as such:

```js
var i 0, j 0, sum1 0, sum2 0, sum;

T - task1 ((
  loop i 1..501 {
    set sum1 sum1+1;
  };
  kill task2;
));

T - task2 ((
  loop j 501..5001 {
    set sum2 sum2+1;
  };
));

split task1, task2;
join task1, task2;

set sum sum1+sum2;
```

Here we have the two previous threads, but the second iterates up to 5000. We have added the `kill task2;` command at the end of the iteration in the first Task, thus the task2 will be abruptly stopped at that time. This of course will lead to unexpected output value due to timing uncertainties.

## Other commands

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
