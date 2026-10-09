# Go Basic — Chapter 9: Slices
Slices deserve more attention than arrays because they are used extensively in real Go applications. When you work with APIs, databases, JSON responses, files, or collections of records, you will encounter slices frequently.

In this chapter, we will learn slices from the beginning, understand how Go stores and manages their data in memory, and practise with many programming and debugging exercises.

By the end of this chapter, you should understand:

- What a slice is and how it differs from an array.
- How `len()`, `cap()`, `append()`, and `copy()` work.
- How to create slices using literals and `make()`.
- How slicing expressions such as `numbers[1:4]` work.
- How slices share an underlying array.
- Why appending to a slice sometimes changes another slice and sometimes does not.
- How to insert, remove, search, reverse, and deduplicate elements.
- How to avoid common slice-related bugs.

# 9.1 What Is a Slice?
Let's start with a simple program.

```
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30}

    fmt.Println(numbers)
}
```

Output:

```
[10 20 30]
```

The expression:

```
numbers := []int{10, 20, 30}
```

creates a slice containing three integers.

Let's understand its syntax carefully.

### Breaking down the declaration

## `[]`
Slice type syntax

## `int`
Element type

## {10, 20, 30}
Initial elements

The variable `numbers` refers to a slice of integers. The slice provides access to the elements stored in an underlying array.

Notice the difference between these two declarations:

```
var a [3]int = [3]int{10, 20, 30}

b := []int{10, 20, 30}
```

- `a` is an array of exactly three integers.
- `b` is a slice of integers.
The array's length is part of its type. `[3]int` and `[4]int` are different types.

A slice's length is not part of its type. Both a slice containing three integers and a slice containing ten integers have the type `[]int`.

## 9.1.1 Why Do We Need Slices?
Suppose you are building an employee management application.

Initially, you have three employees:

```
employees := []string{"Amit", "Neha", "Rahul"}
```

Later, you want to add another employee:

```
employees = append(employees, "Priya")
```

Now the slice contains four employees.

You do not need to decide the final number of employees when declaring the slice. This makes slices convenient for collections whose sizes change during program execution.

Common examples include:

- Employees returned by a database query.
- Products in a shopping cart.
- HTTP request records.
- Lines read from a file.
- Messages received from Kafka.
- JSON arrays returned by an API.
One important distinction: a slice can grow, but an array itself has a fixed length. Go handles slice growth through operations such as `append()`.

# 9.2 Understanding the Internal Structure of a Slice
This is one of the most important concepts in Go.

A slice is not simply an array with a flexible length. Conceptually, a slice is a small descriptor that provides access to a portion of an underlying array.

A slice descriptor contains three pieces of information:

1. A reference to the underlying array data.
2. The slice's length.
3. The slice's capacity.

### Conceptual slice representation
Slice variable: `numbers`

Conceptual descriptor

#### Underlying array

## 10

## 20

## 30

Length

# 3
Accessible elements

Capacity

# 3
Elements available from the start

This is a conceptual diagram, not a guarantee of the exact in-memory representation or address.

For a slice:

```
numbers := []int{10, 20, 30}
```

we have:

```
Length   = 3
Capacity = 3
```

The underlying array holds the actual integer values. The slice descriptor tells Go which portion of that array is accessible and how much capacity is available from its starting position.

We will explore how this works through actual programs shortly.

## 9.2.1 Does a Slice Contain the Array?
Consider:

```
numbers := []int{10, 20, 30}
```

It is useful to imagine two related objects:

```
Slice descriptor
    |
    | refers to
    v
Underlying array
+----+----+----+
| 10 | 20 | 30 |
+----+----+----+
```

The slice variable does not contain three independent integer values as an ordinary array variable would. It holds the slice descriptor, which refers to the array data.

This distinction matters because copying a slice descriptor does not automatically copy the underlying array.

For example:

```
a := []int{10, 20, 30}
b := a

b[0] = 999

fmt.Println(a)
fmt.Println(b)
```

Output:

```
[999 20 30]
[999 20 30]
```

Why did changing `b` also affect `a`?

Because `a` and `b` refer to the same underlying array.

The statement:

```
b := a
```

copies the slice descriptor. It does not create an independent copy of every element.

We will examine this behaviour in depth in Section 9.9.

# 9.3 The `len()` Function
The built-in function `len()` returns the number of elements currently accessible through a slice.

Example:

```
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    fmt.Println(len(numbers))
}
```

Output:

```
5
```

The slice contains five elements, so its length is five.

You can also use `len()` to control a loop:

```
numbers := []int{10, 20, 30, 40, 50}

for i := 0; i < len(numbers); i++ {
    fmt.Println(numbers[i])
}
```

Output:

```
10
20
30
40
50
```

Let's understand the expression:

```
i < len(numbers)
```

The loop starts at index `0` and continues while `i` is less than `5`.

The valid indices are:

```
0  1  2  3  4
```

The index `5` is invalid because the last element is at index `4`.

If you attempt:

```
fmt.Println(numbers[5])
```

the program panics at runtime with an index-out-of-range error.

## 9.3.1 Length Changes When You Append
Consider:

```
numbers := []int{10, 20, 30}

fmt.Println(len(numbers))

numbers = append(numbers, 40)

fmt.Println(len(numbers))
```

Output:

```
3
4
```

Initially, the slice has three elements. After appending `40`, it has four.

Remember:

- `len()` tells you how many elements are in the slice.
- It does not tell you how many elements the underlying array can accommodate before a new array might be allocated.
That second concept is capacity.

# 9.4 The `cap()` Function
The built-in `cap()` function returns the capacity of a slice.

Capacity is the number of elements in the underlying array available to the slice, starting from the slice's first element.

Consider:

```
numbers := make([]int, 3, 5)

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```
[0 0 0]
3
5
```

Let's examine this declaration:

```
make([]int, 3, 5)
```

It creates a slice with:

- Element type: `int`
- Length: `3`
- Capacity: `5`
The three elements within the slice's current length are initialized to zero.

Conceptually, the underlying array has space for five integers:

```
Underlying array
+----+----+----+----+----+
|  0 |  0 |  0 |  0 |  0 |
+----+----+----+----+----+
  <------ len = 3 ------>
  <------ cap = 5 ------------>
```

The slice currently exposes only the first three elements. The remaining two positions can be used as the slice grows, provided no operation changes the slice's backing storage.

## 9.4.1 Length and Capacity Are Different
Try this program:

```
package main

import "fmt"

func main() {
    numbers := make([]int, 3, 5)

    fmt.Println("Length:", len(numbers))
    fmt.Println("Capacity:", cap(numbers))

    numbers = append(numbers, 40)

    fmt.Println(numbers)
    fmt.Println("Length:", len(numbers))
    fmt.Println("Capacity:", cap(numbers))
}
```

Output:

```
Length: 3
Capacity: 5
[0 0 0 40]
Length: 4
Capacity: 5
```

Appending an element increased the length from three to four. The capacity remained five.

Why?

Because the underlying array already had enough available capacity.

Now append one more element:

```
numbers = append(numbers, 50)
```

The resulting slice is:

```
[0 0 0 40 50]
```

Its length is five and its capacity is five.

What happens if you append another element?

```
numbers = append(numbers, 60)
```

The resulting slice contains six elements.

Go must provide enough backing storage for the additional element. If the existing backing array cannot accommodate it, Go allocates a new, larger backing array and copies the existing elements into it.

The new capacity is determined by Go's allocation strategy. Do not assume that it always doubles.

## 9.4.2 A Slice Cannot Be Indexed Beyond Its Length
This is a common beginner mistake.

```
numbers := make([]int, 3, 5)

numbers[3] = 40
```

Will this work?

No. It panics because the slice's length is only three. The valid indices are `0`, `1`, and `2`.

Even though the capacity is five, the additional positions are not yet accessible through indexing.

Use `append()`:

```
numbers = append(numbers, 40)
```

Or increase the slice's length by appending elements.

Important: capacity describes potential growth; it does not make all capacity positions immediately indexable.

## 9.4.3 Experiment With Length and Capacity
Run this program:

```
package main

import "fmt"

func main() {
    numbers := make([]int, 2, 6)

    for i := 0; i < 7; i++ {
        numbers = append(numbers, i*10)

        fmt.Println(
            "Values:", numbers,
            "Length:", len(numbers),
            "Capacity:", cap(numbers),
        )
    }
}
```

Before running it, predict:

1. What is the initial length?
2. What is the initial capacity?
3. At which append operation will the capacity first need to increase?
4. Will the capacity necessarily double every time?
Then run it and compare your predictions.

# 9.5 The `append()` Function
`append()` adds elements to the end of a slice.

Syntax:

```
slice = append(slice, element)
```

Example:

```
numbers := []int{10, 20, 30}

numbers = append(numbers, 40)

fmt.Println(numbers)
```

Output:

```
[10 20 30 40]
```

Notice the assignment:

```
numbers = append(numbers, 40)
```

Why do we assign the result back to `numbers`?

Because `append()` returns a slice. Depending on the available capacity, it may return a slice that refers to the same underlying array or one that refers to a newly allocated array.

You should generally use the returned slice.

## 9.5.1 Append Multiple Elements
You can append several elements in one operation:

```
numbers := []int{10, 20}

numbers = append(numbers, 30, 40, 50)

fmt.Println(numbers)
```

Output:

```
[10 20 30 40 50]
```

## 9.5.2 Append One Slice to Another
Suppose you have two slices:

```
a := []int{10, 20}
b := []int{30, 40, 50}
```

You want to append every element of `b` to `a`.

Use the `...` operator:

```
a = append(a, b...)
```

Output:

```
[10 20 30 40 50]
```

The expression:

```
b...
```

passes the elements of `b` as individual arguments to `append()`.

Without `...`, this would not compile:

```
a = append(a, b)
```

The element type of `a` is `int`, but `b` is a `[]int`, so the types do not match.

## 9.5.3 Append and Capacity
Consider these two examples.

Example A:

```
numbers := make([]int, 2, 5)

numbers[0] = 10
numbers[1] = 20

numbers = append(numbers, 30)

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Example B:

```
numbers := []int{10, 20}

numbers = append(numbers, 30)

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Both slices contain the same values and have the same length.

However, their capacities may differ because they were created differently.

This is why you should not assume that capacity is always equal to length or that two slices with identical contents must have identical capacities.

## 9.5.4 What Happens Internally When `append()` Allocates a New Array?
To understand this properly, we need to look beyond Go syntax and understand variables, slice descriptors, backing arrays, memory sharing, and what happens when `append()` runs out of capacity.

Let's build the explanation step by step, using your example.

### 1. First, understand the difference between a slice and its backing array
Consider this statement:

```go
a := make([]int, 2, 2)
```

It creates a slice with:

- Length = `2`
- Capacity = `2`
- An underlying array that can hold two integers

The two integers are initially zero because Go initializes the elements of a newly allocated array to their zero values.

Conceptually, imagine the memory looks like this:

- Slice variable `a`
- A small descriptor that refers to the backing array
- Pointer → array address
- Length → `2`
- Capacity → `2`

The pointer refers to the underlying array.

Underlying array:

```text
Index 0  ->  0
Index 1  ->  0
```

This is a conceptual representation, not a literal memory-layout diagram.

A slice is not the entire backing array. It is a descriptor containing three essential pieces of information:

1. A pointer to the backing array's relevant starting position.
2. The slice's length.
3. The slice's capacity.

For the moment, you can think of the descriptor as a small structure maintained by the Go runtime.

Important: the pointer, length, and capacity describe a slice. The actual elements live in the backing array.

### 2. Step 1 — Create the first slice
Your code:

```go
a := make([]int, 2, 2)

a[0] = 10
a[1] = 20
```

The first line creates a slice of length 2 and capacity 2. The next two lines modify the array elements.

Conceptually, the state is now:

```text
Slice a
len(a) = 2
cap(a) = 2
Points to Array A
```

Array A:

```text
Index 0 -> 10
Index 1 -> 20
```

At this point:

```go
a
 |
 v
Array A: [10, 20]
```

There is one slice variable, `a`, referring to one backing array.

### 3. Step 2 — Execute `b := a`
Now execute:

```go
b := a
```

What does Go copy here?

Go copies the slice descriptor, not the entire backing array.

The descriptor for `b` receives the same backing-array pointer, length, and capacity as `a`.

Conceptually:

```text
Slice a
len = 2, cap = 2
Pointer -> Array A

Slice b
len = 2, cap = 2
Pointer -> Array A
```

Both pointers refer to the same array.

Shared Array A:

```text
Index 0 -> 10
Index 1 -> 20
```

The important point is that there are now two slice descriptors but only one backing array.

That means changes to an existing array element through either slice are visible through the other slice, provided both slices cover that index.

For example:

```go
b[0] = 999
fmt.Println(a)
fmt.Println(b)
```

At this stage, the output would be:

```text
[999 20]
[999 20]
```

Why? Because `b[0]` and `a[0]` refer to the same physical array element.

But your actual example calls `append()` before modifying `b[0]`. That changes the situation.

### 4. Step 3 — Execute `b = append(b, 30)`
This is the most important line in the example:

```go
b = append(b, 30)
```

To understand it, we need to know what `append()` checks internally.

When Go executes `append(b, 30)`, it needs to add one element to the slice.

The current state of `b` is:

```text
len(b) = 2
cap(b) = 2
```

The slice already has two elements, and its backing array has room for only two elements.

Let's calculate what happens:

| Property | Before append |
| --- | --- |
| Required length | 3 |
| Existing capacity | 2 |
| Is capacity sufficient? | No |

The new element must occupy index `2`.

But the existing backing array has only these indexes:

```text
Array A
Index 0 -> 10
Index 1 -> 20
```

There is no index `2` in Array A.

Go must obtain a backing array with enough capacity for the new element.

Conceptually, the operation proceeds as follows.

#### Before `append()`
Both slices point to Array A.

```text
a
len 2 · cap 2

b
len 2 · cap 2

Array A
[10, 20]
```

#### During `append(b, 30)`
Go obtains a larger backing array and preserves the existing elements.

```text
New Array B
[10, 20, 30]
```

Additional capacity may exist beyond these three elements.

#### After `b = append(b, 30)`
```text
a
Still points to Array A
[10, 20]

b
Now points to Array B
[10, 20, 30]
```

The diagram illustrates the logical result. The actual allocation size is chosen by Go's runtime and is not guaranteed to be exactly three elements.

### What happened to the original array?
Array A still contains:

```text
[10, 20]
```

Go does not need to enlarge Array A in place. Instead, it obtains storage for Array B, copies the existing elements, and puts `30` into the new element.

Now the two slices refer to different backing arrays.

One important distinction: `append()` does not modify the slice variable `b` by itself. It returns a slice describing the result. The assignment stores that returned slice descriptor in `b`.

Conceptually, this line:

```go
b = append(b, 30)
```

means:

1. Evaluate `append(b, 30)`.
2. Obtain the resulting slice descriptor.
3. Assign that descriptor to `b`.

The new descriptor has a length of `3` and a capacity sufficient for at least `3` elements.

### 5. Step 4 — Execute `b[0] = 999`
Now we reach your final modification:

```go
b[0] = 999
```

Where does this write happen?

It happens in Array B, because `b` now points to Array B.

Array B changes from:

```text
[10, 20, 30]
```

to:

```text
[999, 20, 30]
```

Array A remains unchanged:

```text
[10, 20]
```

Consequently:

```go
fmt.Println(a)
fmt.Println(b)
```

prints:

```text
[10 20]
[999 20 30]
```

The key is that `b[0] = 999` changes an array element; it does not change the backing-array pointer stored in `a`.

### 6. What happens at the runtime level?
Let's look a little deeper.

#### 6.1 A slice is a descriptor
Conceptually, you can imagine Go representing a slice like this:

```go
type SliceDescriptor struct {
    Pointer  *int
    Length   int
    Capacity int
}
```

This is an educational model, not a declaration you should use to reproduce Go's actual internal slice representation. Real slice internals are runtime implementation details.

After:

```go
a := make([]int, 2, 2)
b := a
```

The descriptors are conceptually:

```text
a = {pointer: Array A, length: 2, capacity: 2}
b = {pointer: Array A, length: 2, capacity: 2}
```

Notice that the descriptors are separate, but their pointers are the same.

#### 6.2 `append()` checks whether capacity is sufficient
When you append one element, Go needs the new length to be:

$$
\text{new length} = \text{old length} + 1
$$

For your example:

$$
3 = 2 + 1
$$

The old capacity is `2`, so the existing array cannot hold the required three elements.

Conceptually, the operation follows this decision:

```text
append(b, 30)
       |
       v
Will the backing array accommodate
the required new length?
       |
       v
     No
       |
       v
Obtain larger backing storage
       |
       v
Preserve existing elements
       |
       v
Write 30 at index 2
       |
       v
Return a slice descriptor
for the new backing array
       |
       v
Assign it to b
```

This is a simplified description of the logical behavior. The runtime's exact implementation contains additional checks for allocation sizes, integer overflow, and other details.

#### 6.3 The existing elements are copied
Conceptually, Go preserves the values of the old slice elements:

```text
Array A                Array B

[10, 20]  -----------> [10, 20, 30]
                          ^
                          |
                    Newly appended value
```

After the append, the first two values exist in both arrays. They are separate copies of the integer values, not shared integer elements.

This distinction becomes especially important when you work with slices of structs, pointers, or other slices.

#### 6.4 The returned descriptor is assigned to `b`
After the operation, the descriptors are conceptually:

```text
a = {pointer: Array A, length: 2, capacity: 2}
b = {pointer: Array B, length: 3, capacity: new capacity}
```

The capacity of `b` is at least `3`, but its exact value is not guaranteed.

The original variable `a` was never reassigned, so its pointer remains unchanged.

### 7. Experiment: prove that `append()` changes the backing array
Run this complete program on your Mac.

```go
package main

import "fmt"

func main() {
    a := make([]int, 2, 2)

    a[0] = 10
    a[1] = 20

    b := a

    fmt.Println("Before append:")
    fmt.Println("a:", a, "len:", len(a), "cap:", cap(a))
    fmt.Println("b:", b, "len:", len(b), "cap:", cap(b))

    b = append(b, 30)

    fmt.Println("\nAfter append:")
    fmt.Println("a:", a, "len:", len(a), "cap:", cap(a))
    fmt.Println("b:", b, "len:", len(b), "cap:", cap(b))

    b[0] = 999

    fmt.Println("\nAfter changing b[0]:")
    fmt.Println("a:", a)
    fmt.Println("b:", b)
}
```

Your output will be similar to:

```text
Before append:
a: [10 20] len: 2 cap: 2
b: [10 20] len: 2 cap: 2

After append:
a: [10 20] len: 2 cap: 2
b: [10 20 30] len: 3 cap: 4

After changing b[0]:
a: [10 20]
b: [999 20 30]
```

Note: The capacity shown as `4` is a possible result, not a guarantee. Go's growth strategy is an implementation detail and can vary with the Go version, the existing capacity, and the amount being appended. The language guarantees that the returned slice can accommodate the appended elements.

### 8. What if there is already spare capacity?
This is the other half of the concept. Consider a slightly different program:

```go
package main

import "fmt"

func main() {
    a := make([]int, 2, 4)

    a[0] = 10
    a[1] = 20

    b := a

    b = append(b, 30)

    b[0] = 999

    fmt.Println("a:", a)
    fmt.Println("b:", b)
}
```

Output:

```text
a: [999 20]
b: [999 20 30]
```

Why is the result different?

Initially:

```text
len(a) = 2
cap(a) = 4
```

The backing array can accommodate four elements, even though `a` exposes only two elements through its current length.

When you append one element to `b`, the required length becomes `3`. The capacity is `4`, so Go can reuse the existing backing array.

Conceptually, the array looks like this:

```text
Before append
Index 0 -> 10
Index 1 -> 20
Index 2 -> 0
Index 3 -> 0

Length: 2
Capacity: 4
```

```text
After append
Index 0 -> 10
Index 1 -> 20
Index 2 -> 30
Index 3 -> 0
```

The backing array is reused. Both slice descriptors still point to the same array.

There is a subtle but important detail here:

- `a` still has length `2`, so printing `a` displays only indexes `0` and `1`.
- `b` has length `3`, so printing `b` displays indexes `0`, `1`, and `2`.
- Both slices share the same backing array, so changing index `0` through `b` also changes what `a` sees.

The spare capacity was there from the beginning. `append()` simply used it.

### 9. One final distinction: does `append()` always allocate?
No. There are two common cases:

| Situation | What happens |
| --- | --- |
| Enough capacity is available | Go can reuse the existing backing array. |
| Capacity is insufficient | Go obtains a larger backing array and preserves the existing elements. |

In either case, `append()` returns the resulting slice, and you should generally assign that result:

```go
b = append(b, 30)
```

You must not assume that every append creates a new array, or that every append reuses the old one.

### Practice exercises
Try these before moving to the next section.

1. Change `make([]int, 2, 2)` to `make([]int, 2, 3)`. Predict the output before running the program.
2. Change the capacity to `10`. Does `append()` need a new backing array to add one element?
3. Remove `b[0] = 999`. Does `a` change when you append `30` to `b`?
4. Append two elements instead of one: `b = append(b, 30, 40)`. What capacity must the returned slice have at minimum?
5. Explain why `a` and `b` can have different lengths while sharing the same backing array.

The central concept to remember is this: copying a slice copies its descriptor, not its backing array. Appending may reuse the shared array or switch the resulting slice to a new one, depending on available capacity.

# 9.6 Slices and Underlying Arrays
This is one of the most important sections of the chapter.

To understand slices properly, you need to understand the relationship between a slice and the underlying array that stores its elements.

## 9.6.1 What Is an Underlying Array?
Consider this program:

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    fmt.Println(numbers)
}
```

Output:

```text
[10 20 30 40 50]
```

When we write:

```go
numbers := []int{10, 20, 30, 40, 50}
```

Go creates a slice and provides storage for its elements in an underlying array.

Conceptually, the storage looks like this:

```text
Underlying array

Index:       0    1    2    3    4
            ┌────┬────┬────┬────┬────┐
Values:     │ 10 │ 20 │ 30 │ 40 │ 50 │
            └────┴────┴────┴────┴────┘
```

The slice `numbers` provides access to these elements.

Conceptually, a slice descriptor contains three things:

1. A reference to the underlying array and its starting position.
2. Its length.
3. Its capacity.

This is a conceptual model. You normally do not need to manipulate the descriptor yourself.

For example:

```go
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
5
5
```

The slice has five accessible elements and capacity for five elements.

The important idea is:

> A slice is not the underlying array itself. A slice provides a way to access elements stored in an underlying array.

## 9.6.2 Slicing Does Not Automatically Copy Elements
Suppose we want to work with only the elements `20`, `30`, and `40`.

We can write:

```go
part := numbers[1:4]
```

The expression `numbers[1:4]` means:

- Start at index `1`, including that element.
- Stop before index `4`.
- Include indexes `1`, `2`, and `3`.

Therefore:

```text
numbers = [10 20 30 40 50]
part    = [20 30 40]
```

It might be tempting to imagine that Go creates a new array containing:

```text
[20 30 40]
```

But ordinary slicing does not copy the elements into a separate array.

Instead, `part` refers to a portion of the same underlying array.

Conceptually:

```text
Underlying array

Index:       0    1    2    3    4
            ┌────┬────┬────┬────┬────┐
Values:     │ 10 │ 20 │ 30 │ 40 │ 50 │
            └────┴────┴────┴────┴────┘
                  └─────────┘
                      part
```

Let's verify this.

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    part := numbers[1:4]

    fmt.Println("numbers:", numbers)
    fmt.Println("part:", part)

    part[0] = 999

    fmt.Println("After modifying part:")

    fmt.Println("numbers:", numbers)
    fmt.Println("part:", part)
}
```

Output:

```text
numbers: [10 20 30 40 50]
part: [20 30 40]
After modifying part:
numbers: [10 999 30 40 50]
part: [999 30 40]
```

Why did `numbers` change?

Because:

```go
part[0] = 999
```

modifies the first element visible through `part`.

That element is the same underlying array element as:

```go
numbers[1]
```

Therefore, both slices show the modification.

The relationship is:

```text
Access through numbers   Access through part
numbers[1]              part[0]
numbers[2]              part[1]
numbers[3]              part[2]
```

Each pair accesses the same underlying array element.

> Ordinary slicing creates another view of existing elements; it does not automatically create an independent copy.

## 9.6.3 Multiple Slices Can Share the Same Underlying Array
A single underlying array can be referenced by several slices.

Consider:

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    first := numbers[1:4]
    second := numbers[2:5]

    fmt.Println("numbers:", numbers)
    fmt.Println("first:", first)
    fmt.Println("second:", second)
}
```

Output:

```text
numbers: [10 20 30 40 50]
first: [20 30 40]
second: [30 40 50]
```

The slices overlap at the elements `30` and `40`.

```text
Underlying array:

Index:       0    1    2    3    4
            ┌────┬────┬────┬────┬────┐
Values:     │ 10 │ 20 │ 30 │ 40 │ 50 │
            └────┴────┴────┴────┴────┘
                  └─────────┘
                     first

                       └─────────┘
                        second
```

Now execute:

```go
second[0] = 999

fmt.Println("numbers:", numbers)
fmt.Println("first:", first)
fmt.Println("second:", second)
```

Output:

```text
numbers: [10 20 999 40 50]
first: [20 999 40]
second: [999 40 50]
```

Why did all three slices reflect the change?

Because:

```go
numbers[2]
first[1]
second[0]
```

all refer to the same underlying array element.

This can be useful when you want to work with different portions of a collection without copying the data.

However, it can cause bugs if you assume that modifying one slice cannot affect another.

---

# 9.7 Understanding Slice Length and Capacity
Length and capacity are different concepts. You must understand both before studying `append()`.

## 9.7.1 What Is Length?
Length is the number of elements currently accessible through a slice.

Consider:

```go
numbers := []int{10, 20, 30, 40, 50}
part := numbers[1:3]
```

The slice `part` contains:

```text
[20 30]
```

Therefore:

```go
fmt.Println(len(part))
```

Output:

```text
2
```

The length is two because the slice exposes two elements.

## 9.7.2 What Is Capacity?
Capacity is the number of elements from the slice's starting position to the end of its underlying array.

Consider:

```go
numbers := []int{10, 20, 30, 40, 50}
part := numbers[1:3]
```

The underlying array contains five elements, and `part` starts at index `1`.

Conceptually:

```text
Underlying array:

Index:       0    1    2    3    4
            ┌────┬────┬────┬────┬────┐
Values:     │ 10 │ 20 │ 30 │ 40 │ 50 │
            └────┴────┴────┴────┴────┘
                  ▲
                  part starts here

Positions from the starting position to the end:

                  [20] [30] [40] [50]
```

There are four positions from index `1` through the end of the underlying array.

Therefore:

```go
fmt.Println(len(part))
fmt.Println(cap(part))
```

Output:

```text
2
4
```

The length is two, but the capacity is four.

For an ordinary two-index slice expression, the capacity is calculated from the starting position to the end of the underlying array.

```text
length   = high - low
capacity = original capacity - low
```

## 9.7.3 Another Example

```go
numbers := []int{10, 20, 30, 40, 50, 60, 70}

part := numbers[2:5]

fmt.Println(part)
fmt.Println(len(part))
fmt.Println(cap(part))
```

Output:

```text
[30 40 50]
3
5
```

The length is:

```text
5 - 2 = 3
```

The capacity is:

```text
7 - 2 = 5
```

Capacity is measured from the slice's starting position, not from the beginning of the original array.

## 9.7.4 Why Can't We Access Every Element Within the Capacity?
Consider:

```go
numbers := make([]int, 2, 5)

fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
2
5
```

The slice has two accessible elements and capacity for five.

This statement is invalid:

```go
numbers[4] = 100
```

It causes a runtime panic because index `4` is outside the slice's current length.

The valid indexes are:

```text
0 and 1
```

To add an element, use `append()`:

```go
numbers = append(numbers, 100)
```

Now the slice contains three elements:

```go
fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
[0 0 100]
3
5
```

> Length controls which indexes you may access. Capacity helps determine whether appending requires additional storage.

---

# 9.8 The Three-Index Slice Expression
So far, we have used expressions such as:

```go
numbers[1:4]
```

Go also supports a three-index slice expression:

```go
numbers[low:high:max]
```

The third index lets you limit the resulting slice's capacity.

The resulting length is:

```text
high - low
```

The resulting capacity is:

```text
max - low
```

For a slice expression, the indexes must satisfy the appropriate bounds. In particular, the maximum index cannot exceed the original slice's capacity.

## 9.8.1 Example

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    part := numbers[1:3:3]

    fmt.Println("part:", part)
    fmt.Println("len:", len(part))
    fmt.Println("cap:", cap(part))
}
```

Output:

```text
part: [20 30]
len: 2
cap: 2
```

Why?

The length is:

```text
3 - 1 = 2
```

The capacity is:

```text
3 - 1 = 2
```

Compare this with:

```go
part := numbers[1:3]
```

That version has the same length but a capacity of four.

The three-index version limits the capacity to two.

## 9.8.2 How Does Limiting Capacity Affect `append()`?
Let's compare two slices.

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    normal := numbers[1:3]
    limited := numbers[1:3:3]

    normal = append(normal, 999)
    limited = append(limited, 888)

    fmt.Println("numbers:", numbers)
    fmt.Println("normal:", normal)
    fmt.Println("limited:", limited)
}
```

Output:

```text
numbers: [10 20 30 999 50]
normal: [20 30 999]
limited: [20 30 888]
```

Let's examine the normal slice:

```go
normal := numbers[1:3]
```

Its length is two and its capacity is four.

There is enough capacity to append one element without allocating a new underlying array. The appended value overwrites the existing value at `numbers[3]`.

Now examine:

```go
limited := numbers[1:3:3]
```

Its length and capacity are both two.

There is no spare capacity. Appending requires a new underlying array, so the appended value `888` does not overwrite `numbers[3]`.

The exact capacity chosen for the newly allocated array is an implementation detail. Do not depend on a particular growth factor.

> Three-index slicing does not copy the existing elements. The initial elements of `limited` still share the original underlying array.

---

# 9.9 Creating Slices with `make()`
Go provides the built-in `make()` function to create slices.

The general syntax is:

```go
make([]T, length, capacity)
```

Where:

- `T` is the element type.
- `length` is the number of initially accessible elements.
- `capacity` is the capacity of the slice.

The capacity argument is optional.

## 9.9.1 Creating a Slice with a Length

```go
numbers := make([]int, 3)
```

This creates a slice with three elements.

Since the element type is `int`, the elements initially contain the zero value, which is `0`.

```go
fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
[0 0 0]
3
3
```

When capacity is omitted, it equals the length.

You can modify the elements immediately:

```go
numbers[0] = 10
numbers[1] = 20
numbers[2] = 30

fmt.Println(numbers)
```

Output:

```text
[10 20 30]
```

## 9.9.2 Creating a Slice with a Larger Capacity
Consider:

```go
numbers := make([]int, 3, 8)
```

This creates a slice with:

```text
Length   = 3
Capacity = 8
```

The slice initially exposes three elements:

```text
[0 0 0]
```

It has capacity for eight elements.

```go
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
3
8
```

Can we access index `5` immediately?

No.

```go
numbers[5] = 100
```

This causes a runtime panic because the slice's length is only three.

To add another element, use `append()`:

```go
numbers = append(numbers, 100)

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
[0 0 0 100]
4
8
```

The length becomes four, while capacity remains eight.

## 9.9.3 Why Is Preallocating Capacity Useful?
Suppose you expect to collect approximately 1,000 integers.

You could write:

```go
numbers := make([]int, 0, 1000)
```

This creates a slice with:

```text
Length   = 0
Capacity = 1000
```

You can append elements as they arrive:

```go
numbers = append(numbers, 10)
numbers = append(numbers, 20)
numbers = append(numbers, 30)
```

The slice now contains:

```text
[10 20 30]
```

The length is three, and the capacity remains 1,000.

Preallocating capacity can reduce the need for repeated allocations as the slice grows. It is often useful when you know approximately how many elements you will collect.

However, it is an optimization, not a requirement for correctness.

---

# 9.10 Nil Slices and Empty Slices
Consider these two declarations:

```go
var a []int

b := []int{}
```

Both have length zero, but they are not identical.

## 9.10.1 Nil Slice

```go
var numbers []int

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
fmt.Println(numbers == nil)
```

Output:

```text
[]
0
0
true
```

The zero value of a slice type is `nil`.

A nil slice has no underlying array associated with it.

You can safely append to it:

```go
numbers = append(numbers, 10)

fmt.Println(numbers)
```

Output:

```text
[10]
```

Go obtains the necessary storage as the slice grows.

## 9.10.2 Empty but Non-Nil Slice

```go
numbers := []int{}

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
fmt.Println(numbers == nil)
```

Output:

```text
[]
0
0
false
```

This slice is non-nil but contains zero elements.

You can also create an empty slice using:

```go
numbers := make([]int, 0)
```

## 9.10.3 Why Does the Difference Matter?
For many ordinary operations, nil and empty slices behave similarly.

Both can be appended to:

```go
var a []int
b := []int{}

a = append(a, 10)
b = append(b, 10)

fmt.Println(a)
fmt.Println(b)
```

Output:

```text
[10]
[10]
```

But they differ when you check for `nil` and in some API behaviors, including JSON encoding.

Consider:

```go
package main

import (
    "encoding/json"
    "fmt"
)

func main() {
    var a []int
    b := []int{}

    jsonA, _ := json.Marshal(a)
    jsonB, _ := json.Marshal(b)

    fmt.Println(string(jsonA))
    fmt.Println(string(jsonB))
}
```

Output:

```text
null
[]
```

A nil slice encodes as JSON `null`, while an empty, non-nil slice encodes as an empty JSON array `[]`.

For example:

```text
{"items": null}
```

is different from:

```text
{"items": []}
```

This distinction matters when building REST APIs because clients may expect an empty collection rather than a null value.

Use the representation that matches your API's intended behavior.

---

# 9.11 Copying Slice Elements with `copy()`
Go provides the built-in `copy()` function.

Its syntax is:

```go
copy(destination, source)
```

It copies elements from the source slice into the destination slice.

The return value is the number of elements copied.

## 9.11.1 Basic Example

```go
package main

import "fmt"

func main() {
    source := []int{10, 20, 30}

    destination := make([]int, len(source))

    count := copy(destination, source)

    fmt.Println("source:", source)
    fmt.Println("destination:", destination)
    fmt.Println("copied:", count)
}
```

Output:

```text
source: [10 20 30]
destination: [10 20 30]
copied: 3
```

First:

```go
destination := make([]int, len(source))
```

creates a destination slice with its own underlying array.

Then:

```go
count := copy(destination, source)
```

copies the elements into that array.

Now modify the destination:

```go
destination[0] = 999

fmt.Println("source:", source)
fmt.Println("destination:", destination)
```

Output:

```text
source: [10 20 30]
destination: [999 20 30]
```

The source remains unchanged because the destination uses separate storage.

## 9.11.2 What If the Destination Is Smaller?

```go
source := []int{10, 20, 30, 40, 50}
destination := make([]int, 3)

count := copy(destination, source)

fmt.Println(destination)
fmt.Println(count)
```

Output:

```text
[10 20 30]
3
```

Only three elements are copied because the destination has length three.

The number of copied elements is the smaller of the two slice lengths.

## 9.11.3 What If the Destination Is Larger?

```go
source := []int{10, 20, 30}
destination := make([]int, 5)

count := copy(destination, source)

fmt.Println(destination)
fmt.Println(count)
```

Output:

```text
[10 20 30 0 0]
3
```

The first three elements are copied. The remaining destination elements retain their zero values.

The `copy()` function does not automatically increase the destination's length.

## 9.11.4 Does `copy()` Always Make Everything Independent?
No. It copies element values, but those values may themselves contain references to other data.

Consider:

```go
package main

import "fmt"

func main() {
    a := 10
    b := 20

    source := []*int{&a, &b}
    destination := make([]*int, len(source))

    copy(destination, source)

    *destination[0] = 999

    fmt.Println(a)
    fmt.Println(*source[0])
}
```

Output:

```text
999
999
```

Why?

The source and destination have separate arrays containing pointer values, but the copied pointers still point to the same integers.

This is a shallow copy.

For a slice of integers, copying into a separate array gives independent integer elements. For slices containing pointers, maps, slices, or pointers to structs, the referenced data may still be shared.

---

# 9.12 Appending Elements with `append()`
The built-in `append()` function adds elements to a slice.

The general syntax is:

```go
slice = append(slice, element)
```

For example:

```go
numbers := []int{10, 20, 30}

numbers = append(numbers, 40)

fmt.Println(numbers)
```

Output:

```text
[10 20 30 40]
```

Notice that we assign the result back to `numbers`.

This is important because `append()` returns a slice value. It may return a slice with a different length, capacity, or underlying array.

## 9.12.1 Appending Multiple Elements

```go
numbers := []int{10, 20}

numbers = append(numbers, 30, 40, 50)

fmt.Println(numbers)
```

Output:

```text
[10 20 30 40 50]
```

You can append several individual elements in one call.

## 9.12.2 Appending One Slice to Another
Suppose we have:

```go
first := []int{10, 20, 30}
second := []int{40, 50}
```

To append all the elements of `second` to `first`, use the `...` operator:

```go
first = append(first, second...)

fmt.Println(first)
```

Output:

```text
[10 20 30 40 50]
```

The expression:

```go
second...
```

passes the elements of `second` as individual arguments to `append()`.

Without the ellipsis, this does not work for two slices of integers:

```go
// Does not compile:
first = append(first, second)
```

The `append()` function expects individual `int` values in this case, not a `[]int` value.

## 9.12.3 Appending to a Nil Slice
A nil slice can be used directly with `append()`:

```go
var numbers []int

numbers = append(numbers, 10)
numbers = append(numbers, 20)

fmt.Println(numbers)
```

Output:

```text
[10 20]
```

You do not need to initialize the slice before appending.

## 9.12.4 What Happens When Capacity Is Available?
Consider:

```go
numbers := make([]int, 2, 5)

numbers[0] = 10
numbers[1] = 20

numbers = append(numbers, 30)

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

Output:

```text
[10 20 30]
3
5
```

The original slice had capacity five and length two.

Appending one element increased the length to three. There was enough capacity, so the existing underlying array could be reused.

## 9.12.5 What Happens When Capacity Is Exhausted?
Consider:

```go
numbers := make([]int, 2, 2)

numbers[0] = 10
numbers[1] = 20

numbers = append(numbers, 30)

fmt.Println(numbers)
fmt.Println(len(numbers))
fmt.Println(cap(numbers))
```

The result contains:

```text
[10 20 30]
```

The length becomes three.

Because the original capacity was two, Go must obtain storage that can accommodate the additional element.

Go allocates a new underlying array when necessary and returns a slice that refers to the resulting storage.

The exact new capacity depends on the implementation and allocation requirements. Do not rely on a fixed capacity growth factor.

## 9.12.6 Why Must We Capture the Return Value?
Consider:

```go
numbers := []int{10, 20, 30}

append(numbers, 40)

fmt.Println(numbers)
```

The call to `append()` returns a slice value, but the program discards it.

The variable `numbers` still has its original length of three.

Depending on available capacity, the append may also modify the original underlying array. Nevertheless, the variable does not automatically acquire the returned slice's new length.

The correct form is:

```go
numbers = append(numbers, 40)
```

> Rule: normally, assign the result of `append()` back to the slice variable.

---

# 9.13 Removing an Element from a Slice
Go does not provide a built-in `remove()` function for slices.

However, you can use slicing and `append()` to remove an element while preserving the order of the remaining elements.

Suppose we have:

```go
numbers := []int{10, 20, 30, 40, 50}
```

We want to remove the element at index `2`, which is `30`.

Use:

```go
numbers = append(numbers[:2], numbers[3:]...)
```

Complete program:

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    index := 2

    numbers = append(numbers[:index], numbers[index+1:]...)

    fmt.Println(numbers)
}
```

Output:

```text
[10 20 40 50]
```

Let's understand the expression.

### Step 1: Elements before the target

```go
numbers[:index]
```

When `index` is `2`, this produces:

```text
[10 20]
```

### Step 2: Elements after the target

```go
numbers[index+1:]
```

This produces:

```text
[40 50]
```

The element `30` is excluded.

### Step 3: Combine the two portions

```go
append(numbers[:index], numbers[index+1:]...)
```

This appends the elements after the target to the portion before it.

The result is:

```text
[10 20 40 50]
```

### Important: Validate the index
A valid removal index satisfies:

```text
0 <= index < len(numbers)
```

If the index is negative or greater than or equal to the length, the expression will panic.

You should validate the index when it comes from user input or another untrusted source.

Also remember that removal may overwrite elements in the underlying array. Other slices sharing that array may observe the changes.

---

# 9.14 Inserting an Element into a Slice
Go does not provide a built-in `insert()` function for slices.

One approach is to append space for an additional element, shift the existing elements, and then assign the new value.

Suppose we want to insert `999` at index `2`.

Before:

```text
[10 20 30 40]
```

After:

```text
[10 20 999 30 40]
```

Example:

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40}

    index := 2
    value := 999

    numbers = append(numbers, 0)

    copy(numbers[index+1:], numbers[index:])

    numbers[index] = value

    fmt.Println(numbers)
}
```

Output:

```text
[10 20 999 30 40]
```

### Step 1: Make room

```go
numbers = append(numbers, 0)
```

The slice now has one extra element:

```text
[10 20 30 40 0]
```

The appended zero is temporary space for shifting the elements.

### Step 2: Shift the elements to the right

```go
copy(numbers[index+1:], numbers[index:])
```

With `index == 2`, the destination starts at index `3`, and the source starts at index `2`.

The elements are shifted to the right:

```text
[10 20 30 30 40]
```

Go's built-in `copy()` handles overlapping source and destination regions correctly.

### Step 3: Insert the value

```go
numbers[index] = value
```

This replaces the element at index `2` with `999`.

The final result is:

```text
[10 20 999 30 40]
```

For insertion, a valid index satisfies:

```text
0 <= index <= len(numbers)
```

An index equal to the original length inserts at the end.

An index greater than the length or less than zero is invalid.

---

# 9.15 Searching a Slice
One of the simplest ways to search a slice is with a `for` loop.

Suppose we want to find the index of `30`.

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    target := 30
    foundIndex := -1

    for i, value := range numbers {
        if value == target {
            foundIndex = i
            break
        }
    }

    fmt.Println(foundIndex)
}
```

Output:

```text
2
```

The loop:

```go
for i, value := range numbers
```

provides:

- `i`: the index of the current element.
- `value`: the current element's value.

When `value == target`, we store the index and stop searching.

We initialize `foundIndex` to `-1` to indicate that the element has not been found.

If the element is absent, the result remains `-1`.

For example, if:

```go
target := 99
```

the output is:

```text
-1
```

This is called linear search. In the worst case, it examines every element, so its time complexity is $O(n)$.

For ordinary slices, this is often perfectly adequate.

---

# 9.16 Finding the Minimum and Maximum
Let's write a program that finds the smallest and largest values in an integer slice.

```go
package main

import "fmt"

func main() {
    numbers := []int{45, 12, 78, 3, 56}

    minimum := numbers[0]
    maximum := numbers[0]

    for _, value := range numbers[1:] {
        if value < minimum {
            minimum = value
        }

        if value > maximum {
            maximum = value
        }
    }

    fmt.Println("Minimum:", minimum)
    fmt.Println("Maximum:", maximum)
}
```

Output:

```text
Minimum: 3
Maximum: 78
```

Why initialize both variables with the first element?

If we initialized `maximum` to zero, that would fail for an input such as:

```go
numbers := []int{-10, -5, -20}
```

Zero is greater than every value in that slice, but zero is not actually in the slice.

Initializing from the first element ensures that our initial candidates come from the data.

### Handling an Empty Slice
The preceding program assumes the slice contains at least one element.

If the slice is empty, this statement panics:

```go
minimum := numbers[0]
```

A safer function is:

```go
func minMax(numbers []int) (int, int, bool) {
    if len(numbers) == 0 {
        return 0, 0, false
    }

    minimum := numbers[0]
    maximum := numbers[0]

    for _, value := range numbers[1:] {
        if value < minimum {
            minimum = value
        }

        if value > maximum {
            maximum = value
        }
    }

    return minimum, maximum, true
}
```

The boolean indicates whether a result exists.

When the slice is empty, the function returns `false` rather than attempting to access a nonexistent element.

---

# 9.17 Reversing a Slice
We can reverse a slice by swapping elements from opposite ends and moving toward the center.

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50}

    left := 0
    right := len(numbers) - 1

    for left < right {
        numbers[left], numbers[right] = numbers[right], numbers[left]

        left++
        right--
    }

    fmt.Println(numbers)
}
```

Output:

```text
[50 40 30 20 10]
```

Initially:

```text
left  = 0
right = 4
```

The first swap exchanges the elements at indexes `0` and `4`:

```text
[50 20 30 40 10]
```

Then the indexes move inward:

```text
left  = 1
right = 3
```

The second swap produces:

```text
[50 40 30 20 10]
```

Finally, `left` and `right` meet in the middle, so the loop stops.

This reverses the existing slice in place. If another slice shares the same underlying array, its visible elements may change as well.

---

# 9.18 Removing Duplicate Values
Suppose we have:

```go
numbers := []int{10, 20, 10, 30, 20, 40}
```

We want to keep the first occurrence of each number:

```text
[10 20 30 40]
```

We can use a map to remember which values have already appeared.

```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 10, 30, 20, 40}

    seen := make(map[int]bool)
    result := make([]int, 0, len(numbers))

    for _, value := range numbers {
        if !seen[value] {
            seen[value] = true
            result = append(result, value)
        }
    }

    fmt.Println(result)
}
```

Output:

```text
[10 20 30 40]
```

When the program encounters `10` for the first time, it records the value and appends it to `result`.

When it encounters `10` again, the map indicates that it has already been seen, so the program skips it.

The result preserves the order of first occurrences.

This is a useful example of how slices and maps work together: slices maintain an ordered sequence, while maps provide a convenient way to check whether values have already appeared.

---

# 9.19 Practical Example — Managing Employee IDs
Let's combine several slice concepts into a small program.

Requirements:

1. Store employee IDs.
2. Add a new employee ID.
3. Search for an employee ID.
4. Remove an employee ID.
5. Display the final list.

```go
package main

import "fmt"

func findID(ids []int, target int) int {
    for i, id := range ids {
        if id == target {
            return i
        }
    }

    return -1
}

func removeID(ids []int, index int) []int {
    if index < 0 || index >= len(ids) {
        return ids
    }

    return append(ids[:index], ids[index+1:]...)
}

func main() {
    employeeIDs := []int{101, 102, 103, 104}

    fmt.Println("Initial IDs:", employeeIDs)

    employeeIDs = append(employeeIDs, 105)

    fmt.Println("After adding:", employeeIDs)

    index := findID(employeeIDs, 103)

    if index != -1 {
        fmt.Println("Employee 103 found at index:", index)
    } else {
        fmt.Println("Employee 103 not found")
    }

    index = findID(employeeIDs, 102)

    if index != -1 {
        employeeIDs = removeID(employeeIDs, index)
    }

    fmt.Println("After removing 102:", employeeIDs)
}
```

Output:

```text
Initial IDs: [101 102 103 104]
After adding: [101 102 103 104 105]
Employee 103 found at index: 2
After removing 102: [101 103 104 105]
```

This program uses:

- A slice to store an ordered collection.
- `append()` to add an element.
- A loop to search for an element.
- A function to remove an element.
- A returned slice to capture the result of the removal operation.

This is a small example, but the same techniques appear in real Go applications.

---

# 9.20 Common Slice Mistakes

## Mistake 1 — Accessing an Index Beyond the Length
Incorrect:

```go
numbers := make([]int, 3, 10)

numbers[5] = 100
```

This panics.

The length is three, so only indexes `0`, `1`, and `2` are accessible.

The capacity does not make indexes `3` through `9` directly accessible.

Correct:

```go
numbers = append(numbers, 100)
```

This adds an element at index `3`.

## Mistake 2 — Forgetting to Capture `append()`'s Result
Incorrect:

```go
numbers := []int{10, 20}

append(numbers, 30)
```

Correct:

```go
numbers = append(numbers, 30)
```

`append()` returns the resulting slice. Capture that return value.

## Mistake 3 — Assuming Slicing Copies Data

```go
numbers := []int{10, 20, 30}
part := numbers[:2]

part[0] = 999
```

The original slice becomes:

```text
[999 20 30]
```

The slices share the underlying array.

Use `make()` and `copy()` when you need independent storage for the elements.

## Mistake 4 — Assuming Capacity Is Always Preserved
When an append exceeds the available capacity, Go may allocate a new underlying array.

Do not assume two slices will continue sharing storage after an append.

If that relationship matters, reason about the actual capacity and whether the append can reuse the existing array.

## Mistake 5 — Discarding the Result of Removal
Incorrect:

```go
append(numbers[:index], numbers[index+1:]...)
```

Correct:

```go
numbers = append(numbers[:index], numbers[index+1:]...)
```

Assign the result back when you want the slice variable to represent the shorter sequence.

## Mistake 6 — Assuming Other Slices Are Unaffected by Removal
Suppose:

```go
numbers := []int{10, 20, 30, 40, 50}
part := numbers[1:4]

numbers = append(numbers[:1], numbers[2:]...)
```

The removal operation can overwrite elements in the shared underlying array.

Because `part` refers to that array, its contents may change as well.

If independent data is required, copy the relevant elements before performing operations that may overwrite shared storage.

## Mistake 7 — Indexing an Empty Slice
Incorrect:

```go
var numbers []int

fmt.Println(numbers[0])
```

This panics because the slice has no elements.

Correct:

```go
if len(numbers) > 0 {
    fmt.Println(numbers[0])
}
```

Always check the length before accessing an element when the slice may be empty.

---

# 9.21 Chapter 9 Exercises
Complete the exercises in order. Write the code yourself before running it. For prediction exercises, record your expected output first.

## Part A — Slice Fundamentals

### Exercise 1 — Create a Slice
Create a slice of five integers.

Print:

- The entire slice.
- Its length.
- Its capacity.

Explain why the length and capacity have those values.

### Exercise 2 — Access Elements
Given:

```go
numbers := []int{10, 20, 30, 40, 50, 60}
```

Print:

1. The first three elements.
2. The last three elements.
3. All elements except the first.
4. All elements except the last.

### Exercise 3 — Slice Bounds
Predict the output:

```go
numbers := []int{10, 20, 30, 40, 50}

fmt.Println(numbers[1:4])
fmt.Println(numbers[:3])
fmt.Println(numbers[2:])
fmt.Println(numbers[:])
```

Then run the program and compare your prediction.

### Exercise 4 — Length Versus Capacity
Given:

```go
numbers := []int{10, 20, 30, 40, 50, 60}

a := numbers[1:4]
b := numbers[2:5]
c := numbers[3:3]
```

Predict the length and capacity of all three slices.

Explain why `c` has length zero but can still have nonzero capacity.

### Exercise 5 — Use `make()`
Create a slice of integers with length four and capacity ten.

Print the slice, length, and capacity.

Append three integers and print the results again.

Explain which property changed and which did not.

## Part B — Shared Underlying Arrays

### Exercise 6 — Modify Through a Subslice

```go
numbers := []int{1, 2, 3, 4, 5}
part := numbers[1:4]
```

Change `part[1]` to `999`.

Predict the contents of `numbers` before running the program.

### Exercise 7 — Two Overlapping Slices
Create:

```go
numbers := []int{10, 20, 30, 40, 50}

a := numbers[1:4]
b := numbers[2:5]
```

Modify `b[0]`.

Print `numbers`, `a`, and `b`.

Explain which elements are shared.

### Exercise 8 — Make an Independent Copy
Rewrite Exercise 6 so that changing `part[1]` does not affect `numbers`.

Use `make()` and `copy()`.

### Exercise 9 — Three-Index Slicing
Given:

```go
numbers := []int{10, 20, 30, 40, 50, 60}

part := numbers[1:4:5]
```

Predict:

- The contents of `part`.
- `len(part)`.
- `cap(part)`.

Append one element and inspect the original slice.

Explain why capacity matters.

## Part C — Append and Copy

### Exercise 10 — Append Multiple Values
Create an empty integer slice.

Append `10`, `20`, and `30`, then append another slice containing `40` and `50`.

Print the final result.

### Exercise 11 — Append and Capacity
Create two slices:

```go
a := make([]int, 2, 5)
b := make([]int, 2, 2)
```

Append one element to each.

Print their lengths and capacities.

Explain why their resulting capacities need not be equal.

### Exercise 12 — Copy into a Smaller Slice
Copy five source integers into a destination slice of length two.

Print the destination and the number of copied elements.

### Exercise 13 — Copy into a Larger Slice
Copy two source integers into a destination slice of length five.

Explain why the remaining elements contain zero values.

### Exercise 14 — Understand Shallow Copying
Create a slice of pointers to two integers.

Copy the pointers into another slice.

Change one integer through a pointer in the destination slice.

Explain why the source slice can observe the changed integer even though you copied the slice.

## Part D — Slice Operations

### Exercise 15 — Remove by Index
Write a function:

```go
func removeAt(numbers []int, index int) []int
```

The function should remove the element at the requested index.

Handle invalid indexes safely.

### Exercise 16 — Insert by Index
Write a function that inserts an integer into a slice at a specified index.

Test insertion:

- At the beginning.
- In the middle.
- At the end.

Handle invalid indexes.

### Exercise 17 — Search
Write a function that finds the index of an integer in a slice.

Return `-1` when the value is absent.

Test the first element, last element, a missing value, and an empty slice.

### Exercise 18 — Minimum and Maximum
Write a function that returns the minimum and maximum values in a slice.

Handle an empty slice without panicking.

### Exercise 19 — Reverse a Slice
Reverse a slice in place without creating another slice of equal length.

Test an even number of elements, an odd number of elements, and an empty slice.

### Exercise 20 — Remove Duplicates
Given:

```go
numbers := []int{1, 2, 1, 3, 2, 4, 3, 5}
```

Produce:

```text
[1 2 3 4 5]
```

Preserve the order of first occurrences. Try using a map.

### Exercise 21 — Employee IDs
Build a small program that:

1. Stores employee IDs in a slice.
2. Adds an ID.
3. Searches for an ID.
4. Removes an ID.
5. Prints the final slice.

Use separate functions for searching and removing.

## Part E — Debugging Exercises
For each exercise, explain the problem before correcting it.

### Exercise 22 — Length and Capacity

```go
numbers := make([]int, 2, 5)

numbers[4] = 100
```

Why does this panic even though the capacity is five?

### Exercise 23 — Lost Append Result

```go
numbers := []int{10, 20, 30}

append(numbers, 40)

fmt.Println(numbers)
```

Why does the slice variable still have its original length?

### Exercise 24 — Unexpected Shared Changes

```go
numbers := []int{10, 20, 30, 40}
part := numbers[1:3]

part[0] = 999
```

Explain why `numbers` changes.

Rewrite the code so that it does not.

### Exercise 25 — Incorrect Removal

```go
numbers := []int{10, 20, 30, 40}

append(numbers[:1], numbers[2:]...)

fmt.Println(numbers)
```

Explain why discarding the returned slice is incorrect when your intention is to update the slice variable.

### Exercise 26 — Empty Slice Panic

```go
var numbers []int

fmt.Println(numbers[0])
```

Fix the program so that it handles the empty slice safely.

---

# 9.22 Final Mental Model of Slices
Before moving to the next chapter, make sure you can explain these ideas in your own words.

## 1. A slice provides access to elements in an underlying array
A slice is not simply another name for an array. It is a descriptor that provides access to a sequence of elements.

## 2. Length controls accessible elements
If:

```go
numbers := make([]int, 3, 8)
```

the slice initially exposes three elements. You cannot directly access index `5` just because the capacity is eight.

## 3. Capacity helps determine whether an append needs new storage
If the slice has sufficient capacity, `append()` can reuse its underlying array. Otherwise, Go allocates additional storage as needed.

## 4. Slicing normally does not copy elements
When you write:

```go
part := numbers[1:4]
```

the new slice normally shares the underlying array with `numbers`.

## 5. `copy()` copies elements into the destination
To create an independent array of integer values, use:

```go
part := make([]int, len(numbers[1:4]))
copy(part, numbers[1:4])
```

For pointers and other reference-containing element types, copying the elements may still leave referenced data shared.

## 6. Nil and empty slices are different
Both can have length zero, but a nil slice and a non-nil empty slice can behave differently in operations such as JSON encoding.

## 7. Slice operations can affect other slices
Appending, removing, or modifying elements can change data visible through other slices if they share the same underlying array.

---

# 9.23 Chapter Completion Checklist
Before moving to Chapter 10, check that you can do the following without looking at the notes.

- Declare and initialize slices.
- Explain the difference between arrays and slices.
- Explain the difference between `len()` and `cap()`.
- Use slice expressions correctly.
- Explain why subslices can share an underlying array.
- Predict when modifications through one slice affect another.
- Explain how `append()` works.
- Explain what happens when capacity is exhausted.
- Use three-index slicing to limit capacity.
- Create slices with `make()`.
- Explain nil slices versus empty slices.
- Use `copy()` to copy elements.
- Explain shallow copying for slices containing pointers.
- Append, insert, remove, search, reverse, and deduplicate elements.
- Identify and fix common slice-related bugs.

**Next chapter: Chapter 10 — Maps.**

You should now have a consistent set of notes from Section 9.6 through the end of Chapter 9, including practical examples and exercises. Work through the exercises rather than simply reading the output; slices become much easier to understand when you predict the behavior and then verify it by running the code.

