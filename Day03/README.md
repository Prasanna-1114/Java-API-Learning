# Day 03 — HTTP Methods in Depth

## Introduction

HTTP methods tell the server what operation the client wants to perform.

The main HTTP methods are:

* GET
* POST
* PUT
* PATCH
* DELETE

These methods are commonly used to perform CRUD operations.

---

## CRUD

CRUD means:

```text
C = Create
R = Read
U = Update
D = Delete
```

CRUD and HTTP methods usually map like this:

| CRUD Operation | HTTP Method |
| -------------- | ----------- |
| Create         | POST        |
| Read           | GET         |
| Update         | PUT / PATCH |
| Delete         | DELETE      |

---

# GET

GET is used to retrieve data from the server.

Example:

```http
GET /students
```

This means:

> Get all students.

To get one student:

```http
GET /students/5
```

This means:

> Get the student whose ID is 5.

Example response:

```json
{
  "id": 5,
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

GET requests should normally only read data and should not modify data.

---

## GET in Spring Boot

```java
@GetMapping("/students")
public String getStudents() {
    return "All students";
}
```

When the client sends:

```http
GET /students
```

Spring Boot executes:

```java
getStudents()
```

---

# POST

POST is commonly used to create new data.

Example:

```http
POST /students
```

Request body:

```json
{
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

The server may create a new student and return:

```json
{
  "id": 10,
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

---

## Request Body

The request body contains data sent by the client to the server.

Example:

```json
{
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

In Spring Boot, we commonly use:

```java
@RequestBody
```

to receive this data.

Example:

```java
@PostMapping("/students")
public String createStudent(@RequestBody Student student) {
    return "Student created";
}
```

Spring Boot converts the JSON request body into a Java object.

Flow:

```text
JSON
 ↓
@RequestBody
 ↓
Java Object
```

---

# PUT

PUT is generally used to fully update or replace an existing resource.

Suppose the existing student is:

```json
{
  "id": 5,
  "name": "Rahul",
  "email": "rahul@gmail.com",
  "city": "Hyderabad"
}
```

Request:

```http
PUT /students/5
```

Body:

```json
{
  "name": "Rahul Kumar",
  "email": "rahulk@gmail.com",
  "city": "Chennai"
}
```

The complete student information is updated.

---

## PUT in Spring Boot

```java
@PutMapping("/students/{id}")
public String updateStudent(
        @PathVariable int id,
        @RequestBody Student student) {

    return "Student updated: " + id;
}
```

---

# PATCH

PATCH is used to partially update a resource.

Suppose we have:

```json
{
  "id": 5,
  "name": "Rahul",
  "email": "rahul@gmail.com",
  "city": "Hyderabad"
}
```

If we only want to change the city:

```http
PATCH /students/5
```

Body:

```json
{
  "city": "Bangalore"
}
```

Only the city is changed.

The remaining fields stay the same.

---

# PUT vs PATCH

The main difference is:

```text
PUT   → full update/replacement
PATCH → partial update
```

Example:

PUT:

```json
{
  "name": "Rahul",
  "email": "rahul123@gmail.com",
  "city": "Hyderabad"
}
```

PATCH:

```json
{
  "email": "rahul123@gmail.com"
}
```

---

# DELETE

DELETE is used to remove a resource.

Example:

```http
DELETE /students/5
```

This means:

> Delete student whose ID is 5.

Spring Boot example:

```java
@DeleteMapping("/students/{id}")
public String deleteStudent(@PathVariable int id) {
    return "Student deleted: " + id;
}
```

---

# Path Variable

A path variable is a value taken directly from the URL.

Example:

```http
GET /students/10
```

Here:

```text
10
```

is the student ID.

Spring Boot:

```java
@GetMapping("/students/{id}")
public String getStudent(@PathVariable int id) {
    return "Student ID = " + id;
}
```

Request:

```http
GET /students/10
```

Then:

```text
id = 10
```

---

# REST-Style Endpoint Design

A good REST API normally uses resource names in URLs.

Good:

```http
GET /students
POST /students
PUT /students/5
DELETE /students/5
```

Avoid URLs like:

```text
/getStudents
/createStudent
/deleteStudent
```

The HTTP method already tells us the action.

Example:

```http
DELETE /students/5
```

already tells us:

> Delete student 5.

So we do not need:

```text
/deleteStudent/5
```

---

# Complete Student API

A student API may contain:

```http
GET    /students
GET    /students/5
POST   /students
PUT    /students/5
PATCH  /students/5
DELETE /students/5
```

Meaning:

```text
GET /students
→ Get all students

GET /students/5
→ Get student 5

POST /students
→ Create a student

PUT /students/5
→ Fully update student 5

PATCH /students/5
→ Partially update student 5

DELETE /students/5
→ Delete student 5
```

---

# Idempotency

Idempotency means:

> Repeating the same request multiple times should have the same intended final result as sending it once.

Example:

```http
DELETE /students/5
```

After student 5 is deleted, repeating the same request does not delete other students.

Final state remains:

```text
Student 5 does not exist.
```

Common behavior:

| HTTP Method | Usually Idempotent |
| ----------- | ------------------ |
| GET         | Yes                |
| PUT         | Yes                |
| DELETE      | Yes                |
| POST        | No                 |
| PATCH       | Not guaranteed     |

POST is usually not idempotent because repeating:

```http
POST /orders
```

could create multiple orders.

---

# Real-World Example — Products

Get all products:

```http
GET /products
```

Get one product:

```http
GET /products/10
```

Create a product:

```http
POST /products
```

Fully update a product:

```http
PUT /products/10
```

Update only part of a product:

```http
PATCH /products/10
```

Delete a product:

```http
DELETE /products/10
```

---

# Spring Boot HTTP Method Annotations

Spring Boot provides annotations for HTTP methods.

```java
@GetMapping
@PostMapping
@PutMapping
@PatchMapping
@DeleteMapping
```

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping
    public String getStudents() {
        return "All students";
    }

    @GetMapping("/{id}")
    public String getStudent(@PathVariable int id) {
        return "Student " + id;
    }

    @PostMapping
    public String createStudent() {
        return "Student created";
    }

    @PutMapping("/{id}")
    public String updateStudent(@PathVariable int id) {
        return "Student updated " + id;
    }

    @PatchMapping("/{id}")
    public String partiallyUpdateStudent(@PathVariable int id) {
        return "Student partially updated " + id;
    }

    @DeleteMapping("/{id}")
    public String deleteStudent(@PathVariable int id) {
        return "Student deleted " + id;
    }
}
```

---

# Request Flow

Example:

```http
DELETE /students/8
```

Flow:

```text
Postman
   ↓
HTTP DELETE request
   ↓
Spring Boot
   ↓
@DeleteMapping("/{id}")
   ↓
id = 8
   ↓
Java method
   ↓
Service
   ↓
Repository
   ↓
Database
```

---

# Practice — Book API

Get all books:

```http
GET /books
```

Get one book:

```http
GET /books/1
```

Create a book:

```http
POST /books
```

Fully update a book:

```http
PUT /books/1
```

Partially update a book:

```http
PATCH /books/1
```

Delete a book:

```http
DELETE /books/1
```

---

# Day 03 Summary

Today I learned:

* GET
* POST
* PUT
* PATCH
* DELETE
* CRUD mapping
* Request body
* `@RequestBody`
* Path variables
* `@PathVariable`
* PUT vs PATCH
* REST-style endpoint design
* Idempotency basics
* Spring Boot HTTP method annotations
* Real-world CRUD API patterns
