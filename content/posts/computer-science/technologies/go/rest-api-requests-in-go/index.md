---
author: "Avnish"
title: "REST API Requests in Go"
date: "2023-09-15"
description: "Performing HTTP requests on REST APIs with Go"
tags: ["go","programming","rest","api","http","get","post","put","delete"]
categories: ["Go"]
series: ["Go"]
aliases: ["rest-api-requests-in-go"]
ShowToc: true
TocOpen: false
comments: false
math: true
---

# What is REST?
REST (REpresentational State Transfer) is an architectural style for designing and developing APIs that could be used for client and server interactions.

REST defines 6 architectural constraints for APIs:
* Uniform interface: API requests for a resource should look the same irrespective of its origin client. The Uniform Resource Identifier (URI) allocated to resources should be unique.
* Client-server: Changes on the client side should not affect the server and vice-versa.
* Stateless: The API request should contain all the resources required to process it. The server should not store any details related to client requests.
* Cacheable: API Responses could be cached on the client or server side.
* Layered system: Neither the client nor the server should have information regarding the number of intermediate layers during communication.
* Code on demand (optional): When required, code snippets sent through API responses should be executable on demand.

APIs that comply with REST architecture constraints are called **RESTful APIs**.
# REST Requests
A REST request comprises of:
* HTTP Verb: Specifies the type of operation to be performed on resource/s like creation, fetch, updation, and deletion.
* Header: Contains the information about the request like its datatype (`text/html`, `text/plain`, `application/json`, etc.), credentials, proxy server address, etc.
* Path: Path to the resource (URI)
* Body: Input data for the operation.
# HTTP Verbs
`GET`, `POST`, `PUT`, and `DELETE` are common HTTP verbs available in RESTful APIs. 

**Note**: To follow this tutorial you need to head over to <a href="https://crudcrud.com/" target="_blank">crudcrud.com</a> to get a new API endpoint.

The `net/http` Go package provides all the necessary utilities for implementing REST clients and servers.

## `POST`
A `POST` request is performed to create resources.

If we have to perform a `POST` request on the API endpoint provided by crudcrud.com we have to pass the resource (to be created) as a <a href="/posts/go/file-handling-in-go/#encoding-json" target="_blank">JSON payload</a> in the request's body.

```Go
package main

import (
   "fmt"
   "net/http"
   "encoding/json"
   "bytes"
   "io/ioutil"
)

func main() {
	// Replace with the API ID provided by crudcrud.com
	apiID := "XXXXXX"

	// HTTP endpoint with path of resource (vehicles)
	createResourceURL := fmt.Sprintf("https://crudcrud.com/api/%s/vehicles",
									apiID)

	// JSON payload containing resource information
	requestBody, _ := json.Marshal(map[string]string{
		"color": "White",
		"license": "ABC123",
		"numWheels": "4",
		"type": "Car",
	})

	// Converting request body into bytes buffer
	requestBodyBytes := bytes.NewBuffer(requestBody)

	// Creating a POST request on createResourceURL
	request, err := http.NewRequest("POST", createResourceURL, requestBodyBytes)
	if err != nil {
		panic(err)
	}

	// Adding application/json as payload type 
	request.Header.Add("Content-Type", "application/json")

	client := &http.Client{}

	// Performing POST request
	response, err := client.Do(request)
	if err != nil {
		panic(err)
	}

	defer response.Body.Close()

	// Reading the response to the request
	responseBody, err := ioutil.ReadAll(response.Body)
	if err != nil {
		fmt.Println("Error received in response:",err)
	}

	responseBodyString := string(responseBody)
	fmt.Println(responseBodyString)
}

// Output
// {"color":"White","license":"ABC123","numWheels":"4","type":"Car",
// "_id":"6501480bb987ad03e8769e4}
```

To check the resource on the server side you can click on the "Check Endpoint Information" button on the crudcrud.com page. A new REST resource named `vehicles` would have been created.

## `GET`

To fetch all `vehicles` resources created by the `POST` request we will perform a `GET` request on the same API endpoint.

```Go
package main

import (
   "fmt"
   "net/http"
   "io/ioutil"
   "encoding/json"
)

type Vehicle struct{
	Id          string `json:"_id"`
	Color       string
	License     string
	NumWheels   string
	VehicleType string
}

func main() {
	// Replace this with the API ID provided by crudcrud.com
	apiID := "XXXXXX"

	// HTTP endpoint with the path of the resource (vehicles)
	fetchResourceURL := fmt.Sprintf("https://crudcrud.com/api/%s/vehicles", 
									apiID)

	// Performing GET request
	response, err := http.Get(fetchResourceURL)
	if err != nil {
		panic(err)
	}

	defer response.Body.Close()

	// Reading the response to the request
	responseBody, err := ioutil.ReadAll(response.Body)
	if err != nil {
		fmt.Println("Error received in response:",err)
	}

	// Marshalling the response of API into a list of type Vehicle
	var vehicles []Vehicle
	json.Unmarshal(responseBody, &vehicles)
	
	for _, veh := range vehicles{
		fmt.Println("Vehicle Resource ID:", veh.Id)
		fmt.Println("Color:", veh.Color)
		fmt.Println("License:", veh.License)
		fmt.Println("NumWheels:", veh.NumWheels)
		fmt.Println("Type:", veh.VehicleType)
		fmt.Println()
	}

}

// Output
// Vehicle Resource ID: 65014c0ab987ad03e8769e5c
// Color: Black
// License: XYZ435
// NumWheels: 4
// Type: Car
//
// Vehicle Resource ID: 65014c1cb987ad03e8769e5d
// Color: White
// License: ABC123
// NumWheels: 4
// Type: Car
//
// Vehicle Resource ID: 65014c59b987ad03e8769e5e
// Color: Red
// License: IJK546
// NumWheels: 2
// Type: Motorcycle
```

We can also fetch specific resources by specifying their `_id` in URI.

```Go
package main

import (
   "fmt"
   "net/http"
   "io/ioutil"
   "encoding/json"
)

type Vehicle struct{
	Id          string `json:"_id"`
	Color       string
	License     string
	NumWheels   string
	VehicleType string
}

func main() {
	// Replace this API ID with the one provided by crudcrud.com
	apiID := "XXXXXX"

	// Replace this with the ID of the resource you want to fetch
	vehicleResourceID := "65014c59b987ad03e8769e5e"

	// HTTP endpoint with the path of the resource (vehicles)
	fetchResourceURL := fmt.Sprintf("https://crudcrud.com/api/%s/vehicles/%s",	
							apiID, vehicleResourceID)

	// Performing GET request
	response, err := http.Get(fetchResourceURL)
	if err != nil {
		panic(err)
	}

	defer response.Body.Close()

	// Reading the response to the request
	responseBody, err := ioutil.ReadAll(response.Body)
	if err != nil {
		fmt.Println("Error received in response:",err)
	}

	// Unmarshalling the response of API into a list of type Vehicle
	var veh Vehicle
	json.Unmarshal(responseBody, &veh)

	fmt.Println("Vehicle Resource ID:", veh.Id)
	fmt.Println("Color:", veh.Color)
	fmt.Println("License:", veh.License)
	fmt.Println("NumWheels:", veh.NumWheels)
	fmt.Println("Type:", veh.VehicleType)
}

// Output
// Vehicle Resource ID: 65014c59b987ad03e8769e5e
// Color: Red
// License: IJK546
// NumWheels: 2
// Type: Motorcycle
```

## `PUT`
To update a resource we perform a `PUT` request.

```Go
package main

import (
   "fmt"
   "net/http"
   "encoding/json"
   "bytes"
   "io/ioutil"
)

func main() {
	// Replace this API ID with the one provided by crudcrud.com
	apiID := "XXXXXX"

	vehicleResourceID := "65014c0ab987ad03e8769e5c"

	// HTTP endpoint with path (vehicle) of the resource
	updateResourceURL := fmt.Sprintf("https://crudcrud.com/api/%s/vehicles/%s",	
							apiID, vehicleResourceID)

	// JSON payload as bytes
	requestBody, _ := json.Marshal(map[string]string{
		"color": "Black",
		"license": "XYZ435",
		"numWheels": "2",
		"vehicleType": "Motorcycle",
	})

	// Converting request body into bytes buffer
	requestBodyBytes := bytes.NewBuffer(requestBody)

	// Creating a PUT request on updateResourceURL
	request, err := http.NewRequest("PUT", updateResourceURL, requestBodyBytes)
	if err != nil {
		panic(err)
	}

	// Adding application/json as payload type 
	request.Header.Add("Content-Type", "application/json")

	client := &http.Client{}

	// Performing PUT request
	response, err := client.Do(request)
	if err != nil {
		panic(err)
	}

	defer response.Body.Close()

	// Verifying changes by fetching the details of the resource
	// Performing GET request
	responseGet, err := http.Get(updateResourceURL)
	if err != nil {
		panic(err)
	}

	defer responseGet.Body.Close()

	// Reading the response to the request
	responseBody, err := ioutil.ReadAll(responseGet.Body)
	if err != nil {
		fmt.Println("Error received in response:",err)
	}

	fmt.Println("Updated Resource:",string(responseBody))
}

// Output
// Updated Resource: {"_id":"65014c0ab987ad03e8769e5c","color":"Black",
// "license":"XYZ435","numWheels":"2","vehicleType":"Motorcycle"}
```

## `DELETE`
The `DELETE` verb is used for resource deletion requests.

```Go
package main

import (
   "fmt"
   "net/http"
   "io/ioutil"
)

func main() {
	// Replace this API ID with the one provided by crudcrud.com
	apiID := "XXXXXX"

	vehicleResourceID := "65014c0ab987ad03e8769e5c"

	// HTTP endpoint with path (vehicle) of the resource
	deleteResourceURL := fmt.Sprintf("https://crudcrud.com/api/%s/vehicles/%s", 
							apiID, vehicleResourceID)

	// Creating a DELETE request on deleteResourceURL
	request, err := http.NewRequest("DELETE", deleteResourceURL, nil)
	if err != nil {
		panic(err)
	}

	// Adding application/json as payload type 
	request.Header.Add("Content-Type", "application/json")

	client := &http.Client{}

	// Performing DELETE request
	response, err := client.Do(request)
	if err != nil {
		panic(err)
	}

	defer response.Body.Close()

	// Performing GET request
	responseGet, err := http.Get(deleteResourceURL)
	if err != nil {
		panic(err)
	}

	defer responseGet.Body.Close()

	// Reading the response to the request
	responseBody, err := ioutil.ReadAll(responseGet.Body)
	if err != nil {
		fmt.Println("Error received in response:",err)
	}

	fmt.Println("Deleted Resource:",string(responseBody))
}

// Output
// Deleted Resource: {"type":"https://tools.ietf.org/html/rfc7231#section-6.5.4",
// "title":"Not Found","status":404,"traceId":"0HMTFDGO4V3QF:00000001"}
```

<hr>
Thank you for taking the time to read this blog post! If you found this content valuable and would like to stay updated with my latest posts consider subscribing to my <a href="https://www.avni.sh/index.xml" target="_blank">RSS Feed</a>.    

# Resources
<a href="https://restfulapi.net/" target="_blank">REST API Tutorial</a>  
<a href="https://www.codecademy.com/article/what-is-rest" target="_blank">What is REST?</a>  
<a href="https://restfulapi.net/rest-architectural-constraints/" target="_blank">REST Architectural Constraints</a>  
<a href="https://pkg.go.dev/net/http" target="_blank">`net/http` Go Package</a>  
<a href="https://pkg.go.dev/net/http#pkg-constants" target="_blank">HTTP Verbs in `net/http` package</a>  