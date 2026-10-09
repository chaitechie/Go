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

You
