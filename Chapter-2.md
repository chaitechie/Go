# Basic Chapter 1 — Part 2

## Go Toolchain, Compilation, Environment, Packages, and Modules
In Part 1, we established the basic model:

```text
Go source code
      ↓
   compiler
      ↓
 machine code
      ↓
 executable
      ↓
    CPU
```

Now we're going to make that model much more precise.

This part is important because later, when you work with:

- Packages
- Modules
- PostgreSQL
- MongoDB
- Kafka
- REST APIs
- Docker
- Kubernetes

you will constantly interact with the Go toolchain.

---

# 1. What Exactly Happens When `go run` Executes?
Suppose you have:

```text
hello/
└── main.go
```

and `main.go` contains:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello")
}
```

You execute:

```bash
go run main.go
```

A beginner might imagine:

```text
main.go
   ↓
run
```

But that's not what actually happens.

A better mental model is:

```text
             go run main.go
                    │
                    ↓
              Go command
                    │
                    ↓
          Analyze Go source
                    │
                    ↓
              Compile
                    │
                    ↓
           Link dependencies
                    │
                    ↓
        Temporary executable
                    │
                    ↓
               Execute
                    │
                    ↓
                 Output
```

So `go run` still involves compilation.

---

# 2. Does `go run` Create an Executable?
Yes.

But normally it creates a **temporary executable** rather than leaving the executable in your current directory as `go build` does.

Conceptually:

```text
main.go
   │
   ↓
compile
   │
   ↓
temporary executable
   │
   ↓
run
   │
   ↓
output
```

That's why after:

```bash
go run main.go
```

you normally don't see a permanent executable appearing beside:

```text
main.go
```

---

# 3. What Exactly Happens During `go build`?
Now run:

```bash
go build
```

The process is conceptually:

```text
Go source
    │
    ↓
Package analysis
    │
    ↓
Compile
    │
    ↓
Object/code generation
    │
    ↓
Link
    │
    ↓
Executable
```

Unlike `go run`, the resulting executable is normally left in your directory.

For example:

```text
hello/
├── main.go
└── hello
```

Then:

```bash
./hello
```

executes the compiled program.

---

# 4. `go run` vs `go build`
This distinction is worth memorizing.

| Command | Main purpose |
| --- | --- |
| `go run` | Build and immediately run |
| `go build` | Build executable |
| `go test` | Build and run tests |
| `go fmt` | Format packages |
| `go mod` | Work with modules |
| `go get` | Add/update module dependencies |
| `go install` | Build and install executable/package |

Conceptually:

```text
go run
    ↓
build + execute
```

while:

```text
go build
    ↓
build
```

---

# 5. The `go` Command vs the Go Compiler
This distinction is extremely important.

When you type:

```bash
go build
```

the `go` command is **not itself the compiler**.

Think of:

```text
             go
             │
       Go command/tool
             │
       ┌─────┼─────┐
       │     │     │
       ↓     ↓     ↓
    build   test   fmt
       │
       ↓
   compiler
       │
       ↓
    linker
```

The `go` command is a **tool that orchestrates the Go development process**.

It knows how to:

- find packages
- understand modules
- determine dependencies
- invoke the compiler
- invoke the linker
- run tests
- format code
- manage modules
- install programs
- inspect the Go environment

So:

```bash
go
```

is more like the **front door to the Go toolchain**.

---

# 6. What Is the Go Toolchain?
The **Go toolchain** is the collection of programs and supporting components used to develop and build Go software.

It includes things such as:

```text
go command
compiler
linker
formatter
test infrastructure
documentation tools
module tooling
other development tools
```

You interact with much of this through:

```bash
go ...
```

For example:

```bash
go build
go test
go fmt
go mod
go env
go list
go doc
```

---

# 7. `go` — The Main Command
Run:

```bash
go help
```

You will see many commands.

Some important ones are:

```text
build
clean
doc
env
fmt
generate
get
install
list
mod
run
test
tool
version
```

Think of:

```bash
go
```

as a command dispatcher.

For example:

```bash
go build
```

means:

> Ask the Go tool to perform a build.
And:

```bash
go test
```

means:

> Ask the Go tool to test the package.

---

# 8. `gofmt`
`gofmt` is the standard Go source-code formatter.

For example, suppose you write ugly formatting:

```go
package main
import "fmt"
func main(){fmt.Println("Hello")}
```

Run:

```bash
gofmt -w main.go
```

and Go will format it into the standard style:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello")
}
```

This is a major Go philosophy:

> Formatting should be automated rather than debated.

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
