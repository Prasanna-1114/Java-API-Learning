# Day 05 — REST API Architecture and Principles

## What is REST?

REST stands for:

**Representational State Transfer**

REST is a style used to design web APIs.

A REST API usually works with:

* Resources
* URLs
* HTTP methods
* Status codes
* JSON
* Stateless communication

Example:

```http
GET /students
```

means:

> Get all students.

```http
POST /students
```

means:

> Create a student.

```http
DELETE /students/5
```

means:

> Delete student 5.

---

## API vs REST API

An API is a general way for software applications to communicate.

A REST API is an API designed by following REST principles.

So:

```text
API
→ General concept
```

```text
REST API
→ API following REST-style design
```

Not every API is REST.

Other API technologies include:

* SOAP
* GraphQL
* gRPC

---

## What is a Resource?

A resource is a thing or data item that an API works with.

Examples:

```text
Student
User
Product
Order
Book
Employee
Payment
Ticket
```

For example, an e-commerce API may have resources like:

```text
/products
/users
/orders
/payments
```

---

## Resource URLs

A REST API usually organizes URLs around resources.

For students:

```http
GET /students
```

Get all students.

```http
GET /students/5
```

Get student 5.

```http
POST /students
```

Create a student.

```http
PUT /students/5
```

Update student 5.

```http
PATCH /students/5
```

Partially update student 5.

```http
DELETE /students/5
```

Delete student 5.

---

## Avoid Verbs in REST URLs

Avoid URLs like:

```text
/getStudents
/createStudent
/updateStudent
/deleteStudent
```

Better:

```http
GET /students
POST /students
PUT /students/5
DELETE /students/5
```

The HTTP method already tells us the action.

For example:

```http
DELETE /students/5
```

already means:

> Delete student 5.

So there is no need for:

```text
/deleteStudent/5
```

---

## Use Plural Resource Names

A common REST convention is to use plural resource names.

Preferred:

```text
/users
/products
/students
/orders
```

Instead of:

```text
/user
/product
/student
/order
```

Example:

```http
GET /students
```

means:

> Get the student collection.

```http
GET /students/5
```

means:

> Get one student from that collection.

---

## CRUD in REST

CRUD means:

```text
C = Create
R = Read
U = Update
D = Delete
```

REST CRUD mapping:

| Operation      | HTTP Method | Example           |
| -------------- | ----------- | ----------------- |
| Create         | POST        | `POST /books`     |
| Read           | GET         | `GET /books`      |
| Read One       | GET         | `GET /books/5`    |
| Update         | PUT         | `PUT /books/5`    |
| Partial Update | PATCH       | `PATCH /books/5`  |
| Delete         | DELETE      | `DELETE /books/5` |

---

## What is Statelessness?

Statelessness means:

> Every request should contain the information needed for the server to process it.

The server should not depend on hidden information from a previous request.

Example:

```http
GET /profile
Authorization: Bearer abc123
```

The request contains the authentication information needed by the server.

The next request should also contain the required information.

Example:

```text
Request 1
→ Contains required information

Request 2
→ Contains required information

Request 3
→ Contains required information
```

---

## Does Stateless Mean No Database?

No.

Statelessness does not mean the server cannot store data.

The server can still store:

```text
Users
Orders
Products
Payments
Bookings
```

in a database.

Statelessness mainly means the server should not depend on conversational client state from an earlier request to understand a later request.

---

## Client-Server Separation

In a REST architecture, the client and server have separate responsibilities.

Example:

```text
React
   ↓
Spring Boot REST API
   ↓
MySQL
```

React mainly handles:

* User interface
* User interaction
* Displaying data

Spring Boot mainly handles:

* Business logic
* Validation
* Security
* Database operations
* API responses

The frontend should not directly access the database.

Correct:

```text
React
   ↓
REST API
   ↓
Spring Boot
   ↓
MySQL
```

Avoid:

```text
React
   ↓
MySQL
```

---

## Why Can Multiple Clients Use the Same API?

Because the client and backend are separated.

The same Spring Boot API can be used by:

```text
React Website
Mobile Application
Admin Dashboard
Another Backend Service
```

For example:

```http
GET /products
```

can be used by all of these clients.

---

## Representation of a Resource

Suppose Java has a Student object with:

```text
id
name
email
```

The API may send it as JSON:

```json
{
  "id": 1,
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

This JSON is a representation of the Student resource.

REST APIs commonly use JSON to send resource data between client and server.

---

## REST and JSON

REST does not require JSON, but JSON is very common.

Example:

```http
GET /students/1
```

Response:

```json
{
  "id": 1,
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

JSON can be used by:

* React
* Mobile apps
* Java applications
* Python applications
* Other backend services

---

## What is @RestController?

In Spring Boot:

```java
@RestController
```

is used to create a controller that handles API requests and returns response data.

Example:

```java
@RestController
public class StudentController {

}
```

---

## What is @RequestMapping?

`@RequestMapping` can define a common base path for a controller.

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

}
```

Now methods inside this controller can use paths relative to:

```text
/students
```

---

## GET All Students

```java
@GetMapping
public String getStudents() {
    return "All students";
}
```

Because the controller already has:

```java
@RequestMapping("/students")
```

the final endpoint becomes:

```http
GET /students
```

---

## GET One Student

```java
@GetMapping("/{id}")
public String getStudent(@PathVariable int id) {
    return "Student ID: " + id;
}
```

Request:

```http
GET /students/5
```

Then:

```text
id = 5
```

---

## POST Student

```java
@PostMapping
public ResponseEntity<String> createStudent() {

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body("Student created");
}
```

Request:

```http
POST /students
```

Response:

```text
201 Created
```

---

## PUT Student

```java
@PutMapping("/{id}")
public ResponseEntity<String> updateStudent(
        @PathVariable int id) {

    return ResponseEntity.ok(
            "Student updated: " + id
    );
}
```

Request:

```http
PUT /students/5
```

---

## DELETE Student

```java
@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteStudent(
        @PathVariable int id) {

    return ResponseEntity.noContent().build();
}
```

Request:

```http
DELETE /students/5
```

Response:

```text
204 No Content
```

---

## Complete REST Controller Example

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping
    public ResponseEntity<String> getStudents() {
        return ResponseEntity.ok("All students");
    }

    @GetMapping("/{id}")
    public ResponseEntity<String> getStudent(
            @PathVariable int id) {

        return ResponseEntity.ok(
                "Student: " + id
        );
    }

    @PostMapping
    public ResponseEntity<String> createStudent() {

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body("Student created");
    }

    @PutMapping("/{id}")
    public ResponseEntity<String> updateStudent(
            @PathVariable int id) {

        return ResponseEntity.ok(
                "Student updated: " + id
        );
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteStudent(
            @PathVariable int id) {

        return ResponseEntity.noContent().build();
    }
}
```

For now, this code is for learning REST design.

Later the architecture will include:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

---

## Nested Resources

Nested resources show a relationship between resources.

Example:

```http
GET /products/10/reviews
```

means:

> Get reviews for product 10.

Another example:

```http
GET /users/5/orders
```

means:

> Get orders belonging to user 5.

Avoid making URLs too deeply nested because they become difficult to maintain.

---

## Query Parameters

Query parameters are commonly used for:

* Filtering
* Searching
* Sorting
* Pagination

Example:

```http
GET /products?category=laptop
```

This means:

> Get products where category is laptop.

Another example:

```http
GET /products?minPrice=1000&maxPrice=5000
```

---

## Path Variable vs Query Parameter

Use a path when identifying a specific resource.

Example:

```http
GET /students/5
```

Here:

```text
5
```

identifies one student.

Use query parameters for filtering or optional conditions.

Example:

```http
GET /students?city=Hyderabad
```

Here:

```text
city=Hyderabad
```

is a filter.

---

## Good REST Design vs Bad REST Design

Bad:

```text
GET /getAllStudents

POST /createStudent

POST /deleteStudent

GET /updateStudent
```

Better:

```http
GET /students

POST /students

DELETE /students/5

PUT /students/5
```

The better design uses:

```text
Resource
+
HTTP Method
```

---

## Real-World Example — E-commerce API

Resources:

```text
Products
Users
Orders
Payments
Reviews
```

Product endpoints:

```http
GET /products
GET /products/10
POST /products
PUT /products/10
PATCH /products/10
DELETE /products/10
```

Order endpoints:

```http
GET /orders
GET /orders/15
POST /orders
DELETE /orders/15
```

User endpoints:

```http
GET /users/5
PATCH /users/5
```

---

## REST is Not Only CRUD

REST APIs are often explained using CRUD, but real applications can also have actions such as:

```text
Cancel order
Approve payment
Verify email
Reset password
```

REST is broader than simply exposing database tables.

---

## Important REST Principles

### 1. Resources

Design APIs around resources.

```text
/users
/products
/orders
```

### 2. Correct HTTP Methods

Use:

```text
GET
POST
PUT
PATCH
DELETE
```

properly.

### 3. Stateless Communication

Each request should contain enough information for the server to process it.

### 4. Client-Server Separation

Frontend and backend should have separate responsibilities.

### 5. Resource Representations

Resources are commonly sent as JSON.

---

## Practice — Employee API

Get all employees:

```http
GET /employees
```

Get employee 5:

```http
GET /employees/5
```

Create an employee:

```http
POST /employees
```

Fully update employee 5:

```http
PUT /employees/5
```

Partially update employee 5:

```http
PATCH /employees/5
```

Delete employee 5:

```http
DELETE /employees/5
```

Filter employees by department:

```http
GET /employees?department=IT
```

---

# Day 05 Summary

Today I learned:

* What REST is
* API vs REST API
* Resources
* Resource URLs
* CRUD in REST
* Why REST URLs usually use nouns
* Why plural resource names are common
* Statelessness
* Client-server separation
* JSON resource representations
* `@RestController`
* `@RequestMapping`
* REST controller structure
* Nested resources
* Query parameters
* Path variables vs query parameters
* Good vs bad REST API design
* Real-world REST API patterns
