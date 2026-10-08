# Chapter 3 — Data Types in Go

## 3.1 What Is a Data Type?

A **data type** tells Go what kind of value a variable can hold.

For example:

```go
var age int = 40
var name string = "Saket"
var salary float64 = 125000.50
var isActive bool = true
```

Here:

| Variable | Type | Value |
| --- | --- | --- |
| `age` | `int` | `40` |
| `name` | `string` | `"Saket"` |
| `salary` | `float64` | `125000.50` |
| `isActive` | `bool` | `true` |

The type is important because Go needs to know:

1. What kind of value is stored
2. How much memory is required
3. What operations are allowed
4. How the value should be interpreted

For example:

```go
var age int = 40
```

means:

> `age` can contain an integer value.

You cannot simply put a string into it:

```go
age = "forty"
```

This produces a compile-time error.

---

## 3.2 Go Is Statically Typed

Go is a **statically typed language**.

This means the type of a variable is known at compile time.

```go
var age int = 40
```

Go knows:

```text
age → int
```

Therefore:

```go
age = 50
```

is valid.

But:

```go
age = "hello"
```

is invalid.

Go catches this before the program runs.

This is one of the major differences between Go and dynamically typed languages such as JavaScript.

---

## 3.3 Go's Basic Built-in Types

Some of the most important built-in types are:

```text
bool

string

int
int8
int16
int32
int64

uint
uint8
uint16
uint32
uint64
uintptr

float32
float64

complex64
complex128

byte
rune
```

We will study these carefully.

---

## 3.4 The `int` Type

`int` represents an integer.

Examples:

```go
var age int = 40
var count int = 100
var temperature int = -10
```

Integers can be:

- positive
- negative
- zero

Examples:

```text
10
-10
0
500
-250
```

---

## 3.5 Why Does Go Have `int8`, `int16`, `int32`, `int64`?

Go provides integers of different sizes.

```text
int8
int16
int32
int64
```

The number represents the number of **bits**.

For example:

```go
var a int8 = 100
var b int16 = 1000
var c int32 = 100000
var d int64 = 10000000000
```

The ranges are:

| Type | Size | Minimum | Maximum |
| --- | --- | --- | --- |
| `int8` | 8 bits | -128 | 127 |
| `int16` | 16 bits | -32,768 | 32,767 |
| `int32` | 32 bits | -2^31 | 2^31 - 1 |
| `int64` | 64 bits | -2^63 | 2^63 - 1 |

---

## 3.6 `int` Is Special

`int` does not have a fixed size across all architectures.

It is either:

```text
32 bits
```

or:

```text
64 bits
```

On modern 64-bit systems, including your Apple Silicon Mac, `int` is normally 64 bits.

You can verify this:

```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    fmt.Println(strconv.IntSize)
}
```

You should see:

```text
64
```

`int` is generally the type you should use for ordinary integer calculations.

For example:

```go
var age int = 40
var count int = 100
```

You normally don't need to use `int64` unless the size matters.

---

## 3.7 Signed Integers

The types:

```text
int
int8
int16
int32
int64
```

are **signed integers**.

Signed means they can represent both positive and negative values.

For example:

```go
var temperature int = -5
```

is valid.

---

## 3.8 Unsigned Integers

Go also provides:

```text
uint
uint8
uint16
uint32
uint64
```

Unsigned integers cannot represent negative values.

For example:

```go
var age uint = 40
```

is valid.

But:

```go
var age uint = -1
```

is invalid.

The ranges are:

| Type | Range |
| --- | --- |
| `uint8` | 0 → 255 |
| `uint16` | 0 → 65,535 |
| `uint32` | 0 → 2^32 - 1 |
| `uint64` | 0 → 2^64 - 1 |

---

## 3.9 `uint8` and `byte`

Go provides an alias:

```go
byte
```

for:

```go
uint8
```

Therefore:

```go
var b byte = 65
```

is equivalent to:

```go
var b uint8 = 65
```

You will frequently encounter `byte` when working with:

- files
- network data
- binary data
- strings
- HTTP
- JSON
- encryption

For example:

```go
data := []byte("hello")
```

Don't worry too much about `[]byte` yet. We will study slices later.

---

## 3.10 `rune`

Go also provides:

```go
rune
```

which is an alias for:

```go
int32
```

A `rune` represents a Unicode code point.

For example:

```go
var r rune = 'A'
```

You can also use Unicode:

```go
var r rune = 'अ'
```

or:

```go
var r rune = '😊'
```

Notice something important.

A character literal uses **single quotes**:

```go
'A'
```

while a string uses **double quotes**:

```go
"A"
```

These are different.

```go
var r rune = 'A'
var s string = "A"
```

---

## 3.11 Unicode

Go has excellent Unicode support.

For example:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello")
    fmt.Println("नमस्ते")
    fmt.Println("こんにちは")
    fmt.Println("你好")
    fmt.Println("😊")
}
```

All of these are valid strings.

Unicode becomes particularly important when working with:

```text
string
rune
UTF-8
```

We will study this more deeply in the Strings & Runes chapter.

---

## 3.12 Floating-Point Numbers

Go provides:

```text
float32
float64
```

These are used for numbers containing fractional values.

For example:

```go
var price float64 = 99.99
var temperature float64 = 36.5
var percentage float64 = 98.75
```

---

## 3.13 `float32`

Example:

```go
var price float32 = 99.99
```

`float32` uses 32 bits.

It provides approximately:

```text
7 decimal digits of precision
```

---

## 3.14 `float64`

Example:

```go
var price float64 = 99.99
```

`float64` uses 64 bits.

It provides approximately:

```text
15–16 decimal digits of precision
```

For most general-purpose calculations, Go programmers normally prefer:

```text
float64
```

rather than:

```text
float32
```

unless memory or an API specifically requires `float32`.

---

## 3.15 Floating-Point Precision

This is important.

Consider:

```go
package main

import "fmt"

func main() {
    var x float64 = 0.1
    var y float64 = 0.2

    fmt.Println(x + y)
}
```

You might expect:

```text
0.3
```

but floating-point representation can produce something like:

```text
0.30000000000000004
```

This is not a Go bug.

It is a consequence of how floating-point numbers are represented in binary.

Therefore, don't blindly use `float64` for things such as financial calculations where exact decimal representation is important.

---

## 3.16 Boolean Type

Go has:

```go
bool
```

A Boolean has only two possible values:

```text
true
false
```

Example:

```go
var isLoggedIn bool = true
var isAdmin bool = false
```

Booleans are heavily used with conditions:

```go
if isLoggedIn {
    fmt.Println("Welcome")
}
```

---

## 3.17 Strings

Go's string type is:

```go
string
```

Example:

```go
var name string = "Saket"
```

Strings can contain:

- letters
- numbers
- spaces
- symbols
- Unicode

Example:

```go
var message string = "Hello, Go!"
```

---

## 3.18 Empty String

A string can be empty:

```go
var name string = ""
```

This is called the **zero value** of `string`.

More on zero values shortly.

---

## 3.19 String Literals

Double quotes:

```go
"Hello"
```

are commonly used for strings.

Example:

```go
message := "Hello World"
```

Go also supports raw string literals using backticks:

```go
message := `Hello
World`
```

This is particularly useful when you want to preserve formatting.

---

## 3.20 The Zero Value

This is one of the most important concepts in Go.

When you declare a variable without giving it an initial value, Go automatically assigns its **zero value**.

Example:

```go
var age int
```

What is `age`?

```text
0
```

Similarly:

```go
var price float64
```

gets:

```text
0
```

And:

```go
var active bool
```

gets:

```text
false
```

And:

```go
var name string
```

gets:

```text
""
```

---

## 3.21 Zero Values Table

| Type | Zero Value |
| --- | --- |
| `int` | `0` |
| `int8` | `0` |
| `int16` | `0` |
| `int32` | `0` |
| `int64` | `0` |
| `uint` | `0` |
| `float32` | `0` |
| `float64` | `0` |
| `bool` | `false` |
| `string` | `""` |

This is an important Go philosophy:

> Variables are always initialized to a meaningful zero value.

---

## 3.22 Type Inference

Go can determine the type automatically.

Instead of:

```go
var age int = 40
```

you can write:

```go
var age = 40
```

Go determines:

```text
age → int
```

Similarly:

```go
var name = "Saket"
```

becomes:

```text
name → string
```

And:

```go
var price = 99.99
```

becomes:

```text
price → float64
```

---

## 3.23 Short Variable Declaration

Inside functions, you can use:

```go
age := 40
```

This is equivalent to:

```go
var age int = 40
```

Go determines the type automatically.

Example:

```go
name := "Saket"
age := 40
salary := 100000.50
active := true
```

The inferred types are:

```text
name   → string
age    → int
salary → float64
active → bool
```

---

## 3.24 Type Is Part of the Variable

Consider:

```go
age := 40
```

`age` becomes an `int`.

You cannot later change its type:

```go
age = "forty"
```

This is invalid.

The variable remains an `int` throughout its lifetime.

You can change its **value**:

```go
age = 41
```

but not its **type**.

---

## 3.25 Go Does Not Automatically Mix Numeric Types

Consider:

```go
var age int = 40
var salary float64 = 50000.50
```

This is not automatically valid:

```go
result := age + salary
```

Why?

Because:

```text
age    → int
salary → float64
```

They are different types.

You must explicitly convert.

```go
result := float64(age) + salary
```

Now both operands are `float64`.

---

## 3.26 Type Conversion

Go provides explicit type conversion.

Example:

```go
var age int = 40

var x float64 = float64(age)
```

Now:

```text
age → int
x   → float64
```

Another example:

```go
var price float64 = 99.99

var value int = int(price)
```

The fractional part is removed.

So:

```text
99.99 → 99
```

Be careful: this is not rounding.

---

## 3.27 Conversion vs Casting

You will often hear programmers say "type casting."

In Go, the more accurate terminology is:

> **type conversion**

For example:

```go
float64(age)
```

is a type conversion.

---

## 3.28 Integer Overflow

Every integer type has a maximum value.

For example:

```go
int8
```

can hold:

```text
-128 to 127
```

Therefore:

```go
var x int8 = 127
```

is valid.

But adding one can cause overflow in situations where the operation is permitted at runtime:

```go
x++
```

The value wraps according to the integer representation.

You should understand the limits of fixed-width integer types before using them.

---

## 3.29 `uintptr`

Go also has:

```go
uintptr
```

It is an unsigned integer type large enough to hold a pointer value.

You generally **should not use `uintptr` for normal application programming**.

It is mainly relevant to:

- low-level programming
- unsafe operations
- system interfaces
- interoperability

We will not use it in normal Go development for now.

---

## 3.30 `complex64` and `complex128`

Go supports complex numbers.

```go
var x complex64
var y complex128
```

Example:

```go
var z complex128 = 3 + 4i
```

You can use:

```go
real(z)
imag(z)
```

Example:

```go
fmt.Println(real(z))
fmt.Println(imag(z))
```

Output:

```text
3
4
```

These types are uncommon in normal backend development, but they are part of Go's built-in numeric types.

---

## 3.31 `byte` vs `rune`

This distinction is very important.

```text
byte → uint8
rune → int32
```

`byte` is commonly used for raw bytes.

`rune` is commonly used for Unicode code points.

For example:

```go
var b byte = 65
var r rune = 'A'
```

Both represent something related to `A`, but conceptually they are different.

---

## 3.32 Inspecting a Variable's Type

The `fmt` package can help you inspect types.

```go
package main

import "fmt"

func main() {
    age := 40
    name := "Saket"
    price := 99.99
    active := true

    fmt.Printf("%T\n", age)
    fmt.Printf("%T\n", name)
    fmt.Printf("%T\n", price)
    fmt.Printf("%T\n", active)
}
```

Output:

```text
int
string
float64
bool
```

`%T` means:

> print the type of the value.

---

## 3.33 `%v` vs `%T`

These are useful when debugging.

```go
fmt.Printf("%v\n", age)
```

prints the value.

```go
fmt.Printf("%T\n", age)
```

prints the type.

Example:

```go
fmt.Printf("Value: %v\n", age)
fmt.Printf("Type: %T\n", age)
```

Output:

```text
Value: 40
Type: int
```

---

## 3.34 `int` vs `int64`

A common beginner question is:

> Should I always use `int64`?

No.

For normal application code:

```go
age := 40
count := 100
```

is perfectly normal.

Use a specific type such as:

```go
int64
```

when the domain or API requires that specific representation.

This becomes particularly relevant when working with:

- databases
- timestamps
- binary protocols
- external APIs
- large numeric values

---

## 3.35 A Complete Example

```go
package main

import "fmt"

func main() {
    var name string = "Saket"
    var age int = 40
    var salary float64 = 125000.50
    var experience int = 17
    var active bool = true

    fmt.Println("Name:", name)
    fmt.Println("Age:", age)
    fmt.Println("Salary:", salary)
    fmt.Println("Experience:", experience)
    fmt.Println("Active:", active)
}
```

---

# Exercises

## Exercise 1 — Basic Variables

Create a program with these variables:

```text
name
age
salary
isEmployed
```

Choose appropriate data types.

Print all four values.

---

## Exercise 2 — Identify the Types

Without running the program, determine the type of each variable:

```go
a := 10
b := 10.5
c := "10"
d := true
e := 'A'
```

Write:

```text
a → ?
b → ?
c → ?
d → ?
e → ?
```

Then verify using `%T`.

---

## Exercise 3 — Zero Values

Write a program:

```go
var age int
var price float64
var active bool
var name string
```

Print all four.

Before running it, predict:

```text
age    → ?
price  → ?
active → ?
name   → ?
```

Then verify.

---

## Exercise 4 — Integer Types

Create variables of each type:

```text
int8
int16
int32
int64
uint8
uint16
uint32
uint64
```

Assign an appropriate value to each.

Print their values and types.

Use:

```go
fmt.Printf("%T %v\n", variable, variable)
```

---

## Exercise 5 — Find the Limits

Create an `int8` variable:

```go
var x int8 = 127
```

Print it.

Then experiment with:

```go
x++
```

Observe what happens.

Now try similar experiments with:

```go
uint8
int16
```

Record your observations.

---

## Exercise 6 — Type Conversion

Create:

```go
age := 40
```

Convert it to:

```go
float64
```

and store the result in another variable.

Print:

```text
original value
original type
converted value
converted type
```

---

## Exercise 7 — Floating Point to Integer

Create:

```go
price := 99.99
```

Convert it to `int`.

Print both values.

Answer:

> Did Go round the number or truncate it?

---

## Exercise 8 — Mixed Numeric Calculation

Create:

```go
age := 40
height := 5.9
```

Calculate:

```go
age + height
```

You will discover that Go doesn't automatically combine `int` and `float64`.

Fix the program using explicit conversion.

---

## Exercise 9 — Boolean Logic

Create:

```go
isLoggedIn := true
isAdmin := false
```

Print:

```text
Is logged in?
Is admin?
```

Then create:

```go
canAccess := ...
```

where access is allowed only when the user is logged in **and** is an admin.

---

## Exercise 10 — `byte`

Create:

```go
var b byte = 65
```

Print:

```go
fmt.Println(b)
```

Then investigate how to print it as a character.

Try:

```go
fmt.Printf("%c\n", b)
```

What do you get?

---

## Exercise 11 — `rune`

Create:

```go
var r rune = 'A'
```

Print:

```text
value
type
```

Then try:

```go
var r rune = 'अ'
```

and:

```go
var r rune = '😊'
```

Observe the values.

---

## Exercise 12 — `byte` vs `rune`

Create:

```go
var b byte = 'A'
var r rune = 'A'
```

Print:

```go
fmt.Printf("%T %v\n", b, b)
fmt.Printf("%T %v\n", r, r)
```

Explain the difference.

---

## Exercise 13 — Unicode

Create a program that prints:

```text
Your name
A Hindi word
A Japanese word
An emoji
```

For example:

```text
Saket
नमस्ते
こんにちは
😊
```

Then investigate the type of a string variable containing these values.

---

## Exercise 14 — String Length

Create:

```go
name := "Saket"
```

Use:

```go
len(name)
```

Print the result.

Then try:

```go
name := "नमस्ते"
```

Compare the result.

**Important:** Don't immediately assume `len(string)` means "number of human-readable characters." Investigate what it actually counts.

---

## Exercise 15 — Type Inspection

Create variables:

```go
name := "Saket"
age := 40
height := 5.9
active := true
letter := 'A'
```

Print their:

```text
value
type
```

using `%v` and `%T`.

---

## Exercise 16 — Predict Before Running

What do you think this program prints?

```go
package main

import "fmt"

func main() {
    var a int = 10
    var b int = 20

    fmt.Println(a + b)
}
```

Then change it to:

```go
var a int = 10
var b float64 = 20
```

What happens?

Why?

---

## Exercise 17 — Variable Type Cannot Change

Start with:

```go
age := 40
```

Try:

```go
age = 41
```

Then try:

```go
age = "forty"
```

Observe the compiler error.

Explain:

> Why can the value change but the type cannot?

---

## Exercise 18 — Build an Employee

Create these variables:

```text
name
age
salary
experience
isManager
```

Choose appropriate types.

Print a nicely formatted employee profile.

Example:

```text
Name: Saket
Age: 40
Salary: 125000
Experience: 17
Manager: true
```

---

## Exercise 19 — Salary Calculation

Create:

```go
salary := 100000.0
bonus := 15000.0
```

Calculate:

```text
totalSalary
```

Then calculate:

```text
tax = 10% of totalSalary
```

Finally calculate:

```text
netSalary
```

Print all values.

---

## Exercise 20 — Temperature Conversion

Create:

```go
celsius := 30.0
```

Convert it to Fahrenheit.

Formula:

```text
F = C × 9/5 + 32
```

Print:

```text
Celsius
Fahrenheit
```

---

## Exercise 21 — Area of a Circle

Create:

```go
radius := 5.0
```

Calculate:

```text
area = π × radius × radius
```

Use:

```go
math.Pi
```

You will need:

```go
import "math"
```

---

## Exercise 22 — Data Type Investigation

Write a program that prints the type of:

```text
10
10.0
"10"
true
'A'
```

Use `%T`.

Try to predict all five before running.

---

## Exercise 23 — Conversion Chain

Start with:

```go
var a int = 100
```

Convert:

```text
int → float64
float64 → int
```

Print the value and type after every conversion.

---

## Exercise 24 — Large Numbers

Create variables for:

```text
population
distance
bankBalance
```

Choose appropriate types.

Think carefully:

> Should each one be `int`, `int64`, or `float64`?

There isn't necessarily one universal answer. Explain your choices.

---

## Exercise 25 — Debugging Challenge

This program doesn't compile:

```go
package main

import "fmt"

func main() {
    var age int = 40
    var salary float64 = 50000.50

    total := age + salary

    fmt.Println(total)
}
```

Fix it.

Then explain exactly **why** the original program failed.

---

## Exercise 26 — Debugging Challenge

Find the problem:

```go
package main

import "fmt"

func main() {
    var age int
    age = "40"

    fmt.Println(age)
}
```

Fix it in two different ways.

---

## Exercise 27 — Zero Value Challenge

Write a program containing:

```go
var a int
var b float64
var c bool
var d string
```

Without assigning values, calculate/print their zero values.

Then answer:

> Why doesn't Go leave these variables containing random garbage data?

---

## Exercise 28 — Type Conversion Challenge

You have:

```go
age := 40
height := 5.9
```

Create:

```text
ageAsFloat
heightAsInt
```

Then calculate:

```text
result = ageAsFloat + height
```

and print the result.

---

## Exercise 29 — Mini Employee Calculator

Create:

```text
employee name
monthly salary
months worked
bonus
```

Calculate:

```text
annual salary
total compensation
```

For example:

```text
annual salary = monthly salary × 12
total compensation = annual salary + bonus
```

Print a complete report.

---

## Exercise 30 — Type Detective

For every expression below, predict its type:

```text
10
10.5
"hello"
true
'A'
10 + 20
10.0 + 20.0
"hello" + "world"
```

Then verify each one with `%T`.

---

## Exercise 31 — Challenge: `byte`

Create:

```go
var a byte = 65
var b byte = 66
var c byte = 67
```

Print:

```text
65
66
67
```

Then print the corresponding characters.

Try to figure out what relationship exists between:

```text
65 → ?
66 → ?
67 → ?
```

---

## Exercise 32 — Challenge: Rune

Create a program containing:

```go
r1 := 'A'
r2 := 'अ'
r3 := '😊'
```

Print:

```text
value
type
```

for each.

Then compare the numeric values.

---

## Exercise 33 — Mini Data Report

Create a program representing a person:

```text
name
age
height
weight
isEmployed
country
```

Print the person's information.

Then print the type of every field.

Example:

```text
Name: Saket
Type: string

Age: 40
Type: int
```

---

## Exercise 34 — No `:=` Allowed

Write a program using only `var`.

Do not use:

```go
:=
```

Create:

```text
name
age
salary
active
```

Then rewrite the same program using `:=`.

Compare the two approaches.

---

## Exercise 35 — No Explicit Types

Now do the opposite.

Do not explicitly write:

```text
int
float64
string
bool
```

Use type inference wherever possible.

For example:

```go
name := "Saket"
```

Then use `%T` to verify what Go inferred.

---

## Exercise 36 — Mixed Type Calculator

Create:

```go
quantity := 10
price := 99.50
discount := 5.0
```

Calculate:

```text
subtotal
discountAmount
finalPrice
```

Be careful about the different types.

---

## Exercise 37 — Find the Bug

What is wrong here?

```go
var age int = 40
var name string = "Saket"

fmt.Println(age + name)
```

Explain the problem rather than simply deleting the line.

---

## Exercise 38 — Predict the Output

Before running:

```go
package main

import "fmt"

func main() {
    var a int
    var b float64
    var c bool
    var d string

    fmt.Printf("%v\n", a)
    fmt.Printf("%v\n", b)
    fmt.Printf("%v\n", c)
    fmt.Printf("%q\n", d)
}
```

Predict the output.

Then run it.

---

## Exercise 39 — Challenge: Architecture

Write a program that prints:

```text
Size of int
```

Use:

```go
strconv.IntSize
```

Then determine whether your machine is using:

```text
32-bit int
```

or:

```text
64-bit int
```

This connects back to Chapter 1's discussion of CPU architecture.

---

## Exercise 40 — Final Challenge

Create a small program representing a bank account.

Use appropriate data types for:

```text
account holder
account number
balance
active
number of transactions
interest rate
```

Then calculate:

```text
interest earned
new balance
```

Print:

```text
Account Holder
Account Number
Balance
Interest Rate
Interest Earned
New Balance
Active
Transactions
```

For every variable, be prepared to explain:

> **Why did you choose this particular data type?**

---

# 3.76 Final Conceptual Questions

Before moving to Chapter 4, make sure you can answer these **without looking at the notes**.

### Question 1
What does a data type tell Go?

### Question 2
Is Go statically typed or dynamically typed?

### Question 3
What is the difference between:

```go
var age int
```

and:

```go
var age int = 40
```

### Question 4
What is the zero value of:

```text
int
float64
bool
string
```

### Question 5
What is the difference between:

```text
int
int8
int16
int32
int64
```

### Question 6
What is the difference between:

```text
uint
int
```

### Question 7
What is:

```go
byte
```

an alias for?

### Question 8
What is:

```go
rune
```

an alias for?

### Question 9
What is the difference between:

```go
'A'
```

and:

```go
"A"
```

### Question 10
What is the difference between:

```go
int
```

and:

```go
int64
```

### Question 11
Why doesn't this work?

```go
var age int = 40
var salary float64 = 50000

result := age + salary
```

### Question 12
How do you fix it?

### Question 13
What does this do?

```go
float64(age)
```

### Question 14
Is this rounding?

```go
int(99.99)
```

### Question 15
What does `%T` do?

### Question 16
What does `%v` do?

### Question 17
What type does Go normally infer for:

```go
x := 10
```

### Question 18
What type does Go normally infer for:

```go
x := 10.5
```

### Question 19
Can a variable change its type after declaration?

### Question 20
Why does Go have both `byte` and `rune`?

---

## Chapter 3 — What You Should Be Able to Do

By the end of this chapter, you should be comfortable with:

```text
✓ What a data type is
✓ Static typing
✓ int
✓ int8/int16/int32/int64
✓ uint types
✓ float32
✓ float64
✓ bool
✓ string
✓ byte
✓ rune
✓ complex numbers (basic awareness)
✓ uintptr (basic awareness)
✓ Zero values
✓ Type inference
✓ :=
✓ Explicit type conversion
✓ Numeric type differences
✓ Integer overflow
✓ Floating-point precision
✓ %T
✓ %v
✓ Unicode basics
```

### Recommended practice order

Don't do all 40 exercises mechanically.

I recommend:

**Day 1**

```text
Exercise 1–15
```

Focus on understanding types.

**Day 2**

```text
Exercise 16–30
```

Focus on conversion, zero values, and debugging.

**Day 3**

```text
Exercise 31–40
```

Focus on `byte`, `rune`, numeric decisions, and real-world problems.

Then try the **20 conceptual questions without looking at the chapter**.

The most important goal is not memorizing the list of types. You should reach the point where, when you see a piece of data, you can naturally ask:

> **"What type should this data have, and why?"**

That decision-making ability will become especially important when we reach **Structs, JSON, PostgreSQL, APIs, and database types** later in your Go course.
