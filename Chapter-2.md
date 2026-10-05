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
| --- | --- |
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

```go
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

```text
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

```go
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

```text
:=   → create a new variable
=    → assign/change the value of an existing variable
```

For example:

```go
age := 40   // create
age = 41    // change
```

This distinction will become **very important later**, especially when we start working with functions, scopes, packages, and more complex Go programs.

---

# 9. `gofmt` vs `go fmt`
These are related but not identical commands.

### `gofmt`
Directly invokes the Go formatter.

For example:

```bash
gofmt -w main.go
```

### `go fmt`
Uses the Go command to format packages.

For example:

```bash
go fmt
```

For now, you can think:

```text
gofmt
   ↓
format Go source files

go fmt
   ↓
ask Go to format packages
```

We'll revisit this distinction later.

---

# 10. The Compiler
The compiler's job is fundamentally:

> Translate Go source code into machine-level code suitable for the target platform.
Conceptually:

```text
main.go
   │
   ↓
Go compiler
   │
   ↓
machine/object code
```

The compiler also performs extensive checks.

For example:

```go
fmt.Println("Hello"
```

contains invalid syntax.

The compiler catches that before a working executable can be produced.

---

# 11. The Linker
Another important component is the **linker**.

Very simplified:

```text
Go source
    │
    ↓
 compiler
    │
    ↓
 compiled code
    │
    ↓
 linker
    │
    ↓
 executable
```

The linker combines the pieces needed to create the final executable.

Don't worry about the exact internal implementation yet. The important mental model is:

```text
Compiler → produces compiled code

Linker → combines required compiled pieces into the executable
```

---

# 12. Source Code → Compilation → Linking → Executable
Let's put everything together.

Suppose:

```text
main.go
```

contains:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello")
}
```

Conceptually:

```text
             main.go
                │
                ↓
        Go package analysis
                │
                ↓
             Compiler
                │
                ↓
       compiled program code
                │
                ↓
              Linker
                │
                ↓
          final executable
                │
                ↓
               OS
                │
                ↓
               CPU
```

This is the basic compilation pipeline you should keep in your head.

---

# 13. But What About `fmt`?
Our program contains:

```go
import "fmt"
```

So how does Go know where `fmt` comes from?

This leads us to **packages**.

Go doesn't treat everything as one giant source file.

Go programs are organized into **packages**.

For example:

```text
main package
     │
     └── imports
             │
             ↓
            fmt
```

The Go toolchain resolves the imported package and makes the necessary compiled code available during the build.

---

# 14. Operating System and CPU Architecture
Now we need to understand something fundamental.

A program compiled for one operating system and CPU architecture isn't necessarily directly executable on another.

For example, your Mac is:

```text
Operating System: macOS
Architecture: ARM64
```

Go represents these using:

```text
GOOS
GOARCH
```

For your Mac, these are typically:

```text
GOOS=darwin
GOARCH=arm64
```

---

# 15. What Is `GOOS`?
`GOOS` means:

> **Go Operating System**
It tells Go the target operating system.

For example:

```text
darwin
linux
windows
freebsd
```

Your Mac:

```bash
go env GOOS
```

will normally produce:

```text
darwin
```

because Apple's operating system is Darwin-based.

---

# 16. What Is `GOARCH`?
`GOARCH` means:

> **Go Architecture**
It specifies the target CPU architecture.

For example:

```text
amd64
arm64
386
arm
```

Your Apple Silicon Mac normally reports:

```bash
go env GOARCH
```

as:

```text
arm64
```

---

# 17. Why Do `GOOS` and `GOARCH` Matter?
Suppose you compile:

```text
GOOS=darwin
GOARCH=arm64
```

You're creating a program for:

```text
macOS + ARM64
```

But suppose you compile:

```text
GOOS=linux
GOARCH=amd64
```

Now you're creating a program for:

```text
Linux + x86-64
```

Those are different targets.

---

# 18. Cross Compilation
One of Go's very useful capabilities is **cross compilation**.

Cross compilation means:

> Build a program on one platform for another platform.
For example, you're sitting on:

```text
Mac
ARM64
```

and want to produce a:

```text
Linux
AMD64
```

executable.

You can do:

```bash
GOOS=linux GOARCH=amd64 go build
```

Conceptually:

```text
Your Mac
darwin/arm64
     │
     │ Go compiler
     ↓
Linux executable
linux/amd64
```

You don't need to run Linux just to compile the program for Linux.

---

# 19. Another Cross-Compilation Example
Build for Linux ARM64:

```bash
GOOS=linux GOARCH=arm64 go build
```

Build for Windows AMD64:

```bash
GOOS=windows GOARCH=amd64 go build
```

Build for macOS ARM64:

```bash
GOOS=darwin GOARCH=arm64 go build
```

So the combination:

```text
GOOS + GOARCH
```

defines an important part of the target.

---

# 20. Why This Matters for Docker and Kubernetes
This will become very relevant later.

Suppose you develop on:

```text
Mac
ARM64
```

but deploy your application to a:

```text
Linux
AMD64
```

server.

Then you need to understand:

```text
GOOS
GOARCH
```

and eventually:

```text
Docker image architecture
Kubernetes node architecture
```

For example:

```text
Developer Mac
darwin/arm64
       │
       │ build
       ↓
Linux/amd64 binary
       │
       ↓
Docker image
       │
       ↓
Kubernetes
```

This is why we're learning this now rather than treating `GOOS` and `GOARCH` as random environment variables.

---

# 21. What Is `GOROOT`?
Now let's return to:

```bash
go env GOROOT
```

`GOROOT` points to the Go installation/toolchain environment.

You can think of it as:

```text
GOROOT
   │
   ├── Go compiler/toolchain
   ├── standard library source
   └── other Go installation components
```

Run:

```bash
go env GOROOT
```

You may get a path specific to your installation.

**Do not manually modify files under GOROOT.**

Go manages this installation.

---

# 22. What Is `GOPATH`?
Now:

```bash
go env GOPATH
```

might give you something such as:

```text
/Users/saket/go
```

`GOPATH` has several purposes in modern Go, including locations for:

- downloaded module/cache information
- installed Go executables
- other user-specific Go data

The important thing is:

> `GOPATH` is not the same thing as `GOROOT`.
Think:

```text
GOROOT
   ↓
Go itself

GOPATH
   ↓
Your user-level Go environment
```

---

# 23. The Old GOPATH Model
Historically, Go projects were commonly organized like:

```text
$GOPATH/
└── src/
    └── github.com/
        └── someone/
            └── project/
```

For example:

```text
$GOPATH/src/github.com/example/myapp
```

That was the old **GOPATH workflow**.

Modern Go development uses **modules**.

Therefore your project can be somewhere completely unrelated to GOPATH:

```text
/Users/saket/Documents/Learn/GO/app
```

This is perfectly normal.

---

# 24. Why Were Modules Introduced?
The old GOPATH model had limitations.

Modern Go uses **Go modules** to explicitly describe a project's dependency requirements.

A module can define:

```text
Who am I?
What dependencies do I need?
What versions do I need?
```

This information is primarily represented in:

```text
go.mod
```

---

# 25. First Look at `go.mod`
Suppose you have:

```text
myapp/
└── main.go
```

You can initialize a module:

```bash
go mod init example.com/myapp
```

Go creates:

```text
myapp/
├── go.mod
└── main.go
```

The `go.mod` file might initially contain:

```go
module example.com/myapp

go 1.26
```

The exact Go version depends on your installed/current toolchain and the command used.

---

# 26. What Is the Module Path?
This line:

```go
module example.com/myapp
```

defines the **module path**.

This is extremely important.

The module path is an identity for the module.

For example:

```go
module github.com/example/myapp
```

means the module's path is:

```text
github.com/example/myapp
```

That path can then be used when importing packages belonging to that module.

---

# 27. Project Directory vs Module Path
This is something you previously asked about, so let's make it very clear.

Suppose your physical directory is:

```text
/Users/saket/Documents/Learn/GO/app
```

Your module path could be:

```text
example.com/company/employee-api
```

These are different things.

```text
Physical directory
        │
        ↓
/Users/saket/Documents/Learn/GO/app

Module identity
        │
        ↓
example.com/company/employee-api
```

They do **not** have to be identical.

---

# 28. Then Why Do People Usually Use GitHub URLs?
You often see:

```go
module github.com/user/project
```

because module paths are commonly based on repository locations.

For example:

```text
github.com/jonbodner/proteus
```

can serve as both:

```text
repository location
```

and:

```text
module path
```

This is convenient because Go can use the module path to locate the module.

But conceptually:

> A module path is an identifier/path used by Go's module system. It is not simply the physical directory name.

---

# 29. Can the Module Path Be Different From the Directory?
Yes.

For example:

```text
Physical directory:

/Users/saket/Documents/Learn/GO/app
```

and:

```go
module example.com/employee-api
```

are completely possible.

The directory is where the files physically exist.

The module path identifies the module to Go.

---

# 30. How Does Go Find Packages?
Suppose your program says:

```go
import "fmt"
```

Go needs to determine:

> Where is the `fmt` package?
For a standard library package such as `fmt`, Go knows it belongs to the Go standard library.

Now suppose you write:

```go
import "github.com/someuser/somelib"
```

That's different.

Go needs to determine which module provides:

```text
github.com/someuser/somelib
```

This is where the module system becomes important.

---

# 31. Standard Library Packages
Go ships with a large **standard library**.

Examples include:

```text
fmt
os
strings
net/http
encoding/json
database/sql
context
time
sync
errors
```

For example:

```go
import "fmt"
```

doesn't mean:

> Download fmt from GitHub.
`fmt` is part of Go's standard library.

---

# 32. Third-Party Packages
A third-party package is code maintained outside the Go standard library.

For example, later you may use:

```go
github.com/jackc/pgx/v5
```

for PostgreSQL.

Or:

```go
go.mongodb.org/mongo-driver
```

for MongoDB.

These are not part of the standard library.

The module system manages these external dependencies.

---

# 33. Standard Library vs Third-Party
A useful mental model:

```text
                 Go program
                     │
            ┌────────┴────────┐
            │                 │
            ↓                 ↓
     Standard library    Third-party
            │                 │
            ↓                 ↓
         fmt, os          pgx, MongoDB
         net/http         Kafka clients
         context          etc.
```

The distinction is important because dependency management works differently for these two categories.

---

# 34. What Happens When You Import a Third-Party Package?
Suppose:

```go
import "github.com/jackc/pgx/v5"
```

Your project needs a module that provides that package.

Go's module system determines the required module and version.

The dependency can then be recorded in:

```text
go.mod
```

and checksums are recorded in:

```text
go.sum
```

We'll study this in depth in **Packages & Modules**.

For now, just understand the relationship:

```text
import
  ↓
package
  ↓
module
  ↓
dependency version
```

---

# 35. What Is `go.sum`?
When external dependencies are used, you will often see:

```text
go.sum
```

alongside:

```text
go.mod
```

For example:

```text
app/
├── go.mod
├── go.sum
└── main.go
```

`go.sum` contains cryptographic checksums for module content.

Very roughly:

```text
go.mod
   ↓
What dependencies/versions are required?

go.sum
   ↓
Checksums for module content
```

Don't edit `go.sum` manually.

We'll cover it properly when we study modules.

---

# 36. What Is `GOTOOLCHAIN`?
Now we come to a variable you've specifically worked with before:

```bash
go env GOTOOLCHAIN
```

`GOTOOLCHAIN` controls aspects of **which Go toolchain version is selected** when the Go command runs.

This became especially important with newer Go releases, because Go can work with different toolchain versions and can automatically select a newer toolchain when required by a module.

You might see:

```text
auto
```

or another value.

---

# 37. Why Does Toolchain Selection Matter?
Imagine:

```text
Your installed Go toolchain
        ↓
      Go 1.26
```

but a module specifies requirements involving another Go version.

The Go command can use the module's requirements when determining the appropriate toolchain behavior.

This is why you may encounter messages related to:

```text
toolchain
```

or:

```text
GOTOOLCHAIN
```

when working with modules.

We'll study this much more deeply in **Packages & Modules**, because that's where it becomes practically important.

---

# 38. `go env` — Your Window Into Go's Configuration
Rather than guessing what Go is configured to do, ask Go.

Run:

```bash
go env
```

Or query a specific variable:

```bash
go env GOOS
```

```bash
go env GOARCH
```

```bash
go env GOROOT
```

```bash
go env GOPATH
```

```bash
go env GOTOOLCHAIN
```

This is much better than memorizing paths.

---

# 39. `go env -w`
Go also provides a way to persist certain environment configuration values.

For example:

```bash
go env -w GOTOOLCHAIN=auto
```

This writes the setting into Go's environment configuration.

However:

**Do not randomly change Go environment variables while learning.**

First understand what a variable does.

You can inspect the configuration with:

```bash
go env
```

and we'll deliberately change values later when an experiment requires it.

---

# 40. `GOOS` and `GOARCH` Are Often Temporary Environment Settings
For cross compilation, you can write:

```bash
GOOS=linux GOARCH=amd64 go build
```

Notice something important:

You didn't permanently change your environment.

The variables apply to that command invocation.

Conceptually:

```text
GOOS=linux
GOARCH=amd64
      │
      ↓
   go build
```

After that command finishes, your normal environment remains unchanged.

---

# 41. Hands-On Experiment 1 — Inspect Your Environment
Run:

```bash
go version
```

Then:

```bash
go env GOOS
```

Then:

```bash
go env GOARCH
```

Then:

```bash
go env GOROOT
```

Then:

```bash
go env GOPATH
```

Then:

```bash
go env GOTOOLCHAIN
```

You should understand what category of information each command gives you.

---

# 42. Hands-On Experiment 2 — Build for Another OS
Create:

```text
hello/
└── main.go
```

with:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello from Go")
}
```

First build normally:

```bash
go build
```

Check:

```bash
ls -l
```

Then build for Linux:

```bash
GOOS=linux GOARCH=amd64 go build -o hello-linux
```

Check:

```bash
ls -l
```

You should now have something like:

```text
hello
hello-linux
main.go
```

Your Mac normally cannot execute the Linux binary directly.

But you have successfully **built a Linux executable from your Mac**.

---

# 43. Hands-On Experiment 3 — Linux ARM64
Try:

```bash
GOOS=linux GOARCH=arm64 go build -o hello-linux-arm64
```

Now you have targeted:

```text
Linux
+
ARM64
```

So conceptually you have:

```text
Mac ARM64
    │
    ├──→ Linux AMD64
    │
    └──→ Linux ARM64
```

This capability is extremely useful in cloud and container development.

---

# 44. Hands-On Experiment 4 — Windows
You can also try:

```bash
GOOS=windows GOARCH=amd64 go build -o hello.exe
```

Now you've asked Go to produce:

```text
Windows
+
AMD64
```

The important lesson is not the specific binary.

The important lesson is:

> Go can target a different OS/architecture than the machine doing the compilation.

---

# 45. Hands-On Experiment 5 — Inspect the Executable
After:

```bash
go build
```

run:

```bash
file hello
```

On macOS, you may see information describing the executable's architecture and format.

This is useful because you're no longer simply trusting the filename.

You're asking the operating system's tools:

> What kind of executable is this?

---

# 46. Hands-On Experiment 6 — Create a Module
Create a new directory:

```bash
mkdir module-demo
cd module-demo
```

Create:

```text
main.go
```

with:

```go
package main

import "fmt"

func main() {
    fmt.Println("Module demo")
}
```

Now run:

```bash
go mod init example.com/module-demo
```

Check:

```bash
ls -la
```

You should now see:

```text
go.mod
main.go
```

---

# 47. Examine `go.mod`
Run:

```bash
cat go.mod
```

You should see something similar to:

```go
module example.com/module-demo

go 1.26
```

The exact Go version line may differ.

Now you have your first real module.

---

# 48. Run the Module
Instead of:

```bash
go run main.go
```

you can run:

```bash
go run .
```

Notice the difference.

```bash
go run main.go
```

means roughly:

> Run this source file.
Whereas:

```bash
go run .
```

means roughly:

> Run the package represented by the current directory.
This distinction becomes very important once a package contains multiple `.go` files.

---

# 49. Why `go run .` Is Important
Suppose later your directory contains:

```text
app/
├── go.mod
├── main.go
├── database.go
└── config.go
```

All these files may belong to the same package:

```go
package main
```

Running:

```bash
go run .
```

allows Go to work with the package represented by the current directory.

This is generally more representative of how real Go projects are developed than always specifying one source file.

---

# 50. Hands-On Experiment 7 — Add Another Go File
Inside your module:

```text
module-demo/
├── go.mod
└── main.go
```

Create:

```text
message.go
```

Put:

```go
package main

import "fmt"

func printMessage() {
    fmt.Println("Hello from message.go")
}
```

Now change `main.go`:

```go
package main

func main() {
    printMessage()
}
```

Run:

```bash
go run .
```

You should get:

```text
Hello from message.go
```

This demonstrates something important:

> A package can contain multiple Go source files.
The package is the unit being built, not necessarily one `.go` file.

---

# 51. Why `go run main.go` Is Different Here
If you run:

```bash
go run main.go
```

you are explicitly asking Go to work with that specified file set.

But:

```bash
go run .
```

works with the package represented by the current directory.

This distinction will become increasingly important as your projects become larger.

For real applications, you'll often see:

```bash
go run .
```

or:

```bash
go build .
```

rather than explicitly naming a single source file.

---

# 52. Packages and Modules — First Mental Model
We're not yet studying Packages & Modules in full.

But you need the basic hierarchy now:

```text
Module
  │
  ├── Package
  │      │
  │      ├── Go file
  │      ├── Go file
  │      └── Go file
  │
  ├── Package
  │
  └── Package
```

For example:

```text
employee-api/
│
├── go.mod
│
├── main.go
│
├── database/
│   ├── postgres.go
│   └── queries.go
│
└── handler/
    ├── employee.go
    └── health.go
```

Potentially:

```text
employee-api
     │
     ├── main package
     │
     ├── database package
     │
     └── handler package
```

We'll build this properly later.

---

# 53. A Very Important Distinction
Don't confuse these three things:

```text
File
Package
Module
```

They are related, but they're not the same.

### File
Example:

```text
main.go
```

### Package
A collection of Go source files that belong to the same package.

Example:

```go
package main
```

### Module
A larger unit of Go code/dependencies identified by a module path and described by `go.mod`.

Example:

```go
module example.com/employee-api
```

So:

```text
Module
   │
   ├── Package
   │      ├── file
   │      └── file
   │
   └── Package
          ├── file
          └── file
```

---

# 54. The Big Picture
We can now expand our original diagram.

```text
                         Go Project
                             │
                             ↓
                           Module
                             │
                           go.mod
                             │
             ┌───────────────┴───────────────┐
             │                               │
          Package                         Package
             │                               │
       ┌─────┴─────┐                   ┌─────┴─────┐
       │           │                   │           │
      .go         .go                 .go         .go
       │           │                   │           │
       └─────┬─────┘                   └─────┬─────┘
             │                               │
             └──────────────┬────────────────┘
                            ↓
                       Go command
                            │
                 ┌──────────┴──────────┐
                 ↓                     ↓
              Compiler               Linker
                 │                     │
                 └──────────┬──────────┘
                            ↓
                       Executable
                            │
                            ↓
                         OS/CPU
```

This is the foundation for everything that follows.

---

# 55. What You Should Understand Now
After Part 2, you should be able to explain these in your own words:

### `go run`
> Builds the required program and runs it, normally using a temporary executable.

### `go build`
> Builds the program and produces an executable without running it.

### `go`
> The main command used to interact with the Go toolchain.

### Compiler
> Translates Go source code into compiled machine-level code and performs language checks.

### Linker
> Combines the compiled pieces needed for the final executable.

### `GOOS`
> Target operating system.

### `GOARCH`
> Target CPU architecture.

### Cross compilation
> Building a program for a different OS/architecture than the machine doing the build.

### `GOROOT`
> Location/environment associated with the Go installation and toolchain.

### `GOPATH`
> User-level Go environment used for things such as module cache and installed binaries; it is not the required location for modern projects.

### `GOTOOLCHAIN`
> Controls Go toolchain selection behavior.

### Module
> A versioned unit of Go code identified by a module path and described by `go.mod`.

### Module path
> The identity/path used by Go's module system.

### Package
> A collection of Go source files that are compiled together as a unit.

---

# 56. Exercises — Part 2
Now I want you to actually perform these.

## Exercise 1 — Inspect Go
Run:

```bash
go version
```

```bash
go env
```

Then:

```bash
go env GOOS GOARCH GOROOT GOPATH GOTOOLCHAIN
```

Write down what each means.

---

## Exercise 2 — `go run`
Create:

```text
hello/
└── main.go
```

Run:

```bash
go run main.go
```

Then check:

```bash
ls -la
```

Ask yourself:

> Where is my permanent executable?

---

## Exercise 3 — `go build`
Run:

```bash
go build
```

Then:

```bash
ls -la
```

Identify the executable.

Run:

```bash
./<executable-name>
```

---

## Exercise 4 — Cross Compile
Build:

```bash
GOOS=linux GOARCH=amd64 go build -o hello-linux
```

Then:

```bash
file hello-linux
```

Try to identify:

```text
Operating system
Architecture
```

---

## Exercise 5 — Module
Create:

```bash
mkdir module-demo
cd module-demo
```

Then:

```bash
go mod init example.com/module-demo
```

Inspect:

```bash
cat go.mod
```

---

## Exercise 6 — Package vs File
Create:

```text
module-demo/
├── go.mod
├── main.go
└── message.go
```

Put both files in:

```go
package main
```

Define a function in `message.go` and call it from `main.go`.

Run:

```bash
go run .
```

Then compare:

```bash
go run .
```

with:

```bash
go run main.go
```

Observe what happens and think about why.

---

# 57. One Final Mental Model for Part 2
If you remember only one diagram from this section, remember this:

```text
                 YOUR PROJECT
                      │
                      ↓
                   MODULE
                      │
                   go.mod
                      │
                      ↓
                  PACKAGES
                      │
                      ↓
                 .go FILES
                      │
                      ↓
                 GO COMMAND
                    `go`
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
       analyze      compile      manage
          │           │         modules/etc.
          │           ↓
          │         linker
          │           │
          └───────────┤
                      ↓
                 EXECUTABLE
                      │
                      ↓
                 GOOS/GOARCH
                      │
                      ↓
                 OPERATING SYSTEM
                      │
                      ↓
                     CPU
```

And the most important distinction is:

```text
FILE
  ↓
belongs to a
  ↓
PACKAGE
  ↓
belongs to a
  ↓
MODULE
```

That distinction will become **very important** in the next major Go topic: Packages & Modules.

---

# 58. Chapter 1 Status
At this point, Chapter 1 has covered:

- What Go is
- Why Go was created
- Compiled language concept
- Source code vs executable
- `package main`
- `func main`
- `import`
- `fmt.Println`
- `go run`
- `go build`
- Go compiler
- Go linker
- Go toolchain
- `go`
- `gofmt`
- `go fmt`
- `GOOS`
- `GOARCH`
- Cross compilation
- `GOROOT`
- `GOPATH`
- `GOTOOLCHAIN`
- `go env`
- Standard library
- Third-party packages
- Packages
- Modules
- `go.mod`
- Module paths
- Project directories vs module paths
- `go run .`
- Multiple files in a package

**Do the exercises before moving on.** The next part can then take the things you've actually observed on your Mac and explain exactly **how Go resolves packages, how modules and dependencies are resolved, what `go.mod` really controls, and how the Go command builds a multi-package application.**
