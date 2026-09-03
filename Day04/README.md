# Day 04 — HTTP Status Codes and API Responses

## What is an HTTP Status Code?

An HTTP status code is a 3-digit number returned by the server to tell the client what happened with the request.

Example:

```http
GET /students/5
```

If the student exists:

```text
200 OK
```

If the student does not exist:

```text
404 Not Found
```

So an HTTP response usually contains:

```text
Status Code
+
Headers
+
Response Body
```

---

## Why Are Status Codes Important?

Status codes help the client understand whether the request:

* Succeeded
* Failed
* Had invalid input
* Was not authorized
* Could not find the requested resource
* Failed because of a server problem

Example:

```http
GET /products/10
```

If product 10 does not exist, the server should return:

```text
404 Not Found
```

with:

```json
{
  "message": "Product not found"
}
```

---

# HTTP Status Code Groups

HTTP status codes are divided into groups:

| Range   | Meaning       |
| ------- | ------------- |
| 100–199 | Informational |
| 200–299 | Success       |
| 300–399 | Redirection   |
| 400–499 | Client Error  |
| 500–599 | Server Error  |

For API development, the most important ones are:

```text
200
201
204
400
401
403
404
409
500
```

---

# 200 OK

`200 OK` means:

> The request was successful.

Example:

```http
GET /students/5
```

Response:

```text
200 OK
```

Body:

```json
{
  "id": 5,
  "name": "Rahul"
}
```

Commonly used with:

* GET
* PUT
* PATCH

---

# 201 Created

`201 Created` means:

> A new resource was successfully created.

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

Response:

```text
201 Created
```

Body:

```json
{
  "id": 10,
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

Difference:

```text
200 → Request succeeded

201 → Request succeeded and a new resource was created
```

---

# 204 No Content

`204 No Content` means:

> The request succeeded, but the server does not return a response body.

Common example:

```http
DELETE /students/5
```

Response:

```text
204 No Content
```

This means the delete operation succeeded.

---

# 400 Bad Request

`400 Bad Request` means:

> The client sent invalid data or an invalid request.

Example request:

```json
{
  "name": "",
  "email": "wrong-email"
}
```

Possible response:

```text
400 Bad Request
```

Body:

```json
{
  "message": "Invalid student data"
}
```

Common reasons:

* Missing required field
* Invalid input
* Wrong data format
* Validation failure
* Invalid request parameter

---

# 401 Unauthorized

`401 Unauthorized` usually means:

> The user is not properly authenticated.

Example:

```http
GET /profile
```

without a valid login token.

Response:

```text
401 Unauthorized
```

Easy way to remember:

```text
401 → Who are you?
```

---

# 403 Forbidden

`403 Forbidden` means:

> The user is authenticated, but does not have permission to perform the action.

Example:

A normal user tries to access an admin-only API:

```http
DELETE /admin/users/5
```

Response:

```text
403 Forbidden
```

Easy way to remember:

```text
401 → You are not authenticated

403 → You are authenticated, but not allowed
```

---

# 404 Not Found

`404 Not Found` means:

> The requested resource does not exist.

Example:

```http
GET /students/999
```

If student 999 does not exist:

```text
404 Not Found
```

Body:

```json
{
  "message": "Student not found"
}
```

---

# 409 Conflict

`409 Conflict` means:

> The request conflicts with the current state of the system.

Example:

Suppose this email already exists:

```text
rahul@gmail.com
```

A new user tries to register with the same email.

Request:

```http
POST /users
```

Body:

```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

Possible response:

```text
409 Conflict
```

Other examples:

* Duplicate email
* Duplicate username
* Booking an already booked seat
* Creating a resource that already exists

---

# 500 Internal Server Error

`500 Internal Server Error` means:

> Something unexpected went wrong on the server.

Possible reasons:

* Database connection failure
* Unexpected exception
* Backend bug
* Server-side processing failure

Example:

```http
GET /students
```

If the backend unexpectedly fails:

```text
500 Internal Server Error
```

---

# 4xx vs 5xx

## 4xx

Usually means:

> There is a problem with the client's request.

Examples:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
```

## 5xx

Usually means:

> Something went wrong on the server.

Example:

```text
500 Internal Server Error
```

Remember:

```text
4xx → Client/request problem

5xx → Server problem
```

---

# Important Status Codes Summary

| Code | Meaning               | Common Use                     |
| ---: | --------------------- | ------------------------------ |
|  200 | OK                    | Successful request             |
|  201 | Created               | New resource created           |
|  204 | No Content            | Success with no response body  |
|  400 | Bad Request           | Invalid request/input          |
|  401 | Unauthorized          | User not authenticated         |
|  403 | Forbidden             | User does not have permission  |
|  404 | Not Found             | Resource does not exist        |
|  409 | Conflict              | Duplicate/conflicting resource |
|  500 | Internal Server Error | Unexpected server problem      |

---

# Easy Memory Trick

```text
2xx = Success

4xx = Client/request problem

5xx = Server problem
```

Specific meanings:

```text
200 → OK

201 → Created

204 → Success, no body

400 → Bad input

401 → Who are you?

403 → I know you, but you cannot do this

404 → Cannot find it

409 → Conflict

500 → Server error
```

---

# What is ResponseEntity?

In Spring Boot, `ResponseEntity` is used to control the HTTP response.

It can control:

* Status code
* Response body
* Headers

Example:

```java
@GetMapping("/students")
public ResponseEntity<String> getStudents() {

    return ResponseEntity.ok("All students");
}
```

This returns:

```text
200 OK
```

with the body:

```text
All students
```

---

# Returning 201 Created

Example:

```java
@PostMapping("/students")
public ResponseEntity<String> createStudent() {

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body("Student created");
}
```

This returns:

```text
201 Created
```

---

# Returning 404 Not Found

Example:

```java
@GetMapping("/students/{id}")
public ResponseEntity<String> getStudent(@PathVariable int id) {

    if (id == 100) {
        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body("Student not found");
    }

    return ResponseEntity.ok("Student found");
}
```

If the request is:

```http
GET /students/100
```

response:

```text
404 Not Found
```

---

# Returning 204 No Content

Example:

```java
@DeleteMapping("/students/{id}")
public ResponseEntity<Void> deleteStudent(@PathVariable int id) {

    return ResponseEntity.noContent().build();
}
```

This returns:

```text
204 No Content
```

`ResponseEntity<Void>` is used because there is no response body.

---

# Spring Boot Practice Example

For Day 4, this code is only for understanding status codes.

We are not yet creating Entity, Service, Repository, or Database code.

```java
@RestController
@RequestMapping("/books")
public class BookController {

    @GetMapping("/{id}")
    public ResponseEntity<String> getBook(@PathVariable int id) {

        if (id == 1) {
            return ResponseEntity.ok("Book found");
        }

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body("Book not found");
    }

    @PostMapping
    public ResponseEntity<String> createBook() {

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body("Book created");
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteBook(@PathVariable int id) {

        return ResponseEntity.noContent().build();
    }
}
```

For now, the flow is:

```text
HTTP Request
     ↓
Controller
     ↓
ResponseEntity
     ↓
HTTP Status Code
     ↓
Response
```

Later, the real application flow will become:

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

# Real-World Example — User Registration

Request:

```http
POST /users
```

Body:

```json
{
  "name": "Prasu",
  "email": "prasu@gmail.com",
  "password": "password123"
}
```

Possible responses:

```text
201 Created
→ User successfully created
```

```text
400 Bad Request
→ Invalid user information
```

```text
409 Conflict
→ Email already exists
```

```text
500 Internal Server Error
→ Unexpected backend problem
```

---

# Why Correct Status Codes Matter

The frontend can use the status code to decide what to display.

Example:

```text
201
→ Registration successful
```

```text
400
→ Please check your input
```

```text
409
→ Email already registered
```

```text
500
→ Something went wrong
```

A good API should not return:

```text
200 OK
```

when the operation actually failed.

For example, this is poor design:

```text
200 OK
```

with:

```json
{
  "error": "Student not found"
}
```

A better response is:

```text
404 Not Found
```

with:

```json
{
  "message": "Student not found"
}
```

---

# Day 04 Summary

Today I learned:

* What HTTP status codes are
* Why APIs use status codes
* Status code groups
* 200 OK
* 201 Created
* 204 No Content
* 400 Bad Request
* 401 Unauthorized
* 403 Forbidden
* 404 Not Found
* 409 Conflict
* 500 Internal Server Error
* Difference between 4xx and 5xx
* Difference between 401 and 403
* `ResponseEntity`
* `HttpStatus`
* How Spring Boot returns different API responses
* Why status codes are important in real applications
