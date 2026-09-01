# Day 02 — HTTP Fundamentals

## What is HTTP?

HTTP stands for:

**HyperText Transfer Protocol**

HTTP is a set of rules used for communication between a client and a server over the web.

Basic flow:

```text
Client
   |
   | HTTP Request
   ↓
Server
   |
   | HTTP Response
   ↓
Client
```

---

## HTTP Request

An HTTP request is sent by the client to the server.

Example:

```http
GET /students
```

This means:

> Get the student data.

A request can contain:

* HTTP Method
* URL
* Headers
* Body

---

## HTTP Response

An HTTP response is sent by the server back to the client.

Example response:

```json
{
  "id": 1,
  "name": "Rahul"
}
```

A response usually contains:

* Status Code
* Headers
* Body

---

## HTTP Methods

Common HTTP methods are:

| Method | Purpose               |
| ------ | --------------------- |
| GET    | Read data             |
| POST   | Create data           |
| PUT    | Update/replace data   |
| PATCH  | Partially update data |
| DELETE | Delete data           |

Example:

```http
GET /users
```

means get users.

```http
DELETE /users/5
```

means delete user 5.

---

## What is a URL?

URL stands for:

**Uniform Resource Locator**

A URL is the address used to access a resource.

Example:

```text
https://example.com/products/10
```

It contains:

```text
https://
```

Protocol

```text
example.com
```

Domain

```text
/products/10
```

Path

---

## Domain

A domain identifies the website or server.

Examples:

```text
google.com
github.com
amazon.com
```

A domain name is easier for humans to remember than an IP address.

---

## Path

A path identifies a particular resource on the server.

Example:

```text
https://example.com/users/5
```

The path is:

```text
/users/5
```

This may mean:

> Get user 5.

---

## What is localhost?

`localhost` means:

**My own computer.**

Example:

```text
http://localhost:8080
```

If Spring Boot is running on my computer, I can access it using localhost.

---

## What is a Port?

A port identifies a particular application running on a computer.

Example:

```text
localhost:8080
```

Here:

```text
localhost
```

means my computer.

```text
8080
```

is the port.

Common examples:

| Application | Common Port |
| ----------- | ----------- |
| Spring Boot | 8080        |
| React/Vite  | 5173        |
| MySQL       | 3306        |
| PostgreSQL  | 5432        |

Different applications can run on the same computer using different ports.

---

## Local Development Example

```text
React
localhost:5173
      |
      | HTTP Request
      ↓
Spring Boot
localhost:8080
      |
      ↓
MySQL
localhost:3306
```

React sends HTTP requests to Spring Boot.

Spring Boot communicates with MySQL.

Spring Boot then sends the response back to React.

---

## HTTP vs HTTPS

HTTP and HTTPS are used for communication between client and server.

### HTTP

```text
http://example.com
```

HTTP does not provide TLS encryption.

### HTTPS

```text
https://example.com
```

HTTPS uses TLS encryption to protect data while it travels between the client and server.

Production applications normally use HTTPS.

---

## Browser vs Postman

A browser can easily test GET requests.

Example:

```text
http://localhost:8080/students
```

Postman is better for testing APIs because it can easily send:

* GET
* POST
* PUT
* PATCH
* DELETE
* Headers
* JSON body
* Authentication

---

## Spring Boot Example

Later we will write Java code like:

```java
@RestController
public class ProductController {

    @GetMapping("/products")
    public String getProducts() {
        return "Products";
    }
}
```

If Spring Boot runs on port 8080, we can access it using:

```text
http://localhost:8080/products
```

Flow:

```text
Browser/Postman
      ↓
GET /products
      ↓
Spring Boot
      ↓
@GetMapping("/products")
      ↓
getProducts()
      ↓
HTTP Response
```

---

## HTTP vs API

HTTP and API are not the same.

**API** is an interface that allows software applications to communicate.

**HTTP** is a communication protocol commonly used by web APIs.

REST APIs commonly use HTTP.

---

## Day 02 Summary

Today I learned:

* What HTTP is
* HTTP Request
* HTTP Response
* HTTP Methods
* URL
* Domain
* Path
* localhost
* Ports
* HTTP vs HTTPS
* Browser vs Postman
* How React, Spring Boot and MySQL communicate locally
* Difference between HTTP and API
