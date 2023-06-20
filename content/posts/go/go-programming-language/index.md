---
author: "Avnish"
title: "Go Programming Language"
date: "2023-06-22"
description: "Go is used to build applications for cloud, network services, and web development."
tags: ["go","programming","pointers","procedural"]
categories: ["Go", "Cloud Native"]
series: ["Go"]
aliases: ["go-programming-language"]
ShowToc: true
TocOpen: false
comments: false
math: true
---

Go is a compiled language developed by Robert Griesemer, Rob Pike, and Ken Thompson at Google. It is primarily used in cloud-native and web development projects.

Some of the popular projects created with Go
- Docker: Container Engine
- Kubernetes: Container Orchestrator
- Hugo: Static Site Generator (Also used to generate this blog)
- <a href="/posts/kubernetes/helm-charts" target="_blank">Helm</a>: Package manager for Kubernetes

# Packages
A **Package** abstraction is created in Go to group functions that are related to each other or associated with a specific task. At the start of each source file (`*.go`) a package name is assigned to it using the `package` keyword, for example, `package main`. 

To import a package we use the `import` keyword with the name of the package enclosed in `""` like `import "fmt"`. The constants, functions, and variables imported from a package are referred to with their first letter capitalized like `math.Pi` and `math.Pow()`.

```Go
package main

import "fmt"

func main () {
	return 0
}
```

Packages could be bundled together as **modules**. The `go.mod` file declares the path to a Go module and all packages within it. A Go module is initialized using the `go mod init` command followed by the name of the module

```Bash
go mod init exampleModule
```

This will generate a `go.mod` file in the directory specifying the name of the module (`exampleModule`) and its dependencies.

# Standard Input and Output
The `fmt` package provides functions for standard input and output,  these help return output from the program and take inputs from the user respectively. The `fmt.Println()` function outputs its arguments to the terminal and `fmt.Scanln()` takes input from the terminal and assigns them to variables. Variations of `Println` and `Scanln` provided by `fmt`:

- `fmt.Print()`: Standard output without any newline at the end.
- `fmt.Scan()`: Values are read from standard input and the newline is interpreted as space.
- `fmt.Printf()`: Standard output in the specified format.
- `fmt.Scanf()`: Reads the value from the terminal in the specified format.

```Go
package main

import "fmt"

func main () {
        var name string
        fmt.Print("Your name:")
        fmt.Scan(&name)

        fmt.Println("Hello", name)
}

// Output
// Your name: Joe
// Hello Joe
```

# Building and Executing
To compile and build a Go package as a binary we use the `go build` command.

```Bash
go build hello.go
```

This will create a binary named `hello` (or `hello.exe` in Windows) in the current directory that could be executed directly by the host OS.

We can execute a Go package directly from the source file using the `go run` command.

```Bash
go run hello.go
```

# Constants
 `const` keyword is used to define constants, values that don't have to be changed throughout the program. Go supports numeric, character, string, and boolean constants.

```Go
package main

import "fmt"

func main () {
        const pi = 3.14
        fmt.Println("Approximate value of Pi is:", pi)
}

// Output
// Approximate value of Pi is: 3.14
```

# Variables and Datatypes
To initialize a variable in Go we have to use the `var` keyword followed by the name of the variable and its datatype like `var intExample int`.

Go has the following datatypes 
- `bool` for boolean values. Either `true` or `false`.
- `string` for storing single or multiple characters.
- `rune` for storing the Unicode code point of a character.
- `int` for integers. Go also supports `int8`, `int16`, `int32`, and `int64` where the digits after `int` signifies the number of bits required to represent the value.
- `uint` for unsigned integers (integers without + or -). `uint8`/`byte`, `uint16`, `uint32`, `uint64` are also available in Go.
- Variables of the `uintptr` type are used to store integer representations of memory addresses. They are useful for performing pointer arithmetic provided by `unsafe.Pointer`.
- `float32` and `float64` for values that require decimal precision.
- `complex64` and `complex128` are used to represent 64 and 128-bit complex numbers like $5\ +\ i6$ (where $i$ represents $\sqrt{(-1)}$)

```Go
package main

import "fmt"

func main () {
        var intExample int
        intExample = 3
        fmt.Println("Integer Example:", intExample)

        var floatExample float64
        floatExample = 2.3
        fmt.Println("Float Example:", floatExample)

        var stringExample string
        stringExample = "Hello, World"
        fmt.Println("String Example:", stringExample)

        var boolExample bool
        boolExample = true
        fmt.Println("Boolean Example:", boolExample)

        var complexExample complex64
        complexExample = complex(5, 6)
        fmt.Println("Complex Number Example:", complexExample)
}

// Output
// Integer Example: 3
// Float Example: 2.3
// String Example: Hello, World
// Boolean Example: true
// Complex Number Example: (5+6i)
```

To declare and assign values to a variable without explicitly specifying its datatype we use `:=` operand, the variable's datatype will be inferred during compile time by the value on the right side of the operand.

```Go
package main

import "fmt"

func main () {
        intExample := 3
        fmt.Println("Integer Example:", intExample)

        floatExample := 2.3
        fmt.Println("Float Example:", floatExample)

        stringExample := "Hello, World"
        fmt.Println("String Example:", stringExample)

        boolExample := true
        fmt.Println("Boolean Example:", boolExample)

        complexExample := complex(5, 6)
        fmt.Println("Complex Number Example:", complexExample)
}

// Output
// Integer Example: 3
// Float Example: 2.3
// String Example: Hello, World
// Boolean Example: true
// Complex Number Example: (5+6i)
```

If a variable is initialized without any value then a *zero* value will be assigned to it by default. Zero values for different datatypes are `0` (for `int`), `0.0` (for `float64`), `""` (for `string`), and `false` (for `bool`).

```Go
package main

import "fmt"

func main () {
    var example1 int
    fmt.Println(example1)
    
    var example2 float64
    fmt.Println(example2)
    
    var example3 string
    fmt.Println(example3)
    
    var example4 bool
    fmt.Println(example4)
}

// Output
// 0
// 0
// 
// false
```

# Type Conversion
Datatypes could also be used as functions for type conversion of variables for example `float64(intExample)`.

```Go
package main

import "fmt"

func main () {
        intExample := 4
        fmt.Printf("Float conversion of intExample: %0.2f", float64(intExample))
}

// Output
// Float conversion of intExample: 4.00
```

# Functions
**Functions** allow us to aggregate frequently used instructions as a single statement. To define a function in Go we use the `func` keyword followed by the name of the function, its arguments (inputs), and the datatype of the return values like `func exampleFunc (inputValue int) (outputValue int) { }`

```Go
package main

import "fmt"

func square (x int) (int) {
        return x*x
}

func sumAndDifference (x int, y int) (int, int){
        return x+y, x-y
}

func main () {
        value1 := 5
        fmt.Println("Square of", value1, "is:", square(value1))

        value2 := 8
        sum, difference := sumAndDifference(value1, value2)
        fmt.Println("Sum of", value1, "and", value2, "is:", sum)
        fmt.Println("Difference of", value2, "from", value1, "is:", difference)
}

// Output
// Square of 5 is: 25
// Sum of 5 and 8 is: 13
// Difference of 8 from 5 is: -3
```

## Anonymous Functions
Functions are first-class citizens in Go i.e. they could be defined inside variables, passed as arguments, and returned as values from other functions. 

Functions with no name (called **Anonymous functions**) could be created to perform a set of instructions once in a program.

```Go
package main

import "fmt"

func main () {
	// Anonymous Function
	func (x int) {
			fmt.Println("Hello, this is not a random number:", x)
	}(1)

	// Function as Variable
	funcExample := func (x int){
			fmt.Println("Example Function")
			fmt.Println("Input:", x)
	}

	funcExample(3)
}

// Output
// Hello, this is not a random number: 1
// Example Function
// Input: 3
```

# Conditions
Go supports `if`, `else if`,  and `else` for defining conditional statements.`&&` (AND) and `||` (OR) operators could be used to create complex conditional expressions.

```Go
package main

import "fmt"

func evaluateGrades (marks int) string {
    if marks < 100{
    	if marks < 40{
    		return "D"
    	} else if marks >= 40{
    		return "C"
    	} else if marks >= 50{
    		return "B"
    	} else if (marks >= 60){
    		return "A"
    	} 
    } else {
    		return "INVALID"
    }
    return "INVALID"
}

func main () {
	marks := 4
	fmt.Println("Grade for marks:", marks, "is:", evaluateGrades(marks))
	
	marks = 40
	fmt.Println("Grade for marks:", marks, "is:", evaluateGrades(marks))

	marks = 51
	fmt.Println("Grade for marks:", marks, "is:", evaluateGrades(marks))

	marks = 101
	fmt.Println("Grade for marks:", marks, "is:", evaluateGrades(marks))
}

// Output
// Grade for marks: 4 is: D
// Grade for marks: 40 is: C
// Grade for marks: 51 is: C
// Grade for marks: 101 is: INVALID
```

# Switch Cases
Sometimes a `switch` could be relatively clean compared to multiple `if` and `else if` statements.

Unlike `if-else` the first `case` which satisfies the condition will be executed and the following cases will be ignored. If none of the `case` conditions are satisfied then the `default` statement is executed.

```Go
package main

import "fmt"

func evaluate (value1, value2, operation int) (int) {
    switch{
        case operation==1:
            return value1+value2
        case operation==2:
            return value1-value2
        case operation==3:
            return value1*value2
        case operation==4:
            return value1/value2
        default:
            return (-1)
    }
}

func main () {
    var value1, value2, operation int
    
	fmt.Println("Calculator Program:")
	fmt.Print("Enter value1:")
	fmt.Scanln(&value1)
	fmt.Print("Enter value2:")
	fmt.Scanln(&value2)
	
	fmt.Println("\nSelect Operation to Perform")
	fmt.Println("1) ADD")
	fmt.Println("2) SUBTRACT")
	fmt.Println("3) MULTIPLY")
	fmt.Println("4) DIVIDE")
	fmt.Print("Enter the operation number:")
	fmt.Scanln(&operation)
	
	fmt.Println("Result of Operation is:", evaluate(value1, value2, operation))
}

// Output
// Calculator Program:
// Enter value1:56
// Enter value2:54
// Select Operation to Perform
// 1) ADD
// 2) SUBTRACT
// 3) MULTIPLY
// 4) DIVIDE
// Enter the operation number:3
// Result of Operation is: 3024
```

# Additional Datatypes
## Arrays
We create **arrays** to store a sequence of values with the same datatype. Go arrays have explicitly defined sizes associated with their datatype. 

Individual elements in an array could be accessed by their index (starting from `0`). For example, to access the 3rd element in the `array` we can simply call `array[2]`.

An array is defined as `array := [5]int{2, 4, 8, 16, 32}`. `len()` function returns the length of the array.

```Go
package main

import "fmt"

func main() {
    var arrayExample [5]int
    fmt.Println("Zero Value Array:", arrayExample)
    
    arrayExample[1]=2
    fmt.Println("Changed arrayExample:", arrayExample)
    
    arrayExample2 := [5]int{2, 3, 1, 3, 21}
    fmt.Println("Another arrayExample:", arrayExample2)
}

// Output
// Zero Value Array: [0 0 0 0 0]
// Changed arrayExample: [0 2 0 0 0]
// Another arrayExample: [2 3 1 3 21]
```

## Slices
A segment of an array could be extracted by creating its **slice**. To extract a segment starting from index `2` till index `5` in an array called `arrayExample` we can simply write `arrayExample[2:5]`

```Go
package main

import "fmt"

func main() {
    arrayExample2 := [7]int{2, 3, 1, 3, 21, 234, 23}
    
    lowIndex := 2
    highIndex := 5
    fmt.Println("Slice of arrayExample starting from index", lowIndex, "to", highIndex, "is:", arrayExample2[lowIndex:highIndex])
    fmt.Println("Slice of arrayExample starting from index", lowIndex, "till (including)", highIndex, "is:", arrayExample2[lowIndex:highIndex+1])
    
    fmt.Println("Slice Length:", len(arrayExample2[lowIndex:highIndex+1]))
    fmt.Println("Capacity of the original array:", cap(arrayExample2[lowIndex:highIndex+1]))
}

// Output
// Slice of arrayExample starting from index 2 to 5 is: [1 3 21]
// Slice of arrayExample starting from index 2 till (including) 5 is: [1 3 21 234]
// Slice Length: 4
// Capacity of the original array: 5
```

Any changes performed on the slice will also affect the original array. 

The `cap()` function is used to return the capacity of the original array.

The length of the slice is not part of its datatype, so slices are also used as dynamically-sized arrays. A slice is created just like an array but without any size in its declaration like `var sliceExample []int`

```Go
package main

import "fmt"

func main() {
    var capitals []string
    capitals = append(capitals, "delhi")
    capitals = append(capitals, "london")
    fmt.Println("Slice of Capitals:", capitals)
    fmt.Println("Length of Capitals Slice:", len(capitals))
    fmt.Println("Capacity of Capitals Slice:", cap(capitals))
}

// Output
// Slice of Capitals: [delhi london]
// Length of Capitals Slice: 2
// Capacity of Capitals Slice: 2
```

## Maps
To store key-value pairs in Go we have **Maps**. We can define a map that stores `string` keys mapped to `bool` values with the help of `make()` function like `mapExample := make(map[string]bool)`.

```Go
package main

import "fmt"

func main() {
    mapExample := make(map[string]bool)
    mapExample["yes"] = true
    mapExample["no"] = false
    fmt.Println("Example of Map:", mapExample)
}

// Output
// Example of Map: map[no:false yes:true]
```

## Structs
Go is a procedural language but to create data structures with multiple fields we have `struct`. By creating a `struct` we are adding a new datatype to our program, that's why the `type` keyword is used in its declaration. 

While creating an instance of the `struct` we can pass the value to its fields like maps where the key is the name of the field.

```Go
package main

import "fmt"

type dog struct {
    name string
    age int
}

func main() {
    dog1 := dog{name: "DoggyMcDogFace", age: 1}
    fmt.Println("Dog1:", dog1)

    dog2 := dog{name: "AnotherDog", age: 3}
    fmt.Println("Dog2:", dog2)
}

// Output
// Dog1: {DoggyMcDogFace 1}
// Dog2: {AnotherDog 3}
```

# Methods
**Methods** refer to the functions created to be executed on a specific datatype (like `struct`). Their declaration is similar to functions with the addition of `struct` argument after `func` keyword like `func (d dog) showFields() () { }`

```Go
package main

import "fmt"

type dog struct {
    name string
    age int
}

func (d dog) showFields() (){
    fmt.Println("Name of Dog:", d.name)
    fmt.Println("Age of Dog:", d.age)
}

func main() {
    dog1 := dog{name: "DoggyMcDogFace", age: 1}
    dog1.showFields()

    dog2 := dog{name: "AnotherDog", age: 3}
    dog2.showFields()
}

// Output
// Name of Dog: DoggyMcDogFace
// Age of Dog: 1
// Name of Dog: AnotherDog
// Age of Dog: 3
```

## Interfaces
**Interfaces** are used to group common methods. 

Interfaces enable polymorphism in Go, which means a method's behavior is determined by the datatype it is applied upon. 

```Go
package main

import "fmt"

type ubuntu struct {
    releaseName string
    releaseVersion float64
}

func (u ubuntu) showUpdateCommand() {
    fmt.Println("Update command for", u.releaseName, "is")
    fmt.Println("\tapt-get update && apt-get upgrade")
}

type fedora struct {
    releaseName string
    releaseVersion float64
}

func (f fedora) showUpdateCommand() {
    fmt.Println("Update command for", f.releaseName, "is")
    fmt.Println("\tdnf update")
}

type distro interface {
    showUpdateCommand()    
}

func main() {
    var exampleDistro, exampleDistro2 distro
    exampleDistro = ubuntu{releaseName: "Lunar Lobster", releaseVersion: 23.04}
    fmt.Println("Example of a distro:", exampleDistro)
    
    exampleDistro.showUpdateCommand()
    
    exampleDistro2 = fedora{releaseName: "Fedora 39", releaseVersion: 39.00}
    fmt.Println("Example of another distro:", exampleDistro2)
    
    exampleDistro2.showUpdateCommand()
}

// Output
// Example of a distro: {Lunar Lobster 23.04}
// Update command for Lunar Lobster is
// 	 apt-get update && apt-get upgrade
// Example of another distro: {Fedora 39 39}
// Update command for Fedora 39 is
//	  dnf update
```

# For Loops
Loops allow us to execute a statement multiple times until some condition is valid. 

A `for` loop requires three statements: 
- initializing statement: This statement will be executed once before the loop has started.
- conditional expression: This expression will be evaluated after each execution of the loop. The loop will stop if this expression evaluates to `false`.
- incrementing statement: This statement is executed after each iteration of the loop

```Go
package main

import "fmt"

func main() {
    // Initializing Statement: i:=0 (i is initialized with value 0)
    // Conditional Expression: i<10 (until i is less than 10)
    // Incrementing Statement: i++  (increment i by 1)
    for i:=0; i<10; i++ {
        fmt.Println("Value of i:", i)
    }
}

// Output
// Value of i: 0
// Value of i: 1
// Value of i: 2
// Value of i: 3
// Value of i: 4
// Value of i: 5
// Value of i: 6
// Value of i: 7
// Value of i: 8
// Value of i: 9
```

## For Loop over an Array
`len(array)` will return the length of an array, by using its length we can calculate the last index of an array i.e. `len(array)-1`. This will help us write a loop that accesses each element of an array using its index.

```Go
package main

import "fmt"

func main() {
    arrayExample := [6]int{123, 21, 3, 54, 65, 45}
    for i:=0; i<len(arrayExample); i++ {
        fmt.Println("Element at index ", i, "in arrayExample is", arrayExample[i])
    }
}

// Output
// Element at index  0 in arrayExample is 123
// Element at index  1 in arrayExample is 21
// Element at index  2 in arrayExample is 3
// Element at index  3 in arrayExample is 54
// Element at index  4 in arrayExample is 65
// Element at index  5 in arrayExample is 45
```

## Range
To iterate over data structures such as slices, maps, and strings we can take the help of `range`. Each iteration of a loop implemented with `range` will return two values depending on the data structure passed to it:

- for string, it will return the byte position of the character and character
- for array/slices, it will return the index and the element
- for maps, it will return the key and value

```Go
package main

import "fmt"

func main() {
    var sliceExample []float64
    sliceExample = append(sliceExample, 4.3)
    sliceExample = append(sliceExample, 5.2)
    sliceExample = append(sliceExample, 64.3)
    sliceExample = append(sliceExample, 34.8)
    fmt.Println("Example of a Slice:", sliceExample)
    
    for index, value := range sliceExample {
        fmt.Println("Value at index", index, "in sliceExample is", value)
    }
    
    stringExample := "Hello, World"
    fmt.Println("Example of a String:", stringExample)
    for pos, char := range stringExample {
        fmt.Printf("%#U character is at position %d\n", char, pos)
    }
    
    mapExample := make(map[int]string)
    mapExample[1] = "one"
    mapExample[2] = "two"
    mapExample[4] = "four"
    fmt.Println("Example of a Map:", mapExample)
    for key, value := range mapExample {
        fmt.Println("Value for key", key, "in mapExample is", value)
    }
}

// Output
// Example of a Slice: [4.3 5.2 64.3 34.8]
// Value at index 0 in sliceExample is 4.3
// Value at index 1 in sliceExample is 5.2
// Value at index 2 in sliceExample is 64.3
// Value at index 3 in sliceExample is 34.8
// Example of a String: Hello, World
// U+0048 'H' character is at position 0
// U+0065 'e' character is at position 1
// U+006C 'l' character is at position 2
// U+006C 'l' character is at position 3
// U+006F 'o' character is at position 4
// U+002C ',' character is at position 5
// U+0020 ' ' character is at position 6
// U+0057 'W' character is at position 7
// U+006F 'o' character is at position 8
// U+0072 'r' character is at position 9
// U+006C 'l' character is at position 10
// U+0064 'd' character is at position 11
// Example of a Map: map[1:one 2:two 4:four]
// Value for key 1 in mapExample is one
// Value for key 2 in mapExample is two
// Value for key 4 in mapExample is four
```

## Substitute for `while` Loop
Go doesn't have `while` loops but we can use `for` loops for the same purpose by executing it with just the conditional expression.

```Go
package main

import "fmt"

func main() {
    fmt.Println("Enter an odd number to end the loop")
    
    flag := false
    for flag==false {
            var input int
            fmt.Printf("Enter a value:")
            fmt.Scanln(&input)
            
            if input%2!=0{
                flag=true
            }
    }
    
    fmt.Println("Loop ended")
}

// Output
// Enter an odd number to end the loop
// Enter a value:2
// Enter a value:42
// Enter a value:52
// Enter a value:68
// Enter a value:69
// Loop ended
```

# Pointers
Pointers for a datatype will store the address of a value rather than the value itself. So an integer pointer (`*int`) will store the address of an integer value.

To fetch the address of a value we use `&` operand. For example, to fetch the address of the value stored in the `intExample` variable we call `&intExample`. Similarly to fetch the value stored at an address we use `*` operand like `*(&intExample)`.

```Go
package main

import "fmt"

func main() {
    intExample := 2
    fmt.Println("Value of intExample:", intExample)
    fmt.Println("Memory address of intExample:", &intExample)
    
    var pointerExample *int
    pointerExample = &intExample
    fmt.Println("Value of pointerExample:", pointerExample)
    fmt.Println("Value stored at pointerExample address:", *pointerExample)
}

// Output
// Value of intExample: 2
// Memory address of intExample: 0xc000022050
// Value of pointerExample: 0xc000022050
// Value stored at pointerExample address: 2
```

When methods are defined on the pointer of the `struct` rather than the `struct` themselves then they can manipulate the value of fields in the original instance rather than its copy.

```Go
package main

import "fmt"

type car struct {
    color string
}

func (c car) changeColor(color string) {
    c.color = color
}

func (c *car) changeColorPointer(color string) {
    c.color = color
}

func main() {
    newCar := car{color: "red"}
    fmt.Println("newCar: ", newCar)
    
    // This will not work as the change is not performed 
    // on the original memory address but on its copy
    newCar.changeColor("blue")
    fmt.Println("newCar after changing color: ", newCar)
    
    var newCarPtr *car
    newCarPtr = &newCar
    newCarPtr.changeColorPointer("blue")
    fmt.Println("newCar after changing color using pointer: ", newCar)
}

// Output
// newCar:  {red}
// newCar after changing color:  {red}
// newCar after changing color using pointer:  {blue}
```

# Errors
In Go, errors are like other return types. So a function can return two values one being the output of the function and the other value could be an `error` interface. We can declare this in the function's signature like `func sampleFunc(value int) (int, error) { }`.

A custom error is created using `errors.New()` function is available in `errors` package.

```Go
package main

import (
    "fmt"
    "errors"
)

func divide(value1, value2 int) (int, error) {
    if value2==0 {
        return -1, errors.New("Divide by Zero Error. Change value2")
    } 
    return value1/value2, nil
}

func main() {
    var value1, value2 int
    fmt.Printf("Enter value1:")
    fmt.Scanln(&value1)
    fmt.Printf("Enter value2:")
    fmt.Scanln(&value2)
    
    quotient, err := divide(value1, value2)
    fmt.Println("Quotient:", quotient)
    if err!= nil {
        fmt.Println("Error:", err)
    }
}

// Output
// Enter value1:1
// Enter value2:0
// ERROR!
// Quotient: -1
// Error: Divide by Zero Error. Change value2
```

<iframe src="https://bovem.substack.com/embed" width="100%" height="320" style="border:2px solid #EEE; background:white;" frameborder="0" scrolling="no"></iframe>

# Resources
<a href="https://gobyexample.com/" target="_blank">Go by Example</a>  
<a href="https://www.youtube.com/watch?v=C8LgvuEBraI" target="_blank">Learn Go in 12 Minutes</a>