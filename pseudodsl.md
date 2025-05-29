# PseudoDSL documentation

This the the PseudoDSL documentation, powered by [LocSys](https://locsys.issel.ee.auth.gr/). PseudoDSL enables the creation of modern applications with minimal lines of code.

The PseudoDSL is textual and resembles languages like Python, C, or Assembly. It has been created with a direct link to the [AppCreator DSL](https://github.com/robotics-4-all/appcreator-documentation/blob/main/README.md), since a program in PseudoDSL can be transformed to a program in AppCreator and vice versa.

# Language syntax

A general rule: all base commands must terminate with a semicolon (C style): `;` 

## Variables and lists
PseudoDSL has two types of variables, the **variable**, which holds a single value, and the **list** which contains several values.

### `Declare variables`
The simplest way to declare a variable is by using the `var` keyword as such:

```
var i;
```

This means that the variable `i` was generated with no declared initial value. In reality, `i` has been declared with 0 as its initial value. 

You can declare an initial value as such:

```
var i 3;
```

Furthermore you can declare several variables in one line:

```
var i, j 4, k 10, l "test";
```

Finally, you can even use other variables to participate in the initial value expression, as long as they have been declared previous to the assigned variable:

```
var i 8, j i+2, k i*j;
```

### `Set variable value`

If a variable exists, you can redeclare its value by using `set` as such:

```
var i 0;
set i 10;
set i i*3;
```

### `Declare lists`

You can declare lists the same way as you do with variables:

```
var l [0, 5, 7, 10];
```

Or even:

```
var i 8, j 9;
var list [i, j, 11, i+j];
```

## List operations

If we  have declared a list, we can perform several operations on it.

### `Set element at index`

For setting a value in a list, at a specific index:

```
var i 9;
var l [1,4,5,6];
lset l i*2 3;
```

This means that in the 4th place of the list (index equal to 3), the value of `i*2` will be set.

### `Get length`

You can assign a list's length in a variable as such:

```
var len;
var lst [1,3,4];
set len llength(lst);
```

This sets variable `len` to 3.

### `Append`

You can append a value in a list as such:

```
var i 10;
var lst [1,4,5];
lappend(lst, 1+4+5+i);
```

This will append the value 20 to the end of the list, resulting in `[1,4,5,20]`.

### `Remove`

You can remove an element of the list by index as such:

```
var lst [1,3,5,10];
lremove(lst, 0);
```

This will result in the removal of the first element of the list, whose final value will be `[3,5,10]`.

### `Insert`

You can insert an element at a specific place (index) in the list as such:

```
var i 4;
var lst [3,5,7];
linsert(lst, i, 1);
```

This means that the value of variable `i` (4) will be inserted at the 2nd place of the list, between 3 and 5. The final list will have this value: `[3,4,5,7]`.

### `Sort`

You can sort a list in a descending or an ascending order as such:

```
var lst [1,9,2,8];
lsort(lst, asc);
lsort(lst, dec);
```

This application will first sort the list in an ascending order and then in a descending order, resulting in `[9,8,2,1]`. 

### `Reverse`

You can reverse the order of the elements in a list as such:

```
var lst [1,6,9,2];
lreverse(lst);
```

This will result in the list having the following value: `[2,9,6,1]`.

### `Get element by index`

You can get the value at a specific index as such:

```
var lst [4,5,6];
var i lvalue(lst, 0);
```

This application sets variable `i` with the value 4 (the value at the index 0 of the list).

### `Index of`

You can find the index of an element in a list as such:

```
var lst [5,8,10];
var i 10/2;
var exists lindex(lst, i);
```

This way, the variable `exists` will take the `0` value, since 5 exists in the first place of the list. If the value does not exist in the list -1 is returned.

### `Contains`

```
var lst [5,8,10];
var i 5;
var exists lcontains(lst, i);
```

This way, the variable `exists` will take the value `True`, since 5 exists in the first place of the list.

### `To be done`

- min
- max
- average
- std
- element count
- pop
- clear
- delete by value
- copy list to list
- append list to list

## Conditions

## Loops

## Threads and tasks

### `Task declaration`
### `Create threads`
### `Join threads`
### `Kill thread`

## Others

### `Go to`
### `Print`
### `Delay`
### `Random number`
