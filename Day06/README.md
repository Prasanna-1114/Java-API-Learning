# Day 06 — First Spring Boot REST API

## Goal

Today I started building a real REST API using Java and Spring Boot.

The goal is to understand how:

```text
HTTP Request
   ↓
Spring Boot
   ↓
Controller
   ↓
Java Method
   ↓
HTTP Response
```

works in practice.

---

## What is Spring Boot?

Spring Boot is a Java framework used to build backend applications and REST APIs.

It simplifies many things such as:

* Web server setup
* Dependency management
* REST API development
* JSON conversion
* Database integration
* Configuration

Spring Boot is built on top of the Spring Framework.

---

## Spring vs Spring Boot

Spring Framework is the larger Java framework.

Spring Boot makes Spring applications easier to configure and run.

Simple idea:

```text
Spring
→ Main framework

Spring Boot
→ Easier way to build and run Spring applications
```

---

## Spring Initializr

Spring Initializr is used to create a new Spring Boot project.

For this project, the basic setup is:

```text
Project: Maven
Language: Java
Packaging: Jar
Java: 17 or later
```

Dependency:

```text
Spring Web
```

Spring Web is required to create REST APIs.

---

## What is Maven?

Maven is a Java build and dependency management tool.

A Maven project contains:

```text
pom.xml
```

The `pom.xml` file contains project information and dependencies.

Example:

```text
Spring Web
```

is added as a dependency through Maven.

---

## Basic Spring Boot Project Structure

A Spring Boot project may look like:

```text
student-api/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/studentapi/
│   │   │       └── StudentApiApplication.java
│   │   │
│   │   └── resources/
│   │       └── application.properties
│   │
│   └── test/
│
└── pom.xml
```

The main Java code goes inside:

```text
src/main/java
```

---

## Main Spring Boot Class

Example:

```java
@SpringBootApplication
public class StudentApiApplication {

    public static void main(String[] args) {

        SpringApplication.run(
                StudentApiApplication.class,
                args
        );
    }
}
```

---

## What is @SpringBootApplication?

```java
@SpringBootApplication
```

marks the main Spring Boot application class.

It tells Spring Boot to start and configure the application.

---

## What does SpringApplication.run() do?

```java
SpringApplication.run(
        StudentApiApplication.class,
        args
);
```

starts the Spring Boot application.

It also starts the embedded web server.

By default, Spring Boot commonly runs on:

```text
http://localhost:8080
```

---

## Embedded Web Server

Spring Boot includes an embedded web server.

For Spring Web applications, this is commonly Tomcat.

So:

```text
Run Java application
   ↓
Spring Boot starts
   ↓
Embedded server starts
   ↓
API becomes available
```

---

## First Controller

Create a controller class:

```java
@RestController
public class StudentController {

    @GetMapping("/hello")
    public String hello() {

        return "Hello from Student API";
    }
}
```

Now open:

```text
http://localhost:8080/hello
```

Response:

```text
Hello from Student API
```

---

## How the First Endpoint Works

Request:

```http
GET /hello
```

Flow:

```text
Browser/Postman
   ↓
GET /hello
   ↓
Spring Boot
   ↓
StudentController
   ↓
@GetMapping("/hello")
   ↓
hello()
   ↓
HTTP Response
```

---

## What is @RestController?

```java
@RestController
```

tells Spring Boot that the class handles REST API requests and returns response data.

---

## What is @GetMapping?

```java
@GetMapping("/hello")
```

maps an HTTP GET request to a Java method.

Example:

```http
GET /hello
```

runs:

```java
hello()
```

---

## Using @RequestMapping

Instead of repeating `/students` in every method, we can write:

```java
@RestController
@RequestMapping("/students")
public class StudentController {
}
```

Now `/students` becomes the base path.

---

## GET All Students

Example:

```java
@GetMapping
public String getStudents() {

    return "All students";
}
```

Final endpoint:

```http
GET /students
```

Response:

```text
All students
```

---

## GET One Student

Example:

```java
@GetMapping("/{id}")
public String getStudent(
        @PathVariable int id) {

    return "Student ID: " + id;
}
```

Request:

```http
GET /students/5
```

Response:

```text
Student ID: 5
```

---

## What is @PathVariable?

`@PathVariable` gets a value directly from the URL.

Example:

```text
/students/5
```

Here:

```text
5
```

is the student ID.

Spring maps it to:

```java
@PathVariable int id
```

So:

```text
/students/5
   ↓
{id} = 5
   ↓
id = 5
```

---

## Creating a Student Model

Create a simple Java class:

```java
public class Student {

    private int id;
    private String name;
    private String email;

    public Student() {
    }

    public Student(
            int id,
            String name,
            String email) {

        this.id = id;
        this.name = name;
        this.email = email;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

This is currently only a normal Java class.

It is not a database Entity yet.

---

## Returning a Java Object as JSON

Example:

```java
@GetMapping("/{id}")
public Student getStudent(
        @PathVariable int id) {

    return new Student(
            id,
            "Rahul",
            "rahul@gmail.com"
    );
}
```

Request:

```http
GET /students/5
```

Response:

```json
{
  "id": 5,
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

Spring Boot automatically converts the Java object into JSON.

Flow:

```text
Java Object
   ↓
Spring Boot
   ↓
JSON
   ↓
HTTP Response
```

---

## What is @RequestBody?

`@RequestBody` is used to receive JSON data from the client and convert it into a Java object.

Example:

```java
@PostMapping
public Student createStudent(
        @RequestBody Student student) {

    return student;
}
```

Request body:

```json
{
  "id": 10,
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

Spring converts:

```text
JSON
   ↓
@RequestBody
   ↓
Student Java Object
```

---

## POST Student

Example:

```java
@PostMapping
public ResponseEntity<Student> createStudent(
        @RequestBody Student student) {

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(student);
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

with JSON:

```json
{
  "id": 10,
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

---

## Important Point

At this stage, the POST request does not save data to MySQL.

It only does:

```text
Receive JSON
   ↓
Convert JSON to Student object
   ↓
Return Student object
```

Database integration will be added later.

---

## Complete Day 06 Controller Example

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping
    public ResponseEntity<String> getStudents() {

        return ResponseEntity.ok(
                "All students"
        );
    }

    @GetMapping("/{id}")
    public ResponseEntity<Student> getStudent(
            @PathVariable int id) {

        Student student = new Student(
                id,
                "Rahul",
                "rahul@gmail.com"
        );

        return ResponseEntity.ok(student);
    }

    @PostMapping
    public ResponseEntity<Student> createStudent(
            @RequestBody Student student) {

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(student);
    }
}
```

---

## Real Request Flow

Example:

```http
GET /students/5
```

Flow:

```text
Client
   ↓
HTTP GET Request
   ↓
Spring Boot
   ↓
StudentController
   ↓
@GetMapping("/{id}")
   ↓
Java Student Object
   ↓
JSON
   ↓
200 OK
   ↓
Client
```

POST flow:

```text
Client sends JSON
   ↓
POST /students
   ↓
@RequestBody
   ↓
Student Java Object
   ↓
ResponseEntity
   ↓
201 Created
   ↓
JSON Response
```

---

## What I Learned Today

* What Spring Boot is
* Spring vs Spring Boot
* Spring Initializr
* Spring Web
* Maven
* `pom.xml`
* Spring Boot project structure
* `@SpringBootApplication`
* `SpringApplication.run()`
* Embedded web server
* `@RestController`
* `@RequestMapping`
* `@GetMapping`
* `@PostMapping`
* `@PathVariable`
* `@RequestBody`
* Java object to JSON conversion
* JSON to Java object conversion
* `ResponseEntity`
* `201 Created`

---

## Day 06 Summary

Today I created the foundation of my first real Spring Boot REST API.

Current flow:

```text
Client
   ↓
Controller
   ↓
Java Logic
   ↓
Response
```

Database-related layers such as:

```text
Service
Repository
Entity
MySQL
```

will be introduced in the next stages.
