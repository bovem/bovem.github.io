---
author: "Avnish"
title: "Concurrency in Go"
date: "2023-09-12"
description: "Implementing concurrent programs in Go using goroutines, channels, and WaitGroups"
tags: ["go","programming","concurrency","goroutines","select","channels","waitgroups"]
categories: ["Go"]
series: ["Go"]
aliases: ["concurrency-in-go"]
ShowToc: true
TocOpen: false
comments: false
math: true
---

We can make a program concurrent if we can split it as multiple independent tasks executed at the same time. 
 
In concurrency, we execute different portions of a program at the same time (on single or multiple CPU threads) whereas in parallelism a singular task (or subtasks) is executed parallelly on multiple CPU threads.

> Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.
> 
> <cite><a href="https://youtu.be/oV9rvDllKEg" target=_blank>Rob Pike</a>, Co-Creator of Go</cite>

# Goroutines
By adding the `go` keyword before a statement we can start a new **goroutine**. It will create a lightweight execution thread to execute the statement concurrently with the succeeding tasks in its parent function.

The program will end once the `main` goroutine is finished irrespective of the current state of other goroutines.

```Go
// Example of a non-concurrent program

package main

import (
        "fmt"
        "time"
)

func testFunc() {
        for j:=0;j<3;j++{
                fmt.Println("Hello from testfunc")
        }
        time.Sleep(2*time.Second)
}

func main() {
        testFunc()
        for i:=0;i<3;i++{
                fmt.Println("Hello from main")
                time.Sleep(2*time.Second)
        }
}

// Output
// Hello from testfunc
// Hello from testfunc
// Hello from testfunc
// Hello from main
// Hello from main
// Hello from main
```

The output of this program will be three statements from `testFunc()` functions followed by three statements from the `main()` function. 

But, if we add the `go` keyword before `testFunc()`, we can create a new *goroutine* that will execute the `testFunc()` function concurrently with the statements in `main()`.

```go
package main

import (
        "fmt"
        "time"
)

func testFunc() {
        for j:=0;j<3;j++{
                fmt.Println("Hello from testfunc")
        }
        time.Sleep(2*time.Second)
}

func main() {
        go testFunc()
        for i:=0;i<3;i++{
                fmt.Println("Hello from main")
                time.Sleep(2*time.Second)
        }
}

// Output
// Hello from main
// Hello from testfunc
// Hello from testfunc
// Hello from testfunc
// Hello from main
// Hello from main
```

# Channels
To send and receive values between Goroutines we have **channels**. 

Channels are declared using the `make(chan <datatype>)` function with the datatype of the transmitted value, for example, `make(chan string)` will create a channel that could be used to send and receive `string` values.

`chan<- string` denotes a send-only channel and `<-chan string` denotes a receive-only channel for string values. 

To iterate over the values received on a channel we can define a `for` loop with `range` like in the program below.

```Go
package main

import (
        "fmt"
        "time"
        "math/rand"
)

// Sends random integer on randomVal channel every 2 seconds
func getRandomValue(randomVal chan<- int, numValues int){
        for i:=0;i<numValues;i++{
                value := rand.Int()
                fmt.Println("getRandomValue(): Sent Random Integer", value)

                // Send a value to randomVal channel
                randomVal <- value

                // 2 seconds interval to ensure that the receiver gets enough time
                time.Sleep(time.Second*2)
        }
        close(randomVal)
}

// Listens to channel randomVal and prints received values on the terminal
func receiveRandomValue(randomVal <-chan int){

        // Loops over values received in channel
        for value := range randomVal{
                fmt.Println("recieveRandomValue(): Received Random Integer", value)
        }
}

func main() {
        randomValueChannel := make(chan int)

        go getRandomValue(randomValueChannel, 5)

        go receiveRandomValue(randomValueChannel)

        // To ensure that the main goroutine doesn't end 
        // before the other two goroutines
        time.Sleep(time.Second*10)
}

// Output
// getRandomValue(): Sent Random Integer 7680245871766720879
// recieveRandomValue(): Received Random Integer 7680245871766720879
// getRandomValue(): Sent Random Integer 1813843778283051521
// recieveRandomValue(): Received Random Integer 1813843778283051521
// getRandomValue(): Sent Random Integer 3359384099851901129
// recieveRandomValue(): Received Random Integer 3359384099851901129
// getRandomValue(): Sent Random Integer 3707965584748576540
// recieveRandomValue(): Received Random Integer 3707965584748576540
// getRandomValue(): Sent Random Integer 282243786570954167
// recieveRandomValue(): Received Random Integer 282243786570954167
```

A Go program finishes execution whenever the main goroutine is completed ignoring the current progress of other goroutines. To prevent this we can use a channel to flag completion of a goroutine.

```Go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func getRandomInt(doneFlag chan bool){
	fmt.Println("Goroutine for getRandomInt() started")
	fmt.Println("Some random integer: ", rand.Int())
	time.Sleep(time.Second*5)

	// Will send value to the channel
	// once the function is finished
	doneFlag<-true
}

func main(){
	done := make(chan bool)

	go getRandomInt(done)

	// Main Goroutine will wait to receive a value on this channel
	<-done

	fmt.Println("Main Goroutine has ended")
}

// Output
// Goroutine for getRandomInt() started
// Some random integer:  4571662314887367842
// Main Goroutine has ended
```

If we ran this program without `<-done` the main goroutine would've ended the program preemptively and its output would be

```Bash
Main Goroutine has ended
```

## Buffering
A channel won't send values unless there is another channel ready to receive those values. But **Buffered Channels** allow a certain amount of values to be sent without a corresponding receiver for those values.

```Go
package main

import "fmt"

func main() {
	exampleBufferedChan := make(chan string, 3)

	// Sending values to the buffered channel
	exampleBufferedChan<-"c"
	exampleBufferedChan<-"b"
	exampleBufferedChan<-"a"

	// Receiving values from the buffered channel
	fmt.Println("Value 1 from exampleBufferedChan:",<-exampleBufferedChan)
	fmt.Println("Value 2 from exampleBufferedChan:",<-exampleBufferedChan)
	fmt.Println("Value 3 from exampleBufferedChan:",<-exampleBufferedChan)
}

// Output
// Value 1 from exampleBufferedChan: c
// Value 2 from exampleBufferedChan: b
// Value 3 from exampleBufferedChan: a
```

The values from the channel will be received in the same order as they were sent.
# Select
The `select` keyword is used to wait for values on multiple channels simultaneously. Its syntax is similar to a <a href="/posts/go/go-programming-language/#switch-cases" target="_blank">`switch`</a>.

`select` executes the first channel to receive value in `case` statements. If multiple channels are ready with values then one of them is selected at random.

```Go
package main

import (
	"fmt"
	"time"
	"math/rand"
)

func randomValueGenerator(randomValueChannel chan<- int){
	time.Sleep(time.Second*5)
	randomValueChannel <- rand.Int()
}

func main(){
	channel1 := make(chan int, 1)
	channel2 := make(chan int, 1)

	go randomValueGenerator(channel1)
	randomValueGenerator(channel2)

	// Wait on channel1 and channel2 to receive values	
	select{
		case message1 := <- channel1:
			fmt.Println("Received value in channel1:", message1)
		case message2 := <- channel2:
			fmt.Println("Received value in channel2:", message2)
	}
}

// Output
// Received value in channel2: 1923060859381349025
```

I recommend executing this program multiple times as `channel1` and `channel2` will receive values at the same time and the output will be chosen at random by `select`.

## Timeouts using `time.After()`
The `time.After()` function returns a value after the time specified in its parameter. It could be used as a timeout `case` in `select` statements.

```Go
package main

import (
	"fmt"
	"time"
)

func sendOnChannel(doneFlag chan<- bool){
	// It will take at least 5 seconds for this function to finish
	time.Sleep(time.Second*5)
	doneFlag<-true
}

func main(){
	channel1 := make(chan bool)
	channel2 := make(chan bool)

	go sendOnChannel(channel1)
	go sendOnChannel(channel2)

	select{
	case <-channel1:
		fmt.Println("Channel 1 has responded")
	case <-channel2:
		fmt.Println("Channel 2 has responded")

	// Added a 2 seconds timeout in the select statement
	case <-time.After(time.Second*2):
		fmt.Println("You've hit a timeout")
	}
}

// Output
// You've hit a timeout
```
# Non-Blocking Channel Operations with `default`
A `default` statement is defined in `select` to avoid blocking from unbuffered channels.

```Go
package main

import (
	"fmt"
)

func main(){
	channel1 := make(chan bool)

	select{
	case <-channel1:
		fmt.Println("Something received on channel1")

	// Since there is no value received on channel1
	// the default case will be executed
	default:
		fmt.Println("This is default statement")
		fmt.Println("No value was received on channel1")
	}
}

// Output
// This is default statement
// No value was received on channel1
```
# Closing Channels
We can `close()` a channel to stop it from accepting further values.

```Go
package main

import (
	"fmt"
)

func main() {
	valueChannel := make(chan int)

	go func(){
		for i:=0;i<5;i++{
			fmt.Println("Sending",i,"to channel valueChannel")
			valueChannel <- i
		}
		
		// Closing valueChannel to stop it from receiving further values
		close(valueChannel)
	}()

	go func(){
		for{
			value, openFlag := <- valueChannel
			if !openFlag{
				fmt.Println("valueChannel is closed")
				break
			}
			fmt.Println("Received value:", value)
		}
	}()

	<-valueChannel
	fmt.Println("All values received")
	fmt.Println("Main Goroutine finished")
}

// Output
// Sending 0 to channel valueChannel
// Sending 1 to channel valueChannel
// Sending 2 to channel valueChannel
// Received value: 1
// Received value: 2
// Sending 3 to channel valueChannel
// Sending 4 to channel valueChannel
// Received value: 3
// Received value: 4
// valueChannel is closed
// All values received
// Main Goroutine finished
```

# WaitGroups
By default a Go program is finished when the `main` Goroutine is completed, We can add statements that wait for receiving over a channel like `<-done` once all goroutines are finished.

```Go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func fastRoutine(done chan bool)(int){
	// It will take at least 2 seconds to execute this function
	fmt.Println("Fast Routine started")
	time.Sleep(time.Second*2)
	done <- true
	return rand.Int()
}

func slowRoutine(done chan bool)(int){
	// It will take at least 5 seconds to execute this function
	fmt.Println("Slow Routine started")
	time.Sleep(time.Second*5)
	done <- true
	return rand.Int()
}

func main(){
	done := make(chan bool)
	done2 := make(chan bool)

	go func(){
		fastRandomVal := fastRoutine(done)
		fmt.Println("Value received from fast routine:", fastRandomVal)
	}()

	go func(){
		slowRandomVal := slowRoutine(done2)
		fmt.Println("Value received from slow routine:", slowRandomVal)
	}()

	<-done
	<-done2
}

// Output
// Slow Routine started
// Fast Routine started
// Value received from fast routine: 1556543771289280615
// Value received from slow routine: 2312691093026658070
```

But `sync.WaitGroup` provides a much more elegant way to monitor multiple Goroutines at once.

```Go
package main

import (
	"fmt"
	"sync"
	"math/rand"
	"time"
)

func fastRoutine()(int){
	fmt.Println("Fast Routine started")
	time.Sleep(time.Second*2)
	return rand.Int()
}

func slowRoutine()(int){
	fmt.Println("Slow Routine started")
	time.Sleep(time.Second*5)
	return rand.Int()
}

func main(){
	// Created a WaitGroup
	var wg sync.WaitGroup

	// Incrementing WaitGroup counter by 1
	wg.Add(1)
	go func(){
		// Decrement WaitGroup counter by 1 
		// after the goroutine is finished
		defer wg.Done()
		fastRandomVal := fastRoutine()
		fmt.Println("Value received from fast routine:", fastRandomVal)
	}()

	wg.Add(1)
	go func(){
		defer wg.Done()
		slowRandomVal := slowRoutine()
		fmt.Println("Value received from slow routine:", slowRandomVal)
	}()

	// Wait for the WaitGroup counter to reach 0
	wg.Wait()
}

// Output
// Slow Routine started
// Fast Routine started
// Value received from fast routine: 4966166931659874487
// Value received from slow routine: 439978995889306642
```

`WaitGroup` starts a counter at `0` and a delta (`int` value) is added to it using the `Add()` method. The `Done()` method decrements the counter by `1` and the `Wait()` method waits for the counter to reach `0` and moves to the next statements in the program.

<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.avni.sh/index.xml" target="_blank">RSS Feed</a>.    

# Resources
<a href="https://freecontent.manning.com/concurrency-vs-parallelism/" target="_blank">Concurrency vs Parallelism</a>  
<a href="https://youtu.be/oV9rvDllKEg" target="_blank">Concurrency is not Parallelism</a>  
<a href="https://go.dev/talks/2012/waza.slide#29" target="_blank">[Slides] Concurrency is not Parallelism</a>  
<a href="https://pkg.go.dev/github.com/eapache/channels" target="_blank">channels</a>  
<a href="https://pkg.go.dev/sync#WaitGroup" target="_blank">WaitGroup</a>  