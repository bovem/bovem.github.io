---
author: "Avnish"
title: "File Handling in Go"
date: "2023-06-27"
description: "The os package in Go provides data structures, functions, and methods for managing files and directories"
tags: ["go","programming","json","xml","yaml"]
categories: ["Go"]
series: ["Go"]
aliases: ["file-handling-in-go"]
ShowToc: true
TocOpen: false
comments: false
math: true
---

Reading and writing files is one of the most common tasks in programming projects. Using this skill we can store data persistently and decrease the memory consumption of our program.

# Paths
The `path/filepath` package provides `filepath.Join()` function to create filesystem paths for files and directories. The same could also be achieved with string concatenation in Go but it won't be interoperable between different operating systems (Linux uses the forward slash (`/`) for separators in the path whereas Windows uses the backward slash (`\`)).

Other functions provided by the `path/filepath` package:
* `filepath.Dir()`: Returns the directory from the path (excluding filename).
* `filepath.Base()`: Returns the filename from the path (excluding parent directories).
* `filepath.Ext()`: Returns the extension of the file.
* `filepath.IsAbs()`: Returns `true` if the `filepath` is an absolute path otherwise `false`.
* `filepath.Rel()`: Returns relative path between its two argument paths.

```Go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	exampleFile := filepath.Join("/app", "file_handling.go")
	fmt.Println("Example File Path:", exampleFile)
	fmt.Println("Filename:", filepath.Base(exampleFile))
	fmt.Println("Directory:", filepath.Dir(exampleFile))
	fmt.Println("Extension in Filename:", filepath.Ext(exampleFile))
	fmt.Println("Is exampleFile path absolute?:", filepath.IsAbs(exampleFile))

	value, err := filepath.Rel("/tmp", exampleFile)
	if err!=nil {
		panic(err)
	} else {
		fmt.Println("Relative path between /tmp and exampleFile will be:", 
				value)
	}
}

// Output
// Example File Path: /app/file_handling.go
// Filename: file_handling.go
// Directory: /app
// Extension in Filename: .go
// Is exampleFile path absolute?: true
// Relative path between /tmp and exampleFile will be: ../app/file_handling.go
```

# `defer` Keyword
When a file is opened by a program its contents are loaded on the system memory. That's why it is crucial to close the file and release the occupied memory. This will also prevent data corruption on the file.

Any statement in Go prefixed with `defer` keyword will be executed at the end of the parent function. These statements are generally used for cleanup tasks.

```Go
package main

import "fmt"

func testFunc() {
	defer fmt.Println("This will be executed after testFunc() has ended")
	fmt.Println("This is the last statement in testFunc()")
}

func main() {
	defer fmt.Println("This will be executed after main() has ended")
	testFunc()
	fmt.Println("Last statement in main()")
}

// Output
// This is the last statement in testFunc()
// This will be executed after testFunc() has ended
// Last statement in main()
// This will be executed after main() has ended
```

We can define the functions for closing files & perform other resource cleanup, then call them with `defer` keyword to ensure they are executed after our program has finished. The statements will be executed in the reverse order of their declaration.

# Managing Directories
The `os` package in Go provides functions analogous to common Linux/Windows commands used for managing directories.

- `os.Mkdir()`: Similar to the `mkdir` Linux command, it creates the directory given the filepath in string and the octal notation of directory permissions (like `0755`).
- `os.MkdirAll()`: Creates the complete hierarchy of directories similar to the `mkdir` command executed with `--parents` or `-p` flag.
- `os.RemoveAll()`: Removes directory (including subfolders and files).
- `os.Getwd()`: Returns the present working directory similar to the `pwd` command.
- `os.Chdir()`: Changes present working directory. Same as the `cd` command.
- `os.ReadDir()`: `ls` command in Linux lists the contents of the current directory, `os.ReadDir()` will do the same for the directory path passed as the argument. A `for` loop has to be defined on the slice returned by the function to access each file/directory with its name.

```Go
package main

import (
	"fmt"
	"os"
)

func removeDir(dirPath string) {
	os.RemoveAll(dirPath)
	fmt.Println(dirPath, "removed")
}

func getPWD() {
	pwd, err := os.Getwd()
	if err!=nil {
		panic(err)
	}
	fmt.Println("Present Working Directory is:", pwd)
}

func main() {
	fmt.Println("Creating temp directory")
	os.Mkdir("temp", 0755)

	fmt.Println("Creating temp2/temp3/testDir directory hierarchy")
	os.MkdirAll("temp2/temp3/testDir", 0755)
	os.Mkdir("temp2/temp4", 0755)
	os.Mkdir("temp2/temp5", 0755)

	// This will be executed after the main() function is finished
	defer removeDir("temp")
	defer removeDir("temp2")

	val, err := os.ReadDir("temp2")
	if err!=nil {
		panic(err)
	} else {
		fmt.Println("Contents of temp2 directory:")
		for _, file := range val {
			fmt.Print("\t",file.Name())
		}
		fmt.Print("\n")
	}
	
	getPWD()

	fmt.Println("Moving one level up")
	os.Chdir("../")
	getPWD()

	fmt.Println("Moving back to /app directory")
	os.Chdir("/app")
	getPWD()
}

// Output
// Creating temp directory
// Creating temp2/temp3/testDir directory hierarchy
// Contents of temp2 directory:
//         temp3   temp4   temp5
// Present Working Directory is: /app
// Moving one level up
// Present Working Directory is: /
// Moving back to /app directory
// Present Working Directory is: /app
// temp2 removed
// temp removed
```

# Reading from a File
The `os.ReadFile()` function will load the entire content of a file into the system memory. If we want to save memory or read specific portions of a file we can use `os.Open()` function. It returns an instance of  `os.File` struct that could be used for navigating the file in read-only mode.

The `os.Seek()` method will offset all read and write operations to a specific location in an `os.File` instance. For example `exampleFile.Seek(10, 0)` will increase the offset by 10 bytes. Hence, all the read and write operations performed on the `exampleFile` will skip the first 10 bytes. The second argument in `os.Seek()` signifies the origin relative to the offset i.e.

- `exampleFile.Seek(10, 0)`: 10 bytes from the start of the file
- `exampleFile.Seek(10, 1)`: 10 bytes from the current offset location
- `exampleFile.Seek(10, 2)`: 10 bytes from the end of the file

`exampleFile.Seek(0, 0)` will reset the offset to 0 (start of the file).

The `os.Read()` method is used to read the contents of a file in a byte slice. It returns the number of bytes read as a value. In the following example, we will perform read operations on `example.txt`.

```bash
$ cat example.txt
Hello, World
42 is the meaning of life, the universe, and everything
```

```Go
package main

import (
	"fmt"
	"os"
)

func main() {
	fmt.Println("Reading the complete example.txt")
	exampleFile, err := os.ReadFile("example.txt")
	if err!=nil {
		panic(err)
	} else {
		fmt.Println("Content from example.txt:")
		// Converting the byte slice returned by ReadFile to string
		fmt.Println(string(exampleFile))
		fmt.Println()
	}

	exampleFile2, err := os.Open("example.txt")
	if err!=nil {
		panic(err)
	}

	defer exampleFile2.Close()

	// Offsetting operations 13 bytes from the start
	exampleFile2.Seek(13, 0)

	byteArr := make([]byte, 17)
	numBytes, err := exampleFile2.Read(byteArr)
	if err!=nil {
		panic(err)
	}
	fmt.Println("Bytes read from exampleFile2:", numBytes)
	fmt.Printf("%v\n", string(byteArr[:numBytes]))
}

// Output
// Reading the complete example.txt
// Content from example.txt:
// Hello, World
// 42 is the meaning of life, the universe, and everything
// 
// Bytes read from exampleFile2: 17
// 42 is the meaning
```

# Writing to a File
The `os.Create()` function will create a file or directory from the string path passed as its argument.

Similar to `os.ReadFile()`, the `os.WriteFile()` function will write the contents of a byte slice to the given file. It will also create the file if it doesn't exist. To perform more granular writes we have `os.Write()` method.

To open a file for read-write operations we have to use `os.OpenFile()` instead of `os.Open()`, with `os.O_RDWR` as its argument. The changes written to the file by the program will not reflect on the system storage until the `os.Sync()` method is called.

```Go
package main

import (
	"fmt"
	"os"
)

func readFile(filepath string) {
	fmt.Println("Reading the contents of", filepath, ":")
	byteSlice, err := os.ReadFile(filepath)
	if err!=nil {
		panic(err)
	}
	fmt.Println(string(byteSlice))
}

func main() {
	fmt.Println("Writing to hello.txt")
	byteSlice := []byte("Hello from Go program\n")
	err := os.WriteFile("hello.txt", byteSlice, 0755)
	if err!=nil {
		panic(err)
	}
	fmt.Println("Write successful")

	defer os.Remove("hello.txt")

	readFile("hello.txt")

	helloFile, err := os.OpenFile("hello.txt", os.O_RDWR, 0755)
	if err!=nil {
		panic(err)
	}

	defer helloFile.Close()

	// Moving offset to the end of file
	_, err = helloFile.Seek(0, 2)
	if err!=nil {
		panic(err)
	}

	byteSliceWrite := "Hey from WriteFile"
	_, err = helloFile.WriteString(byteSliceWrite)
	if err!=nil {
		panic(err)
	}

	readFile("hello.txt")

	// Syncing changes between memory and storage
	helloFile.Sync()
}

// Output
// Writing to hello.txt
// Write successful
// Reading the contents of hello.txt :
// Hello from Go program
//
// Reading the contents of hello.txt :
// Hello from Go program
// Hey from WriteFile
```

# Handling Common Filetypes
JSON, YAML, and XML are one of the most common file types for configuration, test results, and other structured data storage.

## JSON
### Encoding JSON
Encoding is the process of converting data into a specific format. To encode data into JSON format we have to use the `encoding/json` package.

The `json.Marshal()` function returns the JSON encoding for the struct instance passed as its argument. The fields in struct need to have their first character capitalized. 

The Marshal-ed JSON could be written to file with the help of `os.OpenFile()` and `os.Write()` functions.

```Go
package main

import (
	"fmt"
	"encoding/json"
	"os"
)

type Vehicle struct {
	Name string
	Color string
	NumWheels int
}

func main() {
	v := Vehicle{Name: "Nissan GTR", Color: "Grey", NumWheels: 4}
	fmt.Println("Vehicle instance:", v)

	jsonData, err := json.Marshal(v)
	if err!=nil{
		panic(err)
	}
	fmt.Println("Vehicle JSON:\n", string(jsonData))

	file, err := os.OpenFile("vehicle.json", os.O_CREATE|os.O_RDWR, 0755)
	if err!=nil{
		panic(err)
	}
	
	defer file.Close()

	file.Write(jsonData)
	file.Sync()
}

// Output
// Vehicle instance: {Nissan GTR Grey 4}
// Vehicle JSON:
//  {"Name":"Nissan GTR","Color":"Grey","NumWheels":4}
```

```Bash
$ cat vehicle.json 
{"Name":"Nissan GTR","Color":"Grey","NumWheels":4}
```

### Decoding JSON
Decoding is the reverse of encoding, we take data from a specified format and convert it to its original form. The `json` Go package provides `json.Unmarshal()` function for decoding JSON into a struct.

```Go
package main

import (
	"fmt"
	"encoding/json"
	"os"
)

type Vehicle struct {
	Name string
	Color string
	NumWheels int
}

func main() {
	var v Vehicle

	jsonFile, err := os.ReadFile("vehicle.json")
	if err!=nil {
		panic(err)
	}

	defer jsonFile.Close()

	err = json.Unmarshal(jsonFile, &v)
	if err!=nil {
		panic(err)
	}
	fmt.Println("Struct Instance from JSON:", v)
}

// Output
// Struct Instance from JSON: {Nissan GTR Grey 4}
```

## YAML
Similar to the `json` package, `gopkg.in/yaml` also provides `yaml.Marshal()` and `yaml.Unmarshal()` functions for encoding and decoding data into YAML respectively.

Before using the `gopkg.in/yaml` package we have to install it with the `go get` command.
```bash
go get gopkg.in/yaml.v2
```

```Go
package main

import (
	"fmt"
	"gopkg.in/yaml.v2"
	"os"
)

type VehicleUses struct {
	F1SafetyCar bool
}

type Vehicle struct {
	Name string
	Color string
	NumWheels int
	Uses VehicleUses
}

func main() {
	// Writing an instance of Vehicle to vehicle.yaml
	v := Vehicle{
	Name:"Aston Martin Vantage", 
	Color: "green", 
	NumWheels:4, 
	Uses:VehicleUses{F1SafetyCar: true}
	}

	fmt.Println("Vehicle Instance:", v)

	yamlData, err := yaml.Marshal(&v)
	if err!=nil {
		panic(err)
	}
	fmt.Println("Vehicle in YAML format:")
	fmt.Println(string(yamlData))

	yamlFile, err := os.OpenFile("vehicle.yaml", os.O_CREATE|os.O_RDWR, 0755)
	if err!=nil{
		panic(err)
	}

	defer yamlFile.Close()
	defer os.Remove("vehicle.yaml")

	_, err = yamlFile.Write(yamlData)
	if err!=nil{
		panic(err)
	}

	// Creating an instance of Vehicle from vehicle.yaml
	var v2 Vehicle
	yamlFile2, err := os.ReadFile("vehicle.yaml")
	if err!=nil{
		panic(err)
	}

	err = yaml.Unmarshal(yamlFile2, &v2)
	if err!=nil{
		panic(err)
	}
	fmt.Println("Instance of Vehicle from vehicle.yaml:")
	fmt.Println(v2)
}

// Output
// Vehicle Instance: {Aston Martin Vantage green 4 {true}}
// Vehicle in YAML format:
// name: Aston Martin Vantage
// color: green
// numwheels: 4
// uses:
//   f1safetycar: true
//
// Instance of Vehicle from vehicle.yaml:
// {Aston Martin Vantage green 4 {true}}
```

## XML
The XML format is commonly used to store test results and configuration. The `encoding/xml` Go package provides `xml.Marshal()` and `xml.Unmarshal()` functions.

If we want the data in a prettified format (with indents on each level) then we can use the `xml.MarshalIndent()` function. `MarshalIndent()` is also present in `json` and `gopkg.in/yaml` packages.

```Go
package main

import (
	"fmt"
	"encoding/xml"
	"os"
)

type Student struct{
	Name string
	IdNum int
	Subjects []string
}

func main() {
	// Storing an instance of Student in XML format
	student1 := Student{
	Name: "John Doe", 
	IdNum: 98, 
	Subjects:[]string{"English", "Maths", "Science"}
	}

	fmt.Println("Example of a Student instance:", student1)

	xmlFile, err := os.OpenFile("student.xml", os.O_CREATE|os.O_RDWR, 0755)
	if err!=nil {
		panic(err)
	}

	defer xmlFile.Close()
	defer os.Remove("student.xml")

	xmlData, err := xml.MarshalIndent(student1, " ", "	")
	if err!=nil {
		panic(err)
	}

	fmt.Println("Student data in XML format:")
	fmt.Println(string(xmlData))

	_, err = xmlFile.Write(xmlData)
	if err!=nil {
		panic(err)
	}

	// Reading an instance of Student from XML File
	var student2 Student
	xmlFile2, err := os.ReadFile("student.xml")
	if err!=nil {
		panic(err)
	}

	err = xml.Unmarshal(xmlFile2, &student2)
	fmt.Println("Student from student.xml:")
	fmt.Println(student2)
}

// Output
// Example of a Student instance: {John Doe 98 [English Maths Science]}
// Student data in XML format:
//  <Student>
//         <Name>John Doe</Name>
//         <IdNum>98</IdNum>
//         <Subjects>English</Subjects>
//         <Subjects>Maths</Subjects>
//         <Subjects>Science</Subjects>
//  </Student>
// Student from student.xml:
// {John Doe 98 [English Maths Science]}
```

<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.bovem.in/index.xml" target="_blank">RSS Feed</a> or newsletter.   

&nbsp;

<iframe src="https://bovem.substack.com/embed" width="100%" height="320" style="border:2px solid #EEE; background:white;" frameborder="0" scrolling="no"></iframe>    

# Resources
<a href="https://gobyexample.com/" target="_blank">Go by Example</a>  
<a href="https://go.dev/blog/json" target="_blank">JSON and Go</a>  
<a href="https://pkg.go.dev/encoding/json" target="_blank">encoding/json</a>  
<a href="https://github.com/go-yaml/yaml" target="_blank">go-yaml/yaml</a>  
<a href="https://pkg.go.dev/gopkg.in/yaml.v2" target="_blank">gopkg.in/yaml.v2</a>  
<a href="https://pkg.go.dev/encoding/xml" target="_blank">encoding/xml</a>  