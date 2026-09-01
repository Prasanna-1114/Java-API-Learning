# Day 01 — API Fundamentals

## What is an API?

API stands for:

**Application Programming Interface**

An API allows two software applications to communicate with each other.

A simple flow is:

```text
Client
  |
  | Request
  ↓
API / Server
  |
  ↓
Database
```

The server gets or processes the required information and sends a response back to the client.

---

## Client

A client is the application that sends a request.

Examples:

* Browser
* React application
* Mobile application
* Postman
* Another backend service

Example:

```text
Client → GET /students
```

---

## Server

The server receives a request, performs some work, and sends a response.

In this learning journey, Spring Boot will act as the server.

Example:

```text
Client
   ↓
Spring Boot Server
```

---

## Request

A request is something the client asks the server to do.

Examples:

```text
Get all users
Create a user
Update a user
Delete a user
```

Example HTTP request:

```http
GET /users
```

This means:

> Give me the users.

---

## Response

A response is the information returned by the server to the client.

Example:

```json
[
  {
    "id": 1,
    "name": "Rahul"
  },
  {
    "id": 2,
    "name": "Priya"
  }
]
```

---

## Request and Response Flow

```text
Client
   |
   | Request
   ↓
Server
   |
   | Business Logic
   ↓
Database

Database
   |
   ↓
Server
   |
   | Response
   ↓
Client
```

---

## What is an Endpoint?

An endpoint is a specific URL or address used to access an API resource or operation.

Examples:

```text
/users
/products
/orders
/students
```

Example:

```http
GET /students
```

This usually means:

> Get all students.

---

## CRUD

CRUD represents four common data operations.

```text
C = Create
R = Read
U = Update
D = Delete
```

These commonly map to HTTP methods:

| Operation | HTTP Method |
| --------- | ----------- |
| Create    | POST        |
| Read      | GET         |
| Update    | PUT / PATCH |
| Delete    | DELETE      |

---

## What is JSON?

JSON stands for:

**JavaScript Object Notation**

JSON is a lightweight data format commonly used to exchange structured data between applications.

Example:

```json
{
  "name": "Prasanna",
  "age": 23,
  "isStudent": true
}
```

JSON can contain:

* Strings
* Numbers
* Booleans
* Arrays
* Objects
* null

JSON is used by many technologies including:

* Java
* JavaScript
* Python
* C#
* Go
* PHP

---

## Why Doesn't React Directly Access MySQL?

A frontend application should normally not directly connect to a database.

Bad architecture:

```text
React
   ↓
MySQL
```

This could expose database credentials and create serious security problems.

Better architecture:

```text
React
   ↓
Spring Boot API
   ↓
MySQL
```

The backend can control:

* Authentication
* Authorization
* Validation
* Business logic
* Database operations
* Security

---

## API vs REST API

API is the general concept of software systems communicating.

REST is one particular architectural style used to build APIs.

Not every API is a REST API.

Other API technologies include:

* REST
* GraphQL
* SOAP
* gRPC
* WebSockets

---

## Example

Client sends:

```http
GET /users
```

Spring Boot receives the request.

Later we will write Java code similar to:

```java
@RestController
public class UserController {

    @GetMapping("/users")
    public String getUsers() {
        return "Users";
    }
}
```

Flow:

```text
GET /users
     ↓
@GetMapping("/users")
     ↓
getUsers()
     ↓
Response
```

---

## Day 01 Summary

Today I learned:

* What an API is
* Client and server
* Request and response
* API endpoints
* CRUD
* JSON
* Basic API architecture
* Why the frontend should communicate with the database through a backend API
* API vs REST API
