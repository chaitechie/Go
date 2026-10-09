# Chapter 10 — Maps in Go

We will study maps from the beginning, with detailed explanations, code examples, memory behavior, common mistakes, debugging exercises, and a mini-project.

Since you are learning Go by doing, don't just read the examples. Type them into your editor, run them, change the values, and predict the output before executing the program.

## Chapter roadmap

Part 1 — Map fundamentals

What maps are, creating maps, reading, adding, updating, deleting, and checking existence.

Part 2 — Working with maps

Iteration, map length, nil maps, map initialization, and maps with structs.

Part 3 — Deeper understanding

Map internals, reference-like behavior, copying maps, comparability, and common errors.

Part 4 — Exercises and mini-project

Employee management, word frequency, character frequency, duplicates, and a complete word counter.

# 10.1 What Is a Map?

A map is a Go data structure that stores information as key-value pairs.

Imagine maintaining a list of employees and their ages.

Without a map, you might use separate variables:

```go
employee1 := 40
employee2 := 35
employee3 := 30
```

But what does `employee1` mean? Which employee is 40 years old?

A map lets us associate meaningful keys with values.

```go
ages := map[string]int{
    "Saket": 40,
    "Rahul": 35,
    "Amit":  30,
}
```

Here is the conceptual representation:

```text
MAP: ages

Key       Value
"Saket"   40
"Rahul"   35
"Amit"    30
```

Each key identifies its associated value.

You can think of a map as a dictionary:

- The word is the key.
- The definition is the value.

Or as a database table lookup:

- Employee ID is the key.
- Employee information is the value.

Maps are useful when you want to look up a value using a unique identifier.

## 10.1.1 Map syntax

The general syntax is:

```go
map[KeyType]ValueType
```

For example:

```go
map[string]int
```

This means:

- `map` — declares a map type.
- `string` — the type of every key.
- `int` — the type of every value.

Another example:

```go
map[int]string
```

This means integer keys and string values.

```go
countries := map[int]string{
    91: "India",
    1:  "USA",
    44: "UK",
}
```

Here, the country calling code is the key.

```go
fmt.Println(countries[91])
```

Output:

```text
India
```

Notice that the key and value types are independent. A map can have string keys and integer values, or integer keys and string values.

## 10.1.2 Can a map contain different value types?

Consider:

```go
person := map[string]int{
    "age": 40,
    "year": 2026,
}
```

This is valid because every value is an integer.

But the following is invalid:

```go
person := map[string]int{
    "age":  40,
    "name": "Saket",
}
```

Why?

Because the map declares that every value must be an `int`, but `"Saket"` is a string.

If you need different fields with different types, a struct is often the better choice. We will explore that later in this chapter.

# 10.2 Creating Maps

Go gives us several ways to create maps.

## 10.2.1 Method 1 — Map literal

This is the method you have already seen.

```go
package main

import "fmt"

func main() {
    ages := map[string]int{
        "Saket": 40,
        "Rahul": 35,
    }

    fmt.Println(ages)
}
```

Output might be:

```text
map[Rahul:35 Saket:40]
```

The order may differ each time you run the program. Go does not guarantee map iteration order.

A map literal creates and initializes a map in one expression.

## 10.2.2 Method 2 — Using `make`

Suppose you don't know all the entries at the time you create the map.

You can create an empty map using `make`:

```go
ages := make(map[string]int)
```

Now you can add entries later.

```go
package main

import "fmt"

func main() {
    ages := make(map[string]int)

    ages["Saket"] = 40
    ages["Rahul"] = 35

    fmt.Println(ages)
}
```

Output:

```text
map[Rahul:35 Saket:40]
```

The map is initially empty, but it is ready to accept entries.

You can also provide an initial capacity hint:

```go
ages := make(map[string]int, 100)
```

This tells Go that you expect approximately 100 entries.

It is a hint for allocation and performance, not a maximum size. You can add more than 100 entries.

## 10.2.3 Method 3 — Declare a map variable

Consider:

```go
var ages map[string]int
```

This declares a map variable, but it does not initialize a usable map.

Its value is `nil`.

Let's test that:

```go
package main

import "fmt"

func main() {
    var ages map[string]int

    fmt.Println(ages)
    fmt.Println(ages == nil)
    fmt.Println(len(ages))
}
```

Output:

```text
map[]
true
0
```

You can read from a nil map and ask for its length. However, you cannot insert entries into it.

This will panic:

```go
var ages map[string]int

ages["Saket"] = 40
```

The runtime error will be similar to:

```text
panic: assignment to entry in nil map
```

Initialize it first:

```go
var ages map[string]int
ages = make(map[string]int)

ages["Saket"] = 40
```

Or simply:

```go
ages := make(map[string]int)
ages["Saket"] = 40
```

### Important distinction

| Declaration | Can read? | Can insert? |
| --- | --- | --- |
| `var m map[string]int` | Yes | No |
| `make(map[string]int)` | Yes | Yes |
| `map[string]int{"a": 1}` | Yes | Yes |

A nil map and an initialized empty map both have zero entries, but they are not interchangeable for writing.

# 10.3 Reading Values from a Map

Suppose you have:

```go
ages := map[string]int{
    "Saket": 40,
    "Rahul": 35,
}
```

You can read a value using its key:

```go
age := ages["Saket"]

fmt.Println(age)
```

Output:

```text
40
```

The expression:

```go
ages["Saket"]
```

means: look up the value associated with the key `"Saket"`.

## 10.3.1 What if the key does not exist?

Consider:

```go
ages := map[string]int{
    "Saket": 40,
}

fmt.Println(ages["Rahul"])
```

Output:

```text
0
```

Why doesn't Go report an error?

Because the value type is `int`, and the zero value of `int` is `0`.

When a key is absent, a map lookup returns the zero value of its value type.

Examples:

| Map type | Missing key returns |
| --- | --- |
| `map[string]int` | `0` |
| `map[string]string` | `""` |
| `map[string]bool` | `false` |
| `map[string]*Person` | `nil` |
| `map[string][]int` | `nil` |

This creates an important problem.

Suppose Saket's age is actually zero in a test dataset, or you are tracking a numeric setting where zero is a valid value. How can you distinguish between a missing key and a key whose value is zero?

That leads us to the next section.

# 10.4 Checking Whether a Key Exists — `value, ok`

This is one of the most important features of Go maps.

Consider:

```go
ages := map[string]int{
    "Saket": 40,
    "Rahul": 35,
}
```

You can write:

```go
age, ok := ages["Saket"]

fmt.Println(age)
fmt.Println(ok)
```

Output:

```text
40
true
```

The first result is the value. The second result is a boolean indicating whether the key exists.

Now try:

```go
age, ok := ages["Amit"]

fmt.Println(age)
fmt.Println(ok)
```

Output:

```text
0
false
```

A missing key produces the zero value and `false`.

## 10.4.1 Understanding `value, ok`

Lookup: `age, ok := ages["Amit"]`

- First result: `age` → `0` (the default integer value)
- Second result: `ok` → `false` (the key is absent)

The variable names `value` and `ok` are conventional, but you can choose other names.

For example:

```go
age, found := ages["Saket"]

if found {
    fmt.Println("Age:", age)
} else {
    fmt.Println("Employee not found")
}
```

Output:

```text
Age: 40
```

The `if found` block executes only when the key exists.

## 10.4.2 Why is `ok` so important?

Imagine a map storing bank account balances:

```go
balances := map[string]int{
    "account-101": 0,
}
```

Suppose you check two accounts:

```go
fmt.Println(balances["account-101"])
fmt.Println(balances["account-999"])
```

Both produce `0`.

But their meanings differ:

- `account-101` exists and has a balance of zero.
- `account-999` does not exist.

Use the comma-ok form to distinguish them:

```go
balance, exists := balances["account-101"]

if exists {
    fmt.Println("Account exists. Balance:", balance)
} else {
    fmt.Println("Account does not exist")
}
```

## 10.4.3 Checking for existence without using the value

Sometimes you only need to know whether a key exists.

```go
_, exists := ages["Saket"]

if exists {
    fmt.Println("Employee exists")
}
```

The blank identifier `_` tells Go that you do not need the first result.

This pattern is common in real Go applications.

### Practice exercise

Predict the output before running this code:

```go
package main

import "fmt"

func main() {
    scores := map[string]int{
        "A": 0,
        "B": 50,
    }

    a, okA := scores["A"]
    c, okC := scores["C"]

    fmt.Println(a, okA)
    fmt.Println(c, okC)
}
```

Answer:

```text
0 true
0 false
```

The first result is zero in both cases, but `okA` and `okC` tell us the difference.

# 10.5 Adding Entries to a Map

Use map assignment to add a new entry.

```go
ages := make(map[string]int)

ages["Saket"] = 40
ages["Rahul"] = 35
ages["Amit"] = 30
```

Now the map contains three entries.

You can print individual values:

```go
fmt.Println(ages["Saket"])
fmt.Println(ages["Rahul"])
fmt.Println(ages["Amit"])
```

Output:

```text
40
35
30
```

Notice that map assignment uses square brackets, just like map lookup.

```go
ages["Amit"] = 30
```

This assigns a value to the key `"Amit"`.

If the key doesn't exist, Go adds a new entry. If it already exists, Go replaces its existing value.

That means the same syntax handles both insertion and updating.

# 10.6 Updating Existing Entries

Suppose we have:

```go
ages := map[string]int{
    "Saket": 40,
    "Rahul": 35,
}
```

Saket's age changes in our example dataset.

```go
ages["Saket"] = 41
```

Now:

```go
fmt.Println(ages["Saket"])
```

Output:

```text
41
```

The old value is replaced; Go does not create a second `"Saket"` entry.

A map cannot contain two separate entries with equal keys at the same time.

## 10.6.1 A common beginner mistake

Consider:

```go
ages := map[string]int{
    "Saket": 40,
}

ages["Saket"] = 41
ages["Saket"] = 42
```

What is the final value?

It is `42`, because each assignment replaces the previous value associated with that key.

```go
fmt.Println(ages["Saket"])
```

Output:

```text
42
```

## 10.6.2 Add or update depending on existence

You can combine map lookup and assignment.

```go
age, exists := ages["Amit"]

if exists {
    fmt.Println("Current age:", age)
    ages["Amit"] = age + 1
} else {
    ages["Amit"] = 30
}
```

This code updates Amit's age if he exists. Otherwise, it inserts him with an age of 30.

This is useful when the operation depends on whether a key is already present.

# 10.7 Deleting Entries

Go provides the built-in `delete` function.

```go
delete(ages, "Rahul")
```

The syntax is:

```go
delete(mapVariable, key)
```

For example:

```go
package main

import "fmt"

func main() {
    ages := map[string]int{
        "Saket": 40,
        "Rahul": 35,
        "Amit":  30,
    }

    delete(ages, "Rahul")

    fmt.Println(ages)
}
```

The map will contain Saket and Amit. The order of entries in the printed representation is unspecified.

## 10.7.1 What if the key does not exist?

Deleting a nonexistent key is safe.

```go
delete(ages, "Unknown")
```

This does not panic and does not change the map.

Deleting from a nil map is also safe:

```go
var ages map[string]int

delete(ages, "Saket")
```

This is valid, even though inserting into that nil map would panic.

### Important distinction

| Operation on nil map | Result |
| --- | --- |
| Read a key | Returns zero value |
| Check existence | Returns `false` |
| Call `len` | Returns `0` |
| Delete a key | Safe; no effect |
| Insert a key | Panics |

Remember this table. It is particularly useful when debugging Go programs.

# 10.8 Iterating Over a Map

Suppose you want to print all employees and their ages.

You could manually write:

```go
fmt.Println(ages["Saket"])
fmt.Println(ages["Rahul"])
fmt.Println(ages["Amit"])
```

But that approach is not practical when the map contains hundreds or thousands of entries.

Instead, use `range`.

```go
for name, age := range ages {
    fmt.Println(name, age)
}
```

A complete example:

```go
package main

import "fmt"

func main() {
    ages := map[string]int{
        "Saket": 40,
        "Rahul": 35,
        "Amit":  30,
    }

    for name, age := range ages {
        fmt.Println(name, age)
    }
}
```

Possible output:

```text
Saket 40
Rahul 35
Amit 30
```

The order is not guaranteed. Your program might print Rahul first or Amit first instead.

## 10.8.1 How does `range` work here?

```go
for name, age := range ages { 
    fmt.Println(name, age)
}
```

During each iteration:

- `name` receives a key from the map.
- `age` receives the value associated with that key.
- The loop body executes once for that entry.

For a map with three entries, the loop executes three times.

## 10.8.2 Iterate over only the keys

Suppose we have the following map:

```go
ages := map[string]int{
    "Saket": 40,
    "Rahul": 35,
    "Amit":  30,
}
```

Previously, we used two variables in the `range` loop:

```go
for name, age := range ages {
    fmt.Println(name, age)
}
```

But what if you only want to print employee names?

You can use a single variable:

```go
for name := range ages {
    fmt.Println(name)
}
```

Possible output:

```text
Saket
Rahul
Amit
```

The order is not guaranteed.

During each iteration, `name` receives one key from the map. Since the map has three entries, the loop executes three times.

Important: When you use a single variable in a map `range` loop, Go gives you the key, not the value.

For example:

```go
for name := range ages {
    fmt.Println(name)
}
```

This prints the names.

It does not print the ages.

## 10.8.3 Iterate over only the values

Now suppose you want to print only the ages.

You might try:

```go
for age := range ages {
    fmt.Println(age)
}
```

However, this does not print the ages. It prints the keys, and the code will fail to compile because the keys are strings but `age` is inferred as a string variable. The variable name does not change what `range` returns.

To iterate over the values, use the blank identifier `_` for the key:

```go
for _, age := range ages {
    fmt.Println(age)
}
```

Possible output:

```text
40
35
30
```

Let's understand the syntax:

```go
for _, age := range ages
```

- `_` receives the key, which we deliberately ignore.
- `age` receives the associated value.
- `range ages` iterates over the map entries.

The blank identifier tells Go that we don't need that result.

### Practice

Predict what this program prints:

```go
package main

import "fmt"

func main() {
    scores := map[string]int{
        "Math":    90,
        "Science": 85,
        "English": 95,
    }

    for subject := range scores {
        fmt.Println(subject)
    }

    fmt.Println("---")

    for _, score := range scores {
        fmt.Println(score)
    }
}
```

The first loop prints all three subject names. The second prints all three scores. Neither loop guarantees a particular order.

## 10.8.4 Can you rely on map iteration order?

No. Go does not guarantee a stable iteration order for maps.

Consider:

```go
package main

import "fmt"

func main() {
    ages := map[string]int{
        "Saket": 40,
        "Rahul": 35,
        "Amit":  30,
    }

    for name, age := range ages {
        fmt.Println(name, age)
    }
}
```

One execution might print:

```text
Saket 40
Rahul 35
Amit 30
```

Another might print:

```text
Amit 30
Saket 40
Rahul 35
```

Both outputs are valid.

Do not build program logic that assumes maps will be iterated alphabetically, in insertion order, or in the same order on every run.

### What if you need alphabetical order?

For example, suppose you're generating an employee report and want the names sorted alphabetically.

You can collect the map keys into a slice and sort the slice.

```go
package main

import (
    "fmt"
    "sort"
)

func main() {
    ages := map[string]int{
        "Saket": 40,
        "Rahul": 35,
        "Amit":  30,
    }

    names := make([]string, 0, len(ages))

    for name := range ages {
        names = append(names, name)
    }

    sort.Strings(names)

    for _, name := range names {
        fmt.Println(name, ages[name])
    }
}
```

Output:

```text
Amit 30
Rahul 35
Saket 40
```

Let's break this down.

Step 1: Create a slice to hold the names.

```go
names := make([]string, 0, len(ages))
```

This creates an empty string slice with an initial capacity equal to the number of map entries.

Step 2: Copy the keys into the slice.

```go
for name := range ages {
    names = append(names, name)
}
```

Now `names` contains all the map keys, but in no guaranteed order.

Step 3: Sort the slice.

```go
sort.Strings(names)
```

This sorts the strings alphabetically.

Step 4: Print each name and its corresponding age.

```go
for _, name := range names {
    fmt.Println(name, ages[name])
}
```

We iterate over the sorted slice and use each name to retrieve the corresponding value from the map.

Notice that we sorted the slice, not the map itself. Go maps do not have a built-in sorting operation.

# 10.9 Finding the Number of Entries

Go provides the built-in `len()` function to determine the number of entries in a map.

```go
ages := map[string]int{
    "Saket": 40,
    "Rahul": 35,
    "Amit":  30,
}

fmt.Println(len(ages))
```

Output:

```text
3
```

There are three key-value pairs in the map.

Now let's delete an entry:

```go
delete(ages, "Rahul")

fmt.Println(len(ages))
```

Output:

```text
2
```

The map now has two entries.

## 10.9.1 Does `len()` count keys or values?

It counts entries, meaning the number of key-value pairs.

For example:

```go
products := map[string]int{
    "Laptop":  10,
    "Keyboard": 25,
    "Mouse":    40,
}
```

Then:

```go
fmt.Println(len(products))
```

Output:

```text
3
```

It does not count the total stock of all products. That would require iterating over the values and adding them.

```go
totalStock := 0

for _, stock := range products {
    totalStock += stock
}

fmt.Println(totalStock)
```

Output:

```text
75
```

Here, `len(products)` is `3`, but the total stock is `75`.

## 10.9.2 What is the length of a nil map?

Consider:

```go
var ages map[string]int

fmt.Println(len(ages))
```

Output:

```text
0
```

A nil map contains no entries, so `len()` returns zero.

This is safe even though you haven't initialized the map.

# 10.10 Which Types Can Be Used as Map Keys?

This is an important Go language rule.

A map key must have a comparable type.

In simple terms, Go must be able to compare two values of that type for equality.

## 10.10.1 Valid map key types

These are valid:

```go
map[string]int
map[int]string
map[bool]string
map[float64]string
map[[2]int]string
```

Let's look at each one.

### String keys

```go
ages := map[string]int{
    "Saket": 40,
}
```

Strings can be compared using equality operators.

```go
fmt.Println("Saket" == "Saket")
```

Output:

```text
true
```

### Integer keys

```go
students := map[int]string{
    101: "Rahul",
    102: "Amit",
}
```

Integer keys are valid because integers are comparable.

### Boolean keys

```go
answers := map[bool]string{
    true:  "Yes",
    false: "No",
}
```

Boolean keys are also valid.

### Array keys

Arrays can be map keys if their element type is comparable.

```go
locations := map[[2]int]string{
    [2]int{10, 20}: "Point A",
    [2]int{30, 40}: "Point B",
}
```

Here, each key is an array containing two integers.

This is valid because integer arrays of the same type and length are comparable.

## 10.10.2 Invalid map key types

You cannot use slices, maps, or functions as ordinary map key types.

For example:

```go
// Invalid: slices are not comparable.
m := map[[]int]string{}
```

The compiler rejects this.

Similarly:

```go
// Invalid: maps are not comparable.
m := map[map[string]int]string{}
```

And:

```go
// Invalid: functions are not comparable.
m := map[func()]string{}
```

The reason is that these types do not support ordinary equality comparison between two values of the same type.

For slices, you cannot write:

```go
a := []int{1, 2}
b := []int{1, 2}

// Invalid:
// fmt.Println(a == b)
```

You can compare a slice to `nil`, but you cannot compare two slices using `==`.

This is different from arrays:

```go
a := [2]int{1, 2}
b := [2]int{1, 2}

fmt.Println(a == b)
```

Output:

```text
true
```

That is why `[2]int` can be a map key while `[]int` cannot.

### A note about floating-point keys

Floating-point types are comparable and can be used as map keys. However, special values such as `NaN` behave unusually because `NaN != NaN`.

For ordinary beginner exercises, strings and integers are usually the most straightforward choices.

# 10.11 Maps with Structs

Until now, we have used maps such as:

```go
ages := map[string]int{
    "Saket": 40,
}
```

But in real applications, an employee has more than an age.

An employee might have:

- An ID
- A name
- An age
- A department
- A salary

How do we store all this information?

We can define a struct and use it as the map's value type.

## 10.11.1 Define an employee struct

```go
type Employee struct {
    Name       string
    Age        int
    Department string
    Salary     float64
}
```

This struct represents one employee.

For example:

```go
employee := Employee{
    Name:       "Saket",
    Age:        40,
    Department: "Engineering",
    Salary:     150000,
}
```

Now we can create a map where the key is an employee ID and the value is an `Employee`.

```go
employees := map[string]Employee{
    "E101": {
        Name:       "Saket",
        Age:        40,
        Department: "Engineering",
        Salary:     150000,
    },
    "E102": {
        Name:       "Rahul",
        Age:        35,
        Department: "Finance",
        Salary:     120000,
    },
}
```

Conceptually:

Key

Value

`"E101"`

Employee struct for Saket

`"E102"`

Employee struct for Rahul

The map key uniquely identifies the employee within this map.

## 10.11.2 Retrieve an employee

```go
employee := employees["E101"]

fmt.Println(employee.Name)
fmt.Println(employee.Age)
fmt.Println(employee.Department)
fmt.Println(employee.Salary)
```

Output:

```text
Saket
40
Engineering
150000
```

First, Go looks up the employee ID. Then we access the fields of the resulting struct.

## 10.11.3 Check whether the employee exists

You should use the comma-ok pattern:

```go
employee, exists := employees["E101"]

if exists {
    fmt.Println("Name:", employee.Name)
} else {
    fmt.Println("Employee not found")
}
```

This is preferable when the program needs to distinguish a missing employee from an existing employee whose fields contain zero values.

For example, if you look up a nonexistent employee, the returned `Employee` value is the zero value of that struct, but `exists` will be `false`.

## 10.11.4 Updating a struct stored in a map

Here is a subtle rule that is very important.

Suppose you try this:

```go
employees["E101"].Age = 41
```

This does not compile.

Why?

When you retrieve a struct value from a map, you receive a copy of that struct. The map index expression is not an addressable struct variable whose field you can directly modify.

Instead, follow these steps.

Step 1: Retrieve the struct.

```go
employee := employees["E101"]
```

Step 2: Modify the local copy.

```go
employee.Age = 41
```

Step 3: Assign the modified struct back to the map.

```go
employees["E101"] = employee
```

The complete code is:

```go
employee := employees["E101"]
employee.Age = 41
employees["E101"] = employee
```

Now the map contains the updated employee.

### Why must we assign it back?

Because the struct retrieved from the map is a value copy.

Modifying the local variable does not automatically update the struct stored in the map. The final assignment replaces the map entry with the modified copy.

## 10.11.5 Alternative: Store pointers to structs

You can also declare a map that stores pointers:

```go
employees := make(map[string]*Employee)
```

Now each value is a pointer to an `Employee`.

```go
employees["E101"] = &Employee{
    Name:       "Saket",
    Age:        40,
    Department: "Engineering",
    Salary:     150000,
}
```

You can modify the employee's age through the pointer:

```go
employees["E101"].Age = 41
```

Go automatically dereferences the pointer when accessing the field.

This is useful when you deliberately want multiple parts of a program to share the same employee object. However, pointer-based maps require you to consider nil pointers and shared mutable state.

For now, the value-based map is a good starting point.

# 10.12 Are Maps Reference Types?

You may hear people say that maps are reference types.

This is a useful informal description, but let's understand the behavior more precisely.

Consider:

```go
package main

import "fmt"

func main() {
    a := map[string]int{
        "Saket": 40,
    }

    b := a

    b["Saket"] = 41
    b["Rahul"] = 35

    fmt.Println(a)
    fmt.Println(b)
}
```

Output:

```text
map[Rahul:35 Saket:41]
map[Rahul:35 Saket:41]
```

Why does modifying `b` also affect the map you access through `a`?

When you assign one map value to another, Go does not copy every entry into a new independent map. Both variables refer to the same underlying map data.

Conceptually:

Variable `a`

Variable `b`

Same underlying map

Saket → 41

Rahul → 35

The assignment:

```go
b := a
```

does not create a separate map containing independent copies of all entries.

## 10.12.1 Passing a map to a function

The same behavior occurs when passing a map to a function.

```go
package main

import "fmt"

func updateAge(ages map[string]int) {
    ages["Saket"] = 41
}

func main() {
    ages := map[string]int{
        "Saket": 40,
    }

    updateAge(ages)

    fmt.Println(ages["Saket"])
}
```

Output:

```text
41
```

The map is shared by reference semantics, so the function updates the original map.

This is one of the key behaviors to remember when writing Go programs with maps.

The map is shared by reference semantics, so the function updates the original map.

This is one of the key behaviors to remember when writing Go programs with maps.


