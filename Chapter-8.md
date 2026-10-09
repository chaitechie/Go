# Chapter 8 — Arrays

Arrays are one of the fundamental data structures in Go. They are simple, but they are also important because they help you understand how **multiple values are stored together**, how **indexes work**, and later why **slices** exist.

We will learn arrays from the beginning and then do exercises.

---

## 8.1 What is an Array?

An **array** is a collection of a fixed number of values of the **same type**.

For example, suppose we want to store the ages of five people.

Without an array:

```go
age1 := 20
age2 := 25
age3 := 30
age4 := 35
age5 := 40
```

We have five separate variables.

With an array:

```go
ages := [5]int{20, 25, 30, 35, 40}
```

Now one variable, `ages`, contains five `int` values.

Conceptually:

```text
ages
 │
 ├── 20
 ├── 25
 ├── 30
 ├── 35
 └── 40
```

The individual values are accessed using an **index**.

```go
ages[0]
ages[1]
ages[2]
ages[3]
ages[4]
```

The result is:

```text
ages[0] → 20
ages[1] → 25
ages[2] → 30
ages[3] → 35
ages[4] → 40
```

Notice something very important:

> **Array indexes start at 0, not 1.**

---

## 8.2 Why Do We Need Arrays?

Imagine you want to store the marks of 100 students.

Creating 100 variables would be extremely inconvenient:

```go
mark1 := 85
mark2 := 72
mark3 := 91
mark4 := 67
...
mark100 := 88
```

Instead:

```go
marks := [100]int{
    85,
    72,
    91,
    67,
    // ...
}
```

Now all 100 values belong to one array:

```text
marks
  │
  ├── index 0
  ├── index 1
  ├── index 2
  ├── index 3
  ├── ...
  └── index 99
```

This becomes particularly useful when working with loops.

For example:

```go
for i := 0; i < 100; i++ {
    fmt.Println(marks[i])
}
```

We will study loops in more detail when we reach control flow, but the important idea here is:

> Arrays allow us to group multiple values under one variable name.

---

## 8.3 Array Syntax

The basic syntax for an array is:

```text
var arrayName [size]type
```

For example:

```go
var ages [5]int
```

This means:

```text
variable name → ages
number of elements → 5
element type → int
```

So:

```go
var ages [5]int
```

creates an array capable of holding exactly **5 integers**.

---

## 8.4 Declaring an Array

Let's create an array:

```go
package main

import "fmt"

func main() {
    var ages [5]int

    fmt.Println(ages)
}
```

Output:

```text
[0 0 0 0 0]
```

Why are all values `0`?

Because Go automatically gives variables their **zero value** when they are declared without an initial value.

For an `int`, the zero value is:

```text
0
```

Therefore:

```go
var ages [5]int
```

starts as:

```text
[0 0 0 0 0]
```

---

## 8.5 Array Size

The number inside `[ ]` is the **array length**.

```go
var a [5]int
```

Length:

```text
5
```

Another:

```go
var b [10]int
```

Length:

```text
10
```

Another:

```go
var c [100]string
```

Length:

```text
100
```

The array length is part of the array's type.

This is extremely important.

For example:

```go
[5]int
```

and:

```go
[10]int
```

are **different types**.

They are not the same array type.

---

## 8.6 Array Index

Every element in an array has an index.

Consider:

```go
ages := [5]int{20, 25, 30, 35, 40}
```

The indexes are:

```text
Index       Value

  0    →     20
  1    →     25
  2    →     30
  3    →     35
  4    →     40
```

The first element is:

```go
ages[0]
```

The second:

```go
ages[1]
```

The third:

```go
ages[2]
```

The last:

```go
ages[4]
```

---

## 8.7 Why Does Indexing Start at 0?

This is a convention used by many programming languages.

If an array contains 5 elements:

```go
ages := [5]int{20, 25, 30, 35, 40}
```

there are 5 positions:

```text
0
1
2
3
4
```

Therefore:

```text
number of elements = 5
last index         = 4
```

In general:

```text
last index = length - 1
```

So:

```text
[5]int → indexes 0 through 4
[10]int → indexes 0 through 9
[100]int → indexes 0 through 99
```

---

## 8.8 Reading an Array Element

We can retrieve an individual element using its index.

```go
package main

import "fmt"

func main() {
    ages := [5]int{20, 25, 30, 35, 40}

    fmt.Println(ages[0])
    fmt.Println(ages[1])
    fmt.Println(ages[4])
}
```

Output:

```text
20
25
40
```

---

## 8.9 Changing an Array Element

Array elements can be modified.

```go
ages := [5]int{20, 25, 30, 35, 40}

ages[2] = 31
```

Originally:

```text
[20 25 30 35 40]
```

After:

```go
ages[2] = 31
```

The array becomes:

```text
[20 25 31 35 40]
```

Complete example:

```go
package main

import "fmt"

func main() {
    ages := [5]int{20, 25, 30, 35, 40}

    fmt.Println(ages)

    ages[2] = 31

    fmt.Println(ages)
}
```

Output:

```text
[20 25 30 35 40]
[20 25 31 35 40]
```

---

## 8.10 Array Initialization

There are several ways to initialize an array.

### Method 1 — Declare first

```go
var numbers [5]int
```

Then assign values:

```go
numbers[0] = 10
numbers[1] = 20
numbers[2] = 30
numbers[3] = 40
numbers[4] = 50
```

Result:

```text
[10 20 30 40 50]
```

### Method 2 — Initialize directly

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

This creates the array and initializes it immediately.

---

## 8.11 Partial Array Initialization

You don't necessarily have to provide a value for every element.

For example:

```go
numbers := [5]int{10, 20}
```

The result is:

```text
[10 20 0 0 0]
```

The unspecified elements receive their zero value.

For `int`:

```text
0
```

For `string`:

```text
""
```

For `bool`:

```text
false
```

---

## 8.12 Array of Strings

Arrays aren't limited to integers.

```go
names := [3]string{"Saket", "Rahul", "Amit"}
```

Conceptually:

```text
index       value

  0       Saket
  1       Rahul
  2       Amit
```

Access:

```go
fmt.Println(names[0])
```

Output:

```text
Saket
```

---

## 8.13 Array of Booleans

You can also have:

```go
flags := [3]bool{true, false, true}
```

Result:

```text
[true false true]
```

---

## 8.14 Array of Floating-Point Numbers

For example:

```go
prices := [4]float64{10.5, 20.75, 30.25, 40.99}
```

---

## 8.15 The Array Type

This is an important Go concept.

Consider:

```go
a := [3]int{10, 20, 30}
```

The type of `a` is:

```text
[3]int
```

Not:

```text
int
```

The `3` is part of the type.

Similarly:

```go
b := [5]int{10, 20, 30, 40, 50}
```

has type:

```text
[5]int
```

Therefore:

```text
[3]int
```

and:

```text
[5]int
```

are different types.

This is one of the important differences between **arrays and slices**.

---

## 8.16 Checking the Type

You can use `%T` with `fmt.Printf`:

```go
package main

import "fmt"

func main() {
    numbers := [3]int{10, 20, 30}

    fmt.Printf("%T\n", numbers)
}
```

Output:

```text
[3]int
```

The output tells us:

```text
[3]int
│ │
│ └── element type
└──── number of elements
```

---

## 8.17 Array Length

Go provides the built-in `len()` function.

For example:

```go
numbers := [5]int{10, 20, 30, 40, 50}

fmt.Println(len(numbers))
```

Output:

```text
5
```

So:

```go
len(numbers)
```

means:

> Give me the number of elements in `numbers`.

---

## 8.18 `len()` and Last Index

Suppose:

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

Then:

```go
len(numbers)
```

is:

```text
5
```

But the last index is:

```text
4
```

Because:

```text
last index = length - 1
```

Therefore:

```go
numbers[len(numbers)-1]
```

means:

```go
numbers[4]
```

and returns:

```text
50
```

This pattern becomes extremely useful when working with arrays and slices.

---

## 8.19 Accessing an Invalid Index

Consider:

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

Valid indexes are:

```text
0
1
2
3
4
```

This is invalid:

```go
numbers[5]
```

because index `5` doesn't exist.

Running the program will produce a runtime panic similar to:

```text
panic: runtime error: index out of range
```

Similarly:

```go
numbers[-1]
```

is invalid.

Go arrays do **not** support negative indexes.

---

## 8.20 Arrays and `for` Loops

Arrays become much more useful when combined with loops.

For example:

```go
numbers := [5]int{10, 20, 30, 40, 50}

for i := 0; i < len(numbers); i++ {
    fmt.Println(numbers[i])
}
```

This prints:

```text
10
20
30
40
50
```

The important relationship is:

```text
i = 0 → numbers[0]
i = 1 → numbers[1]
i = 2 → numbers[2]
i = 3 → numbers[3]
i = 4 → numbers[4]
```

---

## 8.21 Array Assignment

Arrays are **value types** in Go.

This is very important.

Consider:

```go
a := [3]int{10, 20, 30}

b := a
```

Now:

```text
a → [10 20 30]

b → [10 20 30]
```

At this point, `b` contains a copy of the array.

If we change `b`:

```go
b[0] = 100
```

we get:

```text
a → [10 20 30]

b → [100 20 30]
```

Changing `b` did not change `a`.

Example:

```go
package main

import "fmt"

func main() {
    a := [3]int{10, 20, 30}

    b := a

    b[0] = 100

    fmt.Println(a)
    fmt.Println(b)
}
```

Output:

```text
[10 20 30]
[100 20 30]
```

This is an important property of arrays.

---

## 8.22 Arrays Are Not References

A common beginner assumption is:

```go
b := a
```

means:

> "b points to the same array as a."

For ordinary array assignment, that's **not** what happens.

Instead:

```text
a
┌───────────────┐
│ 10 │ 20 │ 30 │
└───────────────┘

       ↓ copy

b
┌───────────────┐
│ 10 │ 20 │ 30 │
└───────────────┘
```

There are two arrays.

This will become especially important when we compare arrays with slices.

---

## 8.23 Arrays as Function Arguments

Because arrays are value types, passing an array to a function normally passes a copy.

Example:

```go
package main

import "fmt"

func change(numbers [3]int) {
    numbers[0] = 100
}

func main() {
    numbers := [3]int{10, 20, 30}

    change(numbers)

    fmt.Println(numbers)
}
```

Output:

```text
[10 20 30]
```

Why didn't the original array change?

Because:

```go
change(numbers)
```

passes a copy of the array.

Conceptually:

```text
main's array
[10 20 30]

       ↓ copy

function's array
[10 20 30]
```

The function modifies its own copy.

---

## Pass-by-Value and Go Data Types

Yes, Go is strictly a pass-by-value language. Whenever you pass an argument to a function, Go always creates a copy of that value and passes the copy to the function.

However, because certain data types are structured internally, this behavior can sometimes feel like "pass-by-reference".

### How Different Types Behave Under Pass-by-Value

#### 1. Primitives, Structs, and Arrays

When you pass basic types such as `int`, `string`, and `bool`, as well as `struct` and array types, Go copies the entire value. Any modifications made inside the function do not affect the original variable.

```go
type Person struct {
    Name string
}

func modify(p Person) {
    p.Name = "Bob"
}
```

The original value remains unchanged:

```go
person := Person{Name: "Alice"}
modify(person)

fmt.Println(person.Name) // Alice
```

#### 2. Pointers

When you pass a pointer such as `*int` or `*Person`, Go copies the pointer itself—the memory address. The copied pointer still points to the same memory location as the original pointer.

Therefore, modifying the dereferenced value changes the original data.

```go
func modifyPointer(p *Person) {
    p.Name = "Bob"
}

person := Person{Name: "Alice"}
modifyPointer(&person)

fmt.Println(person.Name) // Bob
```

The pointer value is copied, but the memory it points to is shared.

#### 3. Slices, Maps, and Channels

Slices, maps, and channels are often called "reference types," but they are still passed by value. Internally, these types contain a header structure with a pointer to the underlying data store and metadata such as length or capacity.

When you pass a slice, map, or channel, Go copies the header. The copied header points to the same underlying data storage as the original header.

```go
func modifySlice(s []int) {
    s[0] = 999
}

numbers := []int{10, 20, 30}
modifySlice(numbers)

fmt.Println(numbers) // [999 20 30]
```

The slice header is copied, but its underlying array is shared.

### Summary Comparison

| Data type | What is copied? | Can the function modify the original data? |
| --- | --- | --- |
| Primitive types | The complete value | No |
| Structs | The complete value | No |
| Arrays | The complete value | No |
| Pointers | The pointer value, including its address | Yes, by dereferencing and modifying the shared value |
| Slices | The slice header | Yes, by modifying elements; no when reassigning the slice itself |
| Maps | The map header | Yes, by modifying entries; no when reassigning the map variable |
| Channels | The channel header | No direct element modification; the channel value itself is copied |

### Reassigning vs Modifying

A function can modify the underlying data of a slice or map, but it cannot change the caller's slice or map variable by simply assigning a new value.

```go
func replaceSlice(s []int) {
    s = []int{1, 2, 3}
}

numbers := []int{10, 20, 30}
replaceSlice(numbers)

fmt.Println(numbers) // [10 20 30]
```

The local `s` variable is reassigned, while the caller's `numbers` variable remains unchanged.

The key distinction is:

- **Passing by value** copies the value.
- **Passing a pointer** copies the address and shares the referenced data.
- **Passing a slice, map, or channel** copies its header, which may still reference shared underlying data.

---

## 8.24 Arrays with `range`

Go provides another convenient way to iterate over arrays: `range`.

Example:

```go
numbers := [5]int{10, 20, 30, 40, 50}

for index, value := range numbers {
    fmt.Println(index, value)
}
```

Output:

```text
0 10
1 20
2 30
3 40
4 50
```

Here:

```text
index
```

contains the array index.

And:

```text
value
```

contains the value at that index.

So:

```text
index   value
  0       10
  1       20
  2       30
  3       40
  4       50
```

---

## 8.25 Ignoring the Index

Sometimes you only want the values.

You can use `_`:

```go
numbers := [5]int{10, 20, 30, 40, 50}

for _, value := range numbers {
    fmt.Println(value)
}
```

Output:

```text
10
20
30
40
50
```

The `_` means:

> I don't need this value.

---

## 8.26 Ignoring the Value

You can also use only the index:

```go
numbers := [5]int{10, 20, 30, 40, 50}

for index := range numbers {
    fmt.Println(index)
}
```

Output:

```text
0
1
2
3
4
```

---

## 8.27 Array Literal with Inferred Length

Go provides a convenient syntax:

```go
numbers := [...]int{10, 20, 30, 40, 50}
```

Notice:

```text
[...]
```

instead of:

```text
[5]
```

Go determines the length automatically.

Therefore:

```go
numbers := [...]int{10, 20, 30, 40, 50}
```

creates:

```text
[5]int
```

because there are five elements.

You can verify:

```go
fmt.Printf("%T\n", numbers)
```

Output:

```text
[5]int
```

This is still an **array**, not a slice.

---

## 8.28 Array with Explicit Element Positions

Go also allows you to specify particular indexes.

For example:

```go
numbers := [5]int{
    0: 10,
    3: 40,
}
```

The result is:

```text
[10 0 0 40 0]
```

Because:

```text
index 0 → 10
index 3 → 40
```

The other positions receive their zero value.

Another example:

```go
numbers := [10]int{
    2: 100,
    7: 500,
}
```

Result:

```text
[0 0 100 0 0 0 0 500 0 0]
```

This syntax is useful in some specialized situations.

---

## 8.29 Arrays of Structs

Arrays can contain any Go type, including structs.

For example:

```go
type Person struct {
    Name string
    Age  int
}
```

We can create:

```go
people := [2]Person{
    {Name: "Saket", Age: 40},
    {Name: "Rahul", Age: 35},
}
```

Access:

```go
fmt.Println(people[0].Name)
```

Output:

```text
Saket
```

And:

```go
fmt.Println(people[1].Age)
```

Output:

```text
35
```

This becomes useful when we start building real applications.

---

## 8.30 Multidimensional Arrays

Go also supports arrays containing arrays.

For example:

```go
var matrix [2][3]int
```

This means:

```text
2 rows
3 columns
```

Conceptually:

```text
        columns
       0   1   2

row 0 [ 0   0   0 ]

row 1 [ 0   0   0 ]
```

We can initialize it:

```go
matrix := [2][3]int{
    {1, 2, 3},
    {4, 5, 6},
}
```

Then:

```text
[1 2 3]
[4 5 6]
```

Access:

```go
matrix[0][0]
```

gives:

```text
1
```

And:

```go
matrix[1][2]
```

gives:

```text
6
```

The first index selects the row.

The second index selects the column.

---

## 8.31 Array Memory Concept

Let's consider:

```go
numbers := [4]int{10, 20, 30, 40}
```

Conceptually, the array contains four elements stored as one fixed-size collection:

```text
numbers

┌────────┬────────┬────────┬────────┐
│   10   │   20   │   30   │   40   │
└────────┴────────┴────────┴────────┘
    0        1        2        3
```

Each element has an index.

The exact physical memory layout and size depend on the element type and architecture, but conceptually the elements form a contiguous fixed-size array.

This is one reason arrays are efficient.

---

## 8.32 Array Length Cannot Change

This is one of the most important characteristics of an array.

If you create:

```go
numbers := [3]int{10, 20, 30}
```

it always has exactly three elements.

You cannot do something like:

```go
numbers = [4]int{10, 20, 30, 40}
```

because:

```text
[3]int
```

and:

```text
[4]int
```

are different types.

The size of an array is fixed.

This limitation is one of the reasons Go provides **slices**.

---

## 8.33 Arrays vs Slices — First Look

We will study slices separately, but it is useful to see the fundamental difference.

### Array

```go
numbers := [3]int{10, 20, 30}
```

Fixed length:

```text
3 elements
```

### Slice

```go
numbers := []int{10, 20, 30}
```

A slice can grow:

```go
numbers = append(numbers, 40)
```

Now:

```text
[10 20 30 40]
```

So remember:

```text
Array  → fixed-size collection
Slice  → flexible/dynamic view over an array
```

Do not confuse:

```text
[3]int
```

with:

```text
[]int
```

They are fundamentally different types.

---

## 8.34 Important Array Rules

Keep these rules in mind:

### Rule 1

An array contains elements of the same type.

```text
[3]int
[5]string
[4]bool
```

### Rule 2

The array length is fixed.

```text
[5]int
```

always has five elements.

### Rule 3

Array indexes start at zero.

```text
[5]int
```

has indexes:

```text
0 1 2 3 4
```

### Rule 4

The array length is part of its type.

```text
[3]int != [5]int
```

as Go types.

### Rule 5

Arrays are value types.

```go
b := a
```

copies the array.

### Rule 6

Use `len()` to get the number of elements.

```go
len(numbers)
```

### Rule 7

Access elements with:

```go
numbers[index]
```

---

## 8.35 Complete Example

Let's put several concepts together:

```go
package main

import "fmt"

func main() {
    ages := [5]int{20, 25, 30, 35, 40}

    fmt.Println("Array:", ages)
    fmt.Println("Length:", len(ages))

    fmt.Println("First:", ages[0])
    fmt.Println("Last:", ages[len(ages)-1])

    ages[2] = 31

    fmt.Println("After modification:", ages)

    for index, value := range ages {
        fmt.Println(index, value)
    }
}
```

Output:

```text
Array: [20 25 30 35 40]
Length: 5
First: 20
Last: 40
After modification: [20 25 31 35 40]

0 20
1 25
2 31
3 35
4 40
```

---




## Why the Size Is Part of an Array's Type

In Go, the size of an array is part of its type because an array's length is a fixed, compile-time property that dictates its memory layout and value semantics.

### Key Reasons for This Design

- **Compile-time memory layout:** Go arrays are stored as a single, contiguous block of memory. Knowing the exact number of elements at compile time allows the compiler to calculate the exact amount of memory needed to allocate the array, such as on the stack, and optimize offset math for element access.
- **Distinct types for safety and type checking:** Making the size part of the type means `[3]int` and `[4]int` are completely different, incompatible types. You cannot accidentally pass or assign a 4-element array to a function expecting a 3-element array, preventing buffer overflows and size mismatches.
- **Value semantics:** In Go, arrays are true values rather than pointers to a first element, unlike C or C++. When you assign an array to a new variable or pass it to a function, the entire array is copied. The compiler must know the exact size to determine the size of the copy operation.
- **Simplicity and performance:** Because the size is fixed and known statically, Go avoids the runtime overhead, pointer indirection, and dynamic header bookkeeping required for resizable collections.

If you need a collection where the size can vary or is determined at runtime, Go uses slices (`[]int`) instead of arrays (`[N]int`), which abstract away an underlying array and separate the variable's type from its current length.

> Would you like to explore how slices manage dynamic sizing under the hood compared to fixed arrays?

### References

1. [Arrays in Go](https://www.igmguru.com/blog/arrays-in-go)
2. [Understanding Arrays and Slices in Go](https://medium.com/@krsaurabh.dev/understanding-arrays-and-slices-in-go-48915eb275ed)
3. [Go Arrays](https://mrparish.medium.com/golang-arrays-833ade9f2f3b)
4. [Go Slices Introduction](https://go.dev/blog/slices-intro)
5. [Go Arrays Data Type](https://www.reddit.com/r/golang/comments/122hmvu/might_you_not_know_go_has_a_strange_data_type/)
6. [Go Array](https://victoriametrics.com/blog/go-array/)
7. [Arrays, Slices, and Maps in Go](https://www.freecodecamp.org/news/arrays-slices-and-maps-in-go-a-quick-guide-to-collection-types/)
8. [How Do I Find the Size of the Array in Go?](https://stackoverflow.com/questions/35937828/how-do-i-find-the-size-of-the-array-in-go)
9. [Arrays in Go Are by Value](https://stackoverflow.com/questions/51183003/arrays-in-go-are-by-value)

---

## 8.36 Exercises

Since your goal is to learn Go **by doing**, don't just read these. Type each program yourself.

### Exercise 1 — Create an Array

Create an array containing the ages:

```text
20
25
30
35
40
```

Print the complete array.

Expected:

```text
[20 25 30 35 40]
```

### Exercise 2 — Access Elements

Using the same array, print:

1. First element
2. Second element
3. Last element

Expected:

```text
20
25
40
```

### Exercise 3 — Modify an Element

Create:

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

Change the third element to:

```text
100
```

Expected:

```text
[10 20 100 40 50]
```

### Exercise 4 — Zero Values

Create:

```go
var numbers [5]int
```

Print the array.

Then assign:

```text
10
20
30
```

to the first three positions.

What does the final array contain?

### Exercise 5 — Strings

Create an array containing three names.

Print:

```text
First name:
Second name:
Third name:
```

### Exercise 6 — Find the Length

Create:

```go
numbers := [7]int{10, 20, 30, 40, 50, 60, 70}
```

Print its length using `len()`.

Expected:

```text
7
```

### Exercise 7 — Last Element

Without writing the number `6`, print the last element of:

```go
numbers := [7]int{10, 20, 30, 40, 50, 60, 70}
```

**Hint:**

Think about:

```go
len(numbers)
```

### Exercise 8 — Array Loop

Create:

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

Use a `for` loop to print every element.

Expected:

```text
10
20
30
40
50
```

### Exercise 9 — Index and Value

Using `range`, print:

```text
Index: 0 Value: 10
Index: 1 Value: 20
...
```

for:

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

### Exercise 10 — Sum of Array

Given:

```go
numbers := [5]int{10, 20, 30, 40, 50}
```

calculate the sum.

Expected:

```text
150
```

Don't manually write:

```text
10 + 20 + 30 + 40 + 50
```

Use a loop.

### Exercise 11 — Find the Largest Number

Given:

```go
numbers := [5]int{45, 12, 78, 23, 91}
```

find the largest number.

Expected:

```text
91
```

### Exercise 12 — Copy an Array

Create:

```go
a := [3]int{10, 20, 30}
```

Copy it:

```go
b := a
```

Then change:

```go
b[0]
```

to:

```text
100
```

Print both arrays.

You should observe:

```text
a = [10 20 30]
b = [100 20 30]
```

This exercise is important for understanding **value semantics**.

### Exercise 13 — Array Type

Create:

```go
a := [3]int{10, 20, 30}
b := [5]int{10, 20, 30, 40, 50}
```

Print their types using:

```go
fmt.Printf("%T\n", a)
fmt.Printf("%T\n", b)
```

Observe the difference.

### Exercise 14 — `[...]`

Create an array using:

```go
numbers := [...]int{10, 20, 30, 40}
```

Print:

1. The array
2. Its length
3. Its type

Expected type:

```text
[4]int
```

### Exercise 15 — Two-Dimensional Array

Create:

```text
1 2 3
4 5 6
7 8 9
```

using a two-dimensional array.

Then print:

```text
1
5
9
```

by accessing the appropriate indexes.

---

## 8.37 Challenge Exercise

Write a program that stores the marks of five students:

```text
85
72
91
67
88
```

The program should:

1. Store the marks in an array.
2. Print the array.
3. Print the number of students.
4. Print every student's marks.
5. Calculate the total marks.
6. Calculate the average.
7. Find the highest mark.
8. Find the lowest mark.

For example:

```text
Marks: [85 72 91 67 88]
Students: 5
Total: 403
Average: 80.6
Highest: 91
Lowest: 67
```

Try to solve this **without looking up the solution**.

---

## 8.38 What You Should Understand Before Moving On

Before proceeding to the next chapter, make sure you can explain these in your own words:

```text
1. What is an array?
2. Why do we use arrays?
3. What does [5]int mean?
4. Why does indexing start at 0?
5. What is the last index of a [10]int array?
6. What happens when an array is declared without values?
7. How do you access an element?
8. How do you modify an element?
9. What does len(array) return?
10. Why are [3]int and [5]int different types?
11. What happens when one array is assigned to another?
12. What does [...]int mean?
13. What is a multidimensional array?
14. Why can't an array grow?
15. What is the basic difference between an array and a slice?
```

The **most important mental model** for this chapter is:

```text
Array = fixed-size collection of same-type values

        index
          ↓
[ 10 ][ 20 ][ 30 ][ 40 ][ 50 ]
   0     1     2     3     4

length = 5
last index = length - 1 = 4
type = [5]int
```

Once this model is solid, **slices (`[]T`)** will be much easier to understand.
