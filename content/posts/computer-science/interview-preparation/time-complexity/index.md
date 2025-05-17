---
author: "Avnish"
title: "Time Complexity"
date: "2023-09-22"
description: "The time complexity metric is used to assess an algorithm's performance on scale"
tags: ["data-structures","algorithms","time-complexity","go"]
categories: ["Algorithms"]
series: ["Data Structures and Algorithms"]
aliases: ["time-complexity"]
ShowToc: true
TocOpen: false
comments: false
cover:
  image: "Time Complexity.drawio.png"
  linkFullImages: true
  alt: "Time Complexity Comparison of Algorithms"
  caption: ""
  relative: false
  responsiveImages: false
math: true
---

While programming allows us to create virtually anything, the true test of performance arises when we deploy the same code on a significantly larger scale. 

**Time Complexity** ($T(n)$) is a function that estimates the execution time of an algorithm given the amount of data to be processed as its input ($n$). It is a common benchmark used to measure an algorithm's performance.

# Bounds of Time Complexity
The output of the time complexity function will be a close estimate of an algorithm's runtime given the size of its input but it does not consider other characteristics of the input that could affect its runtime.

Some algorithms perform best when the input data is sorted in ascending order and worst when sorted in descending order or vice versa. That's why we have bounds on the time complexity function, a range starting from best-case to worst-case execution time for the same input size.

## Upper Bound ($O$)
The "big O" ($O$) represents the upper bound (worst-case scenario) on the time complexity function i.e. for an input of size $n$ the algorithm can't take more than $O(n)$ time to provide  the solution.

## Lower Bound ($\Omega$)
The "big Omega" ($\Omega$) represents the lower bound (best-case scenario) on the time complexity function i.e. for an input of size $n$ the algorithm can't take less than $O(n)$ time to provide the solution.

## Expected Case ($\Theta$)
The "big Theta" ($\Theta$) represents the case where both upper and lower bounds are at the same point (expected case scenario) i.e. for an input of size $n$ the algorithm's time complexity couldn't get better or worse than $\Theta(n)$.

Big $O$ is the preferred time complexity function for an algorithm's runtime analysis because it provides a conservative estimate and its result is independent of factors like hardware performance, characteristics of data, compiler optimization, etc.

While choosing among different algorithms to perform a task we aim for the lowest worst-case time complexity.

# Common Time Complexity Functions
The runtime of recurring patterns in programming could be represented by common time complexity functions. This helps us estimate the time complexity of the entire program.

## Constant Time Complexity $O(1)$
<p align="center"><img src="Constant.drawio.png" alt="Scaling an Algorithm with Constant Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Constant Time Complexity</small></p>

An algorithm has **constant time complexity** when its runtime isn't affected by the amount of data passed as input. An example would be a function that performs addition on its two inputs.

```Go
package main

import "fmt"

func addition(x int, y int) (int){
	// The size of x and y does not affect
	// the runtime of this function

	return x+y
}

func main(){
	x := 2000
	y := 2132
	fmt.Println("Addition of", x, "and", y, "is:", addition(x, y)) 
}

// Result
// Addition of 2000 and 2132 is: 4132
```

The above example is implemented in the <a href="/posts/go/go-programming-language/" target="_blank">Go Programming Language</a>.

## Linear Time Complexity $O(n)$
<p align="center"><img src="Linear.drawio.png" alt="Scaling an Algorithm with Linear Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Linear Time Complexity</small></p>

For some algorithms the execution time is directly proportional to the size of its input, such algorithms are categorized in **linear time complexity**.

An example would be a loop that iterates over elements in a list and returns its sum.

```Go
package main

import "fmt"

func arraySum(arr []int)(int){
	sum := 0

	// Time taken to complete this loop
	// will be directly proportional to the
	// size of arr
	for _, element := range arr{
		sum += element
	}

	return sum
}

func main(){
	arrayExample := []int{1, 2, 3, 2, 1, 1}
	fmt.Println("Sum of the array:", arrayExample, "will be", 
                arraySum(arrayExample))
}

// Output
// Sum of the array: [1 2 3 2 1 1] will be 10
```

If we call the function `arraySum()` twice then the time complexity of the program will be $O(2n)$. However, we can generalize it to $O(n)$ because even though the program performs two passes over the array, the growth rate in runtime remains linear with respect to its input size. 

## Quadratic Time Complexity $O(n^2)$
<p align="center"><img src="Quadratic.drawio.png" alt="Scaling an Algorithm with Quadratic Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Quadratic Time Complexity</small></p>

Similar to linear time complexity, algorithms exhibiting quadratic time complexity experience execution times that are directly proportional to the square of the number of inputs. These algorithms scale relatively slower (longer execution time) compared to linear time complexity algorithms like $O(n)$, $O(2n)$, etc.

For example, a program that displays pair combinations of all elements in an array using nested loops.

```Go
package main

import "fmt"

func showCombinations(arr []int){

	// The inner loop is executed n (size of arr) times.
	// Thus, the total time complexity of this function will be O(n*n)
	for _, element1 := range arr{

		// Time complexity of the inner loop
		// is directly proportional to the size of arr i.e. O(n)
		for _, element2 := range arr{

			if(element1 != element2){
				fmt.Println("Combination:", element1, "and", 
                                            element2)
			}

		}

	}
}

func main(){
	arrayExample := []int{123, 1234, 456, 5462}
	fmt.Println("Pair combination of all elements in the array: ", 
                arrayExample, "are:")
	showCombinations(arrayExample)
}

// Output
// Pair combination of all elements in the array:  [123 1234 456 5462] are:
// Combination: 123 and 1234
// Combination: 123 and 456
// Combination: 123 and 5462
// Combination: 1234 and 123
// Combination: 1234 and 456
// Combination: 1234 and 5462
// Combination: 456 and 123
// Combination: 456 and 1234
// Combination: 456 and 5462
// Combination: 5462 and 123
// Combination: 5462 and 1234
// Combination: 5462 and 456
```

## Exponential Time Complexity $O(2^n)$
<p align="center"><img src="Exponential.drawio.png" alt="Scaling an Algorithm with Exponential Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Exponential Time Complexity</small></p>

A brute-force algorithm to find the $n$th number in the Fibonacci series has exponential time complexity because it branches in two recursive calls on every iteration.

```Go
package main

import "fmt"

func fibonacci(n int) (int){
	if n <= 1 {
		return n
	}
	
	// The recursive calls will branch
	// further in two more calls
	return fibonacci(n-1) + fibonacci(n-2)
}

func main() {
	n := 10 
	fmt.Println("The", n, "th Fibonacci number is:", fibonacci(n))
}

// Output
// The 10 th Fibonacci number is: 55
```

Algorithms with $O(2^n)$, $O(e^n)$, $O(10^n)$, etc. time complexities are grouped under **exponential** time. Among these, the $2^n$ function has widespread use in computer science like Moore's law or to find the number of memory addresses possible with $n$ bits arrangement.

> The number of transistors in an Integrated Circuit (IC) doubles about every two years
> 
> <cite><a href="https://en.wikipedia.org/wiki/Gordon_Moore" target=_blank>Gordon Moore</a>, Co-Founder of Intel, 1965</cite>

## Logarithmic Time Complexity $O(\log_2{n})$
<p align="center"><img src="Logarithmic.drawio.png" alt="Scaling an Algorithm with Logarithmic Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Logarithmic Time Complexity</small></p>

The $\log_2{n}$ is the inverse of function $2^n$. The following example of a number guessing game has $O(\log_2{n})$ time complexity because it cuts the search space by half on each iteration.

```Go
package main

import "fmt"

func guessNumber(low int, high int)(int){
	// Returns the middle number in a range from low to high
	mid := (high+low)/2
	return mid
}

func main(){
	low := 0
	high := 100
	fmt.Println("Think of a number between", low, "and", high)
	numQuestions := 0

	for {
            var answer1, answer2 int
            guessedNumber := guessNumber(low, high)

            fmt.Println("I guessed:", guessedNumber, "Is that correct?")
            fmt.Println("1) Yes")
            fmt.Println("2) No")
            fmt.Print("Enter your response (1 or 2):")
            fmt.Scan(&answer1)

            switch(answer1){
                case 1:
                    fmt.Println("It took me", 
                                numQuestions, 
                                "questions to guess your number")

                    // It will take maximum log(n) questions to guess a number
                    // Where n is size of the number range in this case 100

                    fmt.Println("Thanks for playing")
                    return
                
                case 2:
                    numQuestions += 1
                    fmt.Println("Is it higher or lower than", guessedNumber)
                    fmt.Println("1) Higher")
                    fmt.Println("2) Lower")
                    fmt.Print("Enter your response (1 or 2):")
                    fmt.Scan(&answer2)

                    switch(answer2){
                        case 1:
                            // Halving the search space 
                            // to exclude number lower than guessed
                            low = guessedNumber
                        case 2:
                            // Halving the search space 
                            // to exclude number higher than guessed
                            high = guessedNumber
                    }
            }
	}
}

// Output
// Think of a number between 1 and 100
// I guessed: 50 Is that correct?
// 1) Yes
// 2) No
// Enter your response (1 or 2):2
// Is it higher or lower than 50
// 1) Higher
// 2) Lower
// Enter your response (1 or 2):2
// I guessed: 24 Is that correct?
// 1) Yes
// 2) No
// Enter your response (1 or 2):2
// Is it higher or lower than 24
// 1) Higher
// 2) Lower
// Enter your response (1 or 2):1
// I guessed: 37 Is that correct?
// 1) Yes
// 2) No
// Enter your response (1 or 2):1
// It took me 2 questions to guess your number
// Thanks for playing
```

## Linearithmic Time Complexity $O(n \log_2 n)$
<p align="center"><img src="Linearithmic.drawio.png" alt="Scaling an Algorithm with Linearithmic Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Linearithmic Time Complexity</small></p>

<a href="/posts/dsa/merge-sort/" target="_blank">Merge Sort</a>, <a href="/posts/dsa/quick-sort/" target="_blank">Quick Sort</a>, and <a href="/posts/dsa/heap-sort/" target="_blank">Heap Sort</a> are some examples of algorithms with **linearithmic time complexity**.

## Factorial Time Complexity $O(n!)$
<p align="center"><img src="Factorial.drawio.png" alt="Scaling an Algorithm with Factorial Time Complexity"></p>
<p align="center"><small>Scaling an Algorithm with Factorial Time Complexity</small></p>

The solution to the <a href="/posts/dsa/travelling-salesman-problem/" target="_blank">Travelling Salesman Problem</a> has factorial time complexity.

# Comparing Algorithm Performance Using Time Complexity 
Let's say we've been provided with a set of algorithms and we have to choose the one which scales best with respect to the growing input size. 

Assuming the output of the function $O()$ is an algorithm's worst-case execution time in seconds. As we increase the input size from $1$ to $100$ the runtime of algorithms will scale as follows:

| Algorithm    | Time Complexity | Runtime ($n=2$) | Runtime ($n=10$) | Runtime ($n=100$)       |
|--------------|-----------------|-----------------|------------------|-------------------------|
| Logarithmic  | $O(log_2{n})$   | $1$s            | $3.321$s         | $6.644$s                |
| Linear       | $O(n)$          | $2$s            | $10$s            | $100$s                  |
| Linearithmic | $O(nlog_2{n})$  | $2$s            | $33.21$s         | $664.4$s                |
| Quadratic    | $O(n^2)$        | $4$s            | $100$s           | $10000$s                |
| Exponential  | $O(2^n)$        | $4$s            | $1024$s          | $1.26 \times 10^{30}$s  |
| Factorial    | $O(n!)$         | $2$s            | $3628800$s       | $9.33 \times 10^{157}$s |

With the same amount of computational power and input size ($100$), an algorithm with $O(2^n)$ time complexity will finish the task in $3.99 \times 10^{22}$ years. In contrast, an algorithm with $O(n \log_2{n})$ time complexity will take only $664.4$ seconds. This illustrates the importance of performing a time complexity analysis of all possible solutions while solving a problem.

If we plot the worst-case time complexity $O(n)$ against the input size $n$ we can see that algorithms with logarithmic or linear time complexity scale better relative to algorithms with quadratic or exponential time complexity i.e. their runtime grows relatively slow as the input size increases.

<p align="center"><img src="Time Complexity.drawio.png" alt="Time Complexity Comparison of Algorithms"></p>
<p align="center"><small>Time Complexity Comparison of Algorithms</small></p>

<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.avni.sh/index.xml" target="_blank">RSS Feed</a>.    

# Resources
<a href="https://www.freecodecamp.org/news/big-theta-and-asymptotic-notation-explained/" target="_blank">Big Theta and Asymptotic Notation Explained</a>  
<a href="http://watson.latech.edu/book/future/futureMoores1.html" target="_blank">What is Moore's Law</a>  
<a href="https://www.khanacademy.org/math/algebra2/x2ec2f6f830c9fb89:logs/x2ec2f6f830c9fb89:log-intro/v/plotting-exponential-logarithm" target="_blank">Relationship between exponentials & logarithms</a>  
<a href="https://www.khanacademy.org/computing/computer-science/algorithms/recursive-algorithms/a/the-factorial-function" target="_blank">The factorial function</a>  
