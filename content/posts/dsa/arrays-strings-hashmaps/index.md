---
author: "Avnish"
title: "Arrays, Strings, and HashMaps"
date: "2023-09-29"
description: "Understanding basic data structures like arrays, strings, and hashmaps"
tags: ["data-structures", "arrays", "arraylists", "nested-arrays", "matrices", "hashmaps", "hashtables", "strings", "string-builders", "hashmap-collision", "go"]
categories: ["Data Structures"]
series: ["Data Structures and Algorithms"]
aliases: ["arrays-strings-hashmaps"]
ShowToc: true
TocOpen: false
comments: false
cover:
  image: "arrays-strings-hashtables.png"
  linkFullImages: true
  alt: "Arrays, Strings, and HashMaps"
  caption: ""
  relative: false
  responsiveImages: false
math: true
---

Data structures like arrays, strings, and hashmaps are available by default in most programming languages. They facilitate storage of large amounts of data in an efficient format which makes it easier (and sometimes relatively faster) to access during runtime.

# Arrays
<p align="center"><img src="arrays.png" alt="Array"></p>

An array is a sequential store of data (referred to as _elements_).

In languages like Python, an array can store elements with multiple data types, like, `[1, "Hello, World", True]` but for languages like Go, C++, and Java an array can store elements of a singular data type.

## Iterating over an array using indexes
An **index** is assigned to each element based on its location in the array. The value stored at the `index` could be extracted using `[ ]` brackets with the array's variable name, for example, `arrayExample[index]`.

We can iterate over values stored in an array by defining a loop starting from `0` (index of the first element in the <a href="/posts/go/go-programming-language/" target="_blank">Go Programming Language</a>) till `len(arrayExample)-1` (index of last element).

```Go
package main

import "fmt"

func main(){
  arrayExample := []int{12, 3, 2, 1, 3123, 78}
  for i:=0;i<len(arrayExample);i++{
    fmt.Println("Element at index", i, "is:", arrayExample[i])
  }
}

// Output
// Element at index 0 is: 12
// Element at index 1 is: 3
// Element at index 2 is: 2
// Element at index 3 is: 1
// Element at index 4 is: 3123
// Element at index 5 is: 78
```

The <a href="/posts/dsa/time-complexity/" target="_blank">time complexity</a> of this loop is $O(n)$ where $n$ is the size of `arrayExample`.

## Nested Arrays
A simple array will store data in just one dimension but we can nest arrays to store multidimensional data like _matrices_.

```Go
nestedArrayExample := [][]int{
                        {1, 123, 123, 234},
                        {456, 345, 3457, 5},
                        }
```

To iterate over the elements in a nested array we have to define multiple nested loops.
The time complexity of fetching all the elements from a nested array will be $O(n^m)$ where $n$ is the size of individual arrays and $m$ is the order of nesting (to iterate over all the elements we have to define $m$ nested loops each of length $n$). 

```Go
package main

import "fmt"

func main(){
  nestedArrayExample := [][]int{
    {1, 123, 123, 234},
    {456, 345, 3457, 5},
  }

  for i:=0;i<len(nestedArrayExample);i++{
    fmt.Println("Nested Array at index:", i)
    for j:=0;j<len(nestedArrayExample[i]);j++{
      fmt.Printf("Element at index[%d][%d]: %d \n", i, j, nestedArrayExample[i][j])
    }
  }
}

// Output
// Nested Array at index: 0
// Element at index[0][0]: 1 
// Element at index[0][1]: 123 
// Element at index[0][2]: 123 
// Element at index[0][3]: 234 
// Nested Array at index: 1
// Element at index[1][0]: 456 
// Element at index[1][1]: 345 
// Element at index[1][2]: 3457 
// Element at index[1][3]: 5
```

In the example above an array is nested inside another array ($m=2$), to traverse it we have to define a loop nested inside another loop. Thus, the time complexity of the program is $O(n^2)$.

## ArrayList
<p align="center"><img src="arraylist.png" alt="ArrayList"></p>

**ArrayList** data structure is similar to an array but it doubles in size when it runs out of space. It is ideal for scenarios with limited memory space for program execution.

```Go
package main

import "fmt"

type ArrayList struct{
  capacity int
  array []int 
}

func (al *ArrayList) insert(x int){
  if(al.capacity==0){
    // If ArrayList is at full capacity
    // then create a new temporary ArrayList
    // twice the size of the existing one

    fmt.Println("Resizing ArrayList")
    var newArray []int

    // Copy all the elements from existing
    // ArrayList to the new ArrayList
	// This will take O(n) time
	// where n is the size of al.array
    for i:=0;i<len(al.array);i++ {
      newArray = append(newArray, al.array[i])
    }

    // Insert the element in the new ArrayList
    newArray = append(newArray, x)

    // Increase the capacity of ArrayList
    al.capacity = len(al.array)-1

    // Replace the old ArrayList with a new one
    al.array = newArray

  } else{

    // Insert the element into the existing ArrayList
    al.array = append(al.array, x)

    // Decrement the capacity by 1
    al.capacity -= 1
  }
}

func main(){
  al := ArrayList{capacity:1, array: []int{}} 
  al.insert(12)
  al.insert(3)
  al.insert(2)
  al.insert(1)
  al.insert(3123)
  fmt.Println("ArrayList after all elements are inserted:", al.array)
}

// Output
// Resizing ArrayList
// Resizing ArrayList
// Resizing ArrayList
// ArrayList after all elements are inserted: [12 3 2 1 3123]
```

The time complexity of inserting a new element in ArrayList is assumed to be $O(1)$, but if the list is already at max capacity then the time complexity will be $O(n)$ where $n$ is the number of existing elements.

# Strings

A string is like an array of characters. It is defined as a separate datatype, but most array operations like loop, slice, and indexing are interoperable.

```Go
package main

import "fmt"

func main(){
  stringExample := "Hello, World"
  fmt.Println("First character is:", stringExample[0])
  fmt.Println("Last character is:", stringExample[len(stringExample)-1])
  fmt.Println("Slice of string:", stringExample[2:4])

  for i:=0;i<len(stringExample);i++{
    fmt.Printf("Character at index: %d is: %c \n", i, stringExample[i])
  }
}

// Output
// First character is: H
// Last character is: d
// Slice of string: ll
// Character at index: 0 is: H 
// Character at index: 1 is: e 
// Character at index: 2 is: l 
// Character at index: 3 is: l 
// Character at index: 4 is: o 
// Character at index: 5 is: , 
// Character at index: 6 is:   
// Character at index: 7 is: W 
// Character at index: 8 is: o 
// Character at index: 9 is: r 
// Character at index: 10 is: l 
// Character at index: 11 is: d
```

## Concatenation of Strings
<p align="center"><img src="strings.png" alt="String Concatenation"></p>

A program that concatenates two strings of size $m$ and $n$, has time complexity $O(m + n)$ or $O(2n)$ if both strings are of equal length.

```Go
package main

import (
  "fmt"
  "strings"
)

func concat(stringA string, stringB string)(string){

  // Create a new list of strings
  // to store the concatenated string
  var newString []string

  // Copy stringA
  for i:=0;i<len(stringA);i++{
      newString = append(newString, string(stringA[i]))
  }

  // Copy stringB
  for j:=0;j<len(stringB);j++{
      newString = append(newString, string(stringB[j]))
  }

  // Join the list of strings to create a new string
  return strings.Join(newString, "")
}

func main(){
  stringA := "Hello"
  stringB := "World"
  fmt.Printf("Concatenation of %s and %s is: %s \n", stringA, stringB, 
                concat(stringA, stringB))
}

// Output
// Concatenation of Hello and World is: HelloWorld 
```

## StringBuilder
Using **StringBuilder** (a dynamically sized array similar to ArrayList) we can reduce the time complexity of operations performed on a string.

Upon initialization, the StringBuilder will load the characters of a string into an array. If we have to concatenate another string of length $n$, the array will be resized with time complexity $O(n)$. Once all operations are performed, the array in StringBuilder can be converted back to a string.

```Go
package main

import (
  "fmt"
  "strings"
)

type StringBuilder struct{
  array []string
}

func (sb *StringBuilder) load(value string){
  // We assume the time complexity of this function
  // to be O(1)

  sb.array = strings.Split(value, "")
}

func (sb *StringBuilder) concat(value string){
  // Time complexity of concatenating a string of
  // length m will be O(m)

  for i:=0;i<len(value);i++{
    sb.array = append(sb.array, string(value[i]))
  }
}

func (sb *StringBuilder) unload()(string){
  // Just like load() method the time complexity of
  // unloading an array to string will be assumed as O(1)

  return strings.Join(sb.array, "")
}

func main(){
  sb := StringBuilder{array: []string{}}
  sb.load("Hello")
  fmt.Println("Instance of StringBuilder:", sb)

  sb.concat("World")
  fmt.Println("StringBuilder array after concatenation:", sb.array)

  fmt.Println("Unloaded String:", sb.unload())
}

// Output
// Instance of StringBuilder: {[H e l l o]}
// StringBuilder array after concatenation: [H e l l o W o r l d]
// Unloaded String: HelloWorld
```

# HashMaps
<p align="center"><img src="hashtables.png" alt="HashMap"></p>

A **HashMap** is a data structure that stores values mapped to keys. It is generally used to store values that require frequent access, as the time complexity of accessing/searching an element in HashMap is $O(1)$.

The <a href="/posts/go/go-programming-language/#maps" target="_blank">Map</a> in Go, is an implementation of the HashMap data structure.

## Hash Function
While inserting a value in HashMap, a **Hash Function** calculates a hash from the input value and allocates a location to store the value.

A HashMap collision occurs when two values have the same hash or they are allocated the same storage location. In the case of collision, multiple values could be stored in the same location using an array or <a href="/posts/dsa/linked-lists" target="_blank">LinkedList</a>.

An ideal hash function will produce the least collisions, resulting in $O(1)$ lookup time. But in the worst-case scenario (all values are inserted in the same location) the lookup time will increase to $O(n)$, same as an array.

```Go
package main

import "fmt"

type HashMap struct{
  table map[int][]int
}

func calculateHash(value int)(int){
  return value%10
}

func (hm *HashMap) insert(insertValue int){
  // Calculate hash value from insertValue
  hash := calculateHash(insertValue)

  // Append the insertValue to the array 
  // present at "hash" key
  hm.table[hash] = append(hm.table[hash], insertValue)
}

func (hm *HashMap) search(searchValue int)(bool){
  
  // Narrow down the search to the array stored 
  // at the "hash" key
  searchSpace := hm.table[calculateHash(searchValue)]

  // Search for searchValue in the array stored 
  // at "hash" key
  for i:=0;i<len(searchSpace);i++{
    if (searchValue == searchSpace[i]){
      return true
    }
  }

  return false
}

func main(){
  hm := HashMap{table: make(map[int][]int)}
  hm.insert(1)
  hm.insert(12)
  hm.insert(23)
  hm.insert(65)
  hm.insert(67)
  hm.insert(55)

  fmt.Println("HashMap:", hm.table)

  searchValue1 := 55
  fmt.Println("Searching for", searchValue1, "in HashMap:",
                hm.search(searchValue1))

  searchValue2 := 2
  fmt.Println("Searching for", searchValue2, "in HashMap:",
                hm.search(searchValue2))
}

// Output
// HashMap: map[1:[1] 2:[12] 3:[23] 5:[65 55] 7:[67]]
// Searching for 55 in HashMap: true
// Searching for 2 in HashMap: false
```

<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.avni.sh/index.xml" target="_blank">RSS Feed</a>.    

# Resources
<a href="https://www.freecodecamp.org/news/hash-tables/" target="_blank">Hash Table Explained: What it Is and How to Implement It</a>  
<a href="https://www.freecodecamp.org/news/java-list-tutorial-util-list-api-example/" target="_blank">Java List Methods Tutorial – Util List API Example</a>  
<a href="https://www.freecodecamp.org/news/efficient-string-building-in-javascript/" target="_blank">How to Work with Strings in JavaScript – Tips for Efficient String Concatenation</a>  
