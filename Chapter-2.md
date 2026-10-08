# Chapter 2 — Variables

This chapter teaches **variables from the beginning**, including what actually happens conceptually when you declare, initialize, assign, and reassign a variable in Go.

> One small correction first: the code examples are **Go**, not JavaScript or YAML. In your notes, I'll use Go code blocks (`go`) so they can be copied directly.

---

# 2.1 What Is a Variable?

A **variable** is a named location used by a program to hold a value.

For example:

```go
age := 40
```

Here:

- `age` is the **variable name**
- `40` is the **value**
- Go associates `age` with a location where that value can be stored

You can then use the variable:

```go
fmt.Println(age)
```

Output:

```text
40
```

## Think of a variable as a named box

Conceptually:

```text
        age
         │
         ▼
      ┌─────┐
      │ 40  │
      └─────┘
```

If we later write:

```go
age = 41
```

the value associated with `age` changes:

```text
        age
         │
         ▼
      ┌─────┐
      │ 41  │
      └─────┘
```

The important distinction is:

```text
Variable → has a name
Value    → data stored in/associated with the variable
Type     → tells Go what kind of value the variable holds
```

For example:

```go
var age int = 40
```

means:

```text
name  = age
type  = int
value = 40
```

---

# 2.1.1 Why Do We Need Variables?

Imagine you want to calculate someone's age after five years.

Without a variable:

```go
fmt.Println(40 + 5)
```

This works, but it isn't very meaningful.

With a variable:

```go
age := 40
fmt.Println(age + 5)
```

Output:

```text
45
```

Now the program can work with the concept of an `age`.

Variables allow us to:

- store data
- reuse data
- modify data
- perform calculations
- pass data to functions
- make programs dynamic

For example:

```go
salary := 100000

salary = salary + 10000

fmt.Println(salary)
```

Output:

```text
110000
```

---

# 2.1.2 Variable Declaration

**Declaration** means telling Go that a variable exists.

For example:

```go
var age int
```

This tells Go:

> Create a variable called `age` whose type is `int`.

At this point, we haven't explicitly provided a value.

Go gives it the **zero value** for that type.

For `int`, the zero value is:

```text
0
```

So:

```go
var age int

fmt.Println(age)
```

produces:

```text
0
```

---

# 2.1.3 Variable Initialization

Initialization means giving a variable its initial value.

For example:

```go
var age int = 40
```

Now:

```text
name → age
type → int
value → 40
```

Example:

```go
package main

import "fmt"

func main() {
    var age int = 40

    fmt.Println(age)
}
```

Output:

```text
40
```

---

# 2.1.4 Assignment

Assignment means putting a value into an existing variable.

Example:

```go
var age int

age = 40
```

There are two separate operations here:

```go
var age int
```

Declaration.

Then:

```go
age = 40
```

Assignment.

You can later assign another value:

```go
age = 41
```

Now the variable contains `41`.

---

# 2.2 `var`

The `var` keyword is used to declare variables.

Basic syntax:

```go
var variableName type
```

For example:

```go
var age int
var name string
```

You can print them:

```go
package main

import "fmt"

func main() {
    var age int
    var name string

    fmt.Println(age)
    fmt.Println(name)
}
```

Output:

```text
0

```

Why is `name` empty?

Because the zero value of `string` is:

```text
""
```

The empty string doesn't visibly print anything.

---

## Common zero values

Go automatically gives variables their zero value when you don't provide an initial value.

| Type | Zero value |
|---|---|
| `int` | `0` |
| `float64` | `0` |
| `bool` | `false` |
| `string` | `""` |
| pointer | `nil` |
| slice | `nil` |
| map | `nil` |

For example:

```go
var age int
var salary float64
var active bool
var name string

fmt.Println(age)
fmt.Println(salary)
fmt.Println(active)
fmt.Println(name)
```

Output:

```text
0
0
false

```

---

# 2.2.1 Why Does Go Have Zero Values?

One important Go design principle is:

> Variables should have a useful, predictable default value.

For example:

```go
var count int
```

You don't get an undefined or garbage value.

You get:

```text
0
```

Therefore this is safe:

```go
var count int

count++
```

After this:

```text
count = 1
```

---

# 2.3 Declaration + Initialization

You can declare and initialize a variable in one statement:

```go
var age int = 40
```

And:

```go
var name string = "Saket"
```

Complete example:

```go
package main

import "fmt"

func main() {
    var age int = 40
    var name string = "Saket"

    fmt.Println(name)
    fmt.Println(age)
}
```

Output:

```text
Saket
40
```

Here:

```go
var age int = 40
```

contains four important parts:

```text
var     age     int     =     40
│       │       │       │     │
│       │       │       │     └── value
│       │       │       └──────── assignment/initialization
│       │       └──────────────── type
│       └──────────────────────── variable name
└──────────────────────────────── keyword
```

---

# 2.4 Type Inference

Go can sometimes determine the type automatically.

Instead of:

```go
var age int = 40
```

you can write:

```go
var age = 40
```

Go looks at:

```text
40
```

and determines that the variable should be an integer type (`int`).

Similarly:

```go
var name = "Saket"
```

Go determines:

```text
name → string
```

Example:

```go
package main

import "fmt"

func main() {
    var age = 40
    var name = "Saket"

    fmt.Println(age)
    fmt.Println(name)
}
```

This is called **type inference**.

---

## Explicit type vs inferred type

Explicit:

```go
var age int = 40
```

Inferred:

```go
var age = 40
```

Both result in an `int` variable here.

For basic cases, Go can determine the type from the initial value.

---

# 2.5 Short Variable Declaration `:=`

Go provides an even shorter way:

```go
age := 40
```

This is called a **short variable declaration**.

It essentially combines:

```text
declaration
+
initialization
```

So:

```go
age := 40
```

is similar to:

```go
var age = 40
```

And because Go infers the type:

```text
age → int
```

Likewise:

```go
name := "Saket"
```

creates a string variable.

---

## `:=` can only be used for new variables

This is extremely important.

This is valid:

```go
age := 40
```

But once `age` already exists:

```go
age := 40
age := 41
```

the second line produces an error.

Why?

Because `:=` means:

> Declare a new variable.

But `age` already exists.

To change its value, use:

```go
age = 41
```

So:

```go
age := 40
age = 41
```

is correct.

---

# 2.6 Multiple Variables

Go allows you to declare multiple variables together.

For example:

```go
var a, b int
```

This creates two variables:

```text
a → int
b → int
```

Both initially contain the zero value:

```text
a = 0
b = 0
```

You can also initialize them:

```go
var a, b int = 10, 20
```

Now:

```text
a = 10
b = 20
```

---

## Multiple variables with `:=`

You can also write:

```go
x, y := 10, 20
```

This creates two variables:

```text
x = 10
y = 20
```

You can even have different types:

```go
name, age := "Saket", 40
```

Now:

```text
name → string
age  → int
```

---

# 2.6.1 Multiple Assignment

Go supports multiple assignment.

For example:

```go
x, y = 10, 20
```

This assigns:

```text
x = 10
y = 20
```

This becomes particularly useful when swapping variables.

---

# 2.7 Reassignment

Once a variable exists, you can change its value.

Example:

```go
age := 40

age = 41
```

Initially:

```text
age = 40
```

After:

```go
age = 41
```

we have:

```text
age = 41
```

You **do not** use `:=` for ordinary reassignment.

Correct:

```go
age := 40
age = 41
```

Incorrect:

```go
age := 40
age := 41
```

---

# 2.7.1 Reassignment Does Not Change the Type

This is important.

Suppose:

```go
age := 40
```

`age` is an `int`.

You can do:

```go
age = 41
```

But you cannot do:

```go
age = "Saket"
```

because `"Saket"` is a string.

Go is **statically typed**.

The variable remains an `int`.

---

## Static Typing in Go

A **statically typed language** is a programming language where the **type of a variable is known and checked before the program runs**, usually during compilation.

Since you're learning Go, let's understand it using Go.

### 1. Example in Go

```go
var age int = 40
```

Here:

- `age` → variable
- `int` → type
- `40` → value

Go knows that:

> `age` can contain an `int`.

So this is valid:

```go
age = 50
```

But this is invalid:

```go
age = "Saket"
```

Go will catch this **before the program runs**:

```text
cannot use "Saket" (untyped string constant) as int value
```

That's the **static typing** part.

---

### 2. Why is it called "static"?

Think of the type as being **fixed/known ahead of execution**.

```go
var age int
```

The compiler knows:

```text
age → int
```

before your program starts running.

So when it sees:

```go
age = "hello"
```

the compiler can immediately say:

> ❌ This is wrong. `age` is an `int`, but you're trying to put a `string` into it.

---

### 3. Compare with a dynamically typed language

For example, JavaScript:

```javascript
let age = 40;
age = "Saket";
```

This is allowed.

The same variable can now contain:

```text
age → 40
```

and later:

```text
age → "Saket"
```

The type is determined/checked more dynamically while the program executes.

So, broadly:

| Language | Typing | Variable type |
| --- | --- | --- |
| Go | Static | Known at compile time |
| JavaScript | Dynamic | Determined at runtime |

`age = 40` is valid in both languages. However, after `age` is declared as an `int` in Go, `age = "Saket"` is invalid, while it is valid in JavaScript.

---

### 4. Important: Static typing ≠ explicit typing

This is important in Go.

You don't always have to explicitly write the type:

```go
var age = 40
```

Go **infers** that:

```text
age → int
```

So this is still statically typed.

The compiler effectively determines:

```go
var age int = 40
```

from:

```go
var age = 40
```

Similarly:

```go
name := "Saket"
```

Go knows:

```text
name → string
```

and you cannot later do:

```go
name = 100
```

---

### 5. The easiest definition to remember

> **Statically typed language:** The compiler knows and checks the types of variables before the program runs.

For Go:

```go
age := 40
```

means Go determines:

```text
age → int
```

and that type doesn't change during the lifetime of that variable.

This is one of the reasons Go can catch many programming mistakes **before you run your program**.

---

# 2.7.2 A Useful Mental Model

Consider:

```go
age := 40
```

You can think:

```text
Variable:
    name = age
    type = int
    value = 40
```

Then:

```go
age = 41
```

changes:

```text
Variable:
    name = age
    type = int
    value = 41
```

The **value changes**, but the **type does not**.

---

# Exercises

Now let's make these practical.

## Exercise 1 — Create Variables

Create variables for:

```text
name
age
salary
city
```

Use appropriate Go types.

For example, you might start with:

```go
name := ...
age := ...
salary := ...
city := ...
```

Don't copy a solution—try choosing the values and types yourself.

---

## Exercise 2 — Print Them

Create the four variables and print them.

Expected kind of output:

```text
Name: Saket
Age: 40
Salary: 100000
City: Pune
```

Hint:

```go
fmt.Println("Name:", name)
```

---

## Exercise 3 — Change Their Values

Start with:

```go
age := 40
city := "Pune"
```

Then change them to different values using **assignment**, not `:=`.

For example:

```go
age = ...
city = ...
```

---

## Exercise 4 — Sum of Three Integers

Create three integer variables:

```go
a := ...
b := ...
c := ...
```

Calculate their sum.

For example:

```go
sum := a + b + c
```

Print the result.

---

## Exercise 5 — Swap Two Variables

Given:

```go
a := 10
b := 20
```

After the swap, you should have:

```text
a = 20
b = 10
```

Try to do this using Go's multiple assignment feature.

**Hint:**

```go
a, b = ...
```

---

# Exercise 6 — Which Declarations Compile?

For each example, determine whether it is valid Go.

### A

```go
var age int
```

### B

```go
var age = 40
```

### C

```go
age := 40
```

### D

```go
age = 40
```

### E

```go
age := 40
age = 41
```

### F

```go
age := 40
age := 41
```

### G

```go
var age int = 40
age = 41
```

### H

```go
var age int = "40"
```

Try to classify each as:

```text
COMPILES
```

or

```text
DOES NOT COMPILE
```

and explain why.

---

# Debugging Exercise

Given:

```go
name := "Saket"
name := "Gupta"
```

This does **not** compile.

The reason is that `:=` is a **short variable declaration**.

The first line creates:

```text
name → string → "Saket"
```

The second line tries to declare `name` again in the same scope:

```go
name := "Gupta"
```

But `name` already exists.

If your intention is to **change the value**, use `=`:

```go
name := "Saket"
name = "Gupta"
```

Now:

```text
name = "Gupta"
```

---

# Important Rules to Remember

Keep these rules in your notes:

```text
1. var can declare a variable.

2. A variable can be declared without an initial value:
       var age int

3. Go gives variables their type's zero value.

4. You can declare and initialize together:
       var age int = 40

5. Go can infer the type:
       var age = 40

6. := declares and initializes a new variable:
       age := 40

7. = assigns a new value to an existing variable:
       age = 41

8. := cannot normally be used to simply reassign an
   existing variable:
       age := 40
       age := 41   ❌

9. Variables have a fixed type:
       age := 40
       age = "Saket"   ❌

10. Go supports multiple variables:
       x, y := 10, 20

11. Go supports multiple assignment:
       x, y = 20, 10
```

### The most important distinction

If you remember only one thing from this chapter, remember:

```go
:=   → create a new variable
=    → assign/change the value of an existing variable
```

For example:

```go
age := 40   // create
age = 41    // change
```

This distinction will become **very important later**, especially when we start working with functions, scopes, packages, and more complex Go programs.