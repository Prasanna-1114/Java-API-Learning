# Day 07 — Spring Boot Layered Architecture

## Goal

Today I learned how a real Spring Boot backend is divided into layers.

The main flow is:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

The response comes back in the reverse direction:

```text
Database
  ↓
Repository
  ↓
Service
  ↓
Controller
  ↓
Client
```

Each layer has a separate responsibility.

---

# Why Do We Need Layers?

In a small application, we could write everything inside a Controller.

Example:

```java
@GetMapping("/{id}")
public Student getStudent(@PathVariable Long id) {

    // validate id
    // access database
    // apply business rules
    // handle errors
    // return student
}
```

This may work for a very small project.

But when the application becomes large, the Controller becomes difficult to:

* Read
* Test
* Maintain
* Debug
* Reuse

So we separate the code into different layers.

---

# Main Spring Boot Layers

| Layer      | Responsibility                      |
| ---------- | ----------------------------------- |
| Controller | Handles HTTP requests and responses |
| Service    | Handles business logic              |
| Repository | Handles database access             |
| Entity     | Represents database data            |

Easy way to remember:

```text
Controller
→ What request came?

Service
→ What should the application do?

Repository
→ What data should be read or stored?

Entity
→ What does the data look like?
```

---

# 1. Controller Layer

The Controller is the first backend layer that receives the HTTP request.

Example request:

```http
GET /students/5
```

Example Controller:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    private final StudentService studentService;

    public StudentController(StudentService studentService) {
        this.studentService = studentService;
    }

    @GetMapping("/{id}")
    public Student getStudent(@PathVariable Long id) {

        return studentService.getStudentById(id);
    }
}
```

The Controller does not directly access the database.

Instead, it calls:

```java
studentService.getStudentById(id);
```

The Controller mainly handles:

* HTTP methods
* Endpoints
* Path variables
* Query parameters
* Request bodies
* Status codes
* Responses

---

# 2. Service Layer

The Service layer contains the application's business logic.

Business logic means the rules of the application.

Examples:

```text
Do not allow duplicate email

Do not allow negative product price

Do not allow booking an already booked seat

Apply discount if order total is above a limit
```

Example:

```java
@Service
public class StudentService {

    private final StudentRepository studentRepository;

    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    public Student getStudentById(Long id) {

        return studentRepository
                .findById(id)
                .orElse(null);
    }
}
```

`@Service` tells Spring that this class contains service/business logic.

The Service can:

* Apply business rules
* Perform calculations
* Validate data
* Make decisions
* Call Repository methods

---

# 3. Repository Layer

The Repository layer communicates with the database.

Example:

```java
public interface StudentRepository
        extends JpaRepository<Student, Long> {
}
```

By extending `JpaRepository`, Spring Data JPA provides many database methods automatically.

Examples:

```java
findAll();
```

Gets all students.

```java
findById(id);
```

Gets one student.

```java
save(student);
```

Creates or updates a student.

```java
deleteById(id);
```

Deletes a student.

---

# What is JpaRepository?

Example:

```java
JpaRepository<Student, Long>
```

`Student` means:

> This Repository manages Student entities.

`Long` means:

> The Student primary key uses the Long datatype.

Example Entity field:

```java
@Id
private Long id;
```

Therefore:

```java
JpaRepository<Student, Long>
```

is correct.

---

# 4. Entity Layer

An Entity represents data stored in a database.

Example:

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    public Student() {
    }

    public Student(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // getters and setters
}
```

Conceptually:

```text
Java Entity
Student
   ↓
Database Table
student
```

Fields:

```text
id
name
email
```

can map to database columns.

---

# Important Entity Annotations

## @Entity

```java
@Entity
```

Marks a Java class as a JPA Entity.

It tells JPA that the class represents persistent database data.

---

## @Id

```java
@Id
private Long id;
```

Marks the primary key.

The primary key uniquely identifies each row.

Example:

```text
id | name
---------
1  | Rahul
2  | Priya
3  | Arjun
```

---

## @GeneratedValue

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)
```

means the ID can be automatically generated by the database.

Example request:

```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

After saving:

```json
{
  "id": 1,
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

The client did not have to manually create the ID.

---

# Complete GET Request Flow

Suppose the client sends:

```http
GET /students/5
```

## Step 1 — Controller

```java
@GetMapping("/{id}")
public Student getStudent(@PathVariable Long id) {

    return studentService.getStudentById(id);
}
```

The Controller receives:

```text
id = 5
```

Then calls:

```java
studentService.getStudentById(5L);
```

---

## Step 2 — Service

```java
public Student getStudentById(Long id) {

    return studentRepository
            .findById(id)
            .orElse(null);
}
```

The Service calls:

```java
studentRepository.findById(5L);
```

---

## Step 3 — Repository

The Repository searches the database.

Conceptually:

```text
StudentRepository
      ↓
Find student where id = 5
      ↓
Database
```

Suppose the database contains:

```text
id | name  | email
--------------------------
5  | Rahul | rahul@gmail.com
```

The result returns:

```text
Database
   ↓
Repository
   ↓
Service
   ↓
Controller
   ↓
Client
```

The client may receive:

```json
{
  "id": 5,
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

---

# POST Request Through All Layers

Client sends:

```http
POST /students
```

Body:

```json
{
  "name": "Arjun",
  "email": "arjun@gmail.com"
}
```

Controller:

```java
@PostMapping
public ResponseEntity<Student> createStudent(
        @RequestBody Student student) {

    Student savedStudent =
            studentService.createStudent(student);

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(savedStudent);
}
```

Service:

```java
public Student createStudent(Student student) {

    return studentRepository.save(student);
}
```

Flow:

```text
POST /students
      ↓
Controller
      ↓
Student Java Object
      ↓
Service
      ↓
Repository.save()
      ↓
Database
      ↓
Student saved
      ↓
201 Created
```

---

# Why Do We Need the Service Layer?

The Controller could technically call the Repository directly.

Example:

```java
@GetMapping
public List<Student> getStudents() {

    return studentRepository.findAll();
}
```

But this becomes difficult when business logic grows.

Example:

```java
public Student createStudent(Student student) {

    if (student.getAge() < 16) {
        throw new RuntimeException(
                "Student must be at least 16"
        );
    }

    if (studentRepository.existsByEmail(student.getEmail())) {
        throw new RuntimeException(
                "Email already exists"
        );
    }

    return studentRepository.save(student);
}
```

These application rules belong in the Service layer.

So:

```text
Controller
→ HTTP responsibility

Service
→ Business responsibility

Repository
→ Database responsibility
```

---

# Dependency Injection

Suppose `StudentController` needs `StudentService`.

We write:

```java
private final StudentService studentService;

public StudentController(StudentService studentService) {
    this.studentService = studentService;
}
```

We do not manually create:

```java
new StudentService();
```

Spring creates and manages the required object and provides it to the Controller.

This is called:

**Dependency Injection**

Simple meaning:

> A class receives the object it needs instead of creating the dependency itself.

Example:

```text
StudentController
      ↓ needs
StudentService
```

Spring injects `StudentService`.

Similarly:

```text
StudentService
      ↓ needs
StudentRepository
```

Spring injects `StudentRepository`.

---

# Constructor Injection

Providing dependencies through a constructor is called constructor injection.

Example:

```java
public StudentController(StudentService studentService) {

    this.studentService = studentService;
}
```

Constructor injection is commonly preferred because dependencies are:

* Clear
* Easier to test
* Easier to manage
* Less tightly coupled

---

# Model vs Entity

A model is a general object representing application data.

Example:

```java
public class Student {
}
```

An Entity specifically represents persistent database data.

Example:

```java
@Entity
public class Student {
}
```

So:

```text
Model
→ General application data

Entity
→ Database-mapped persistent data
```

---

# Project Structure

A common project structure is:

```text
src/main/java/com/example/studentapi/
│
├── StudentApiApplication.java
│
├── controller/
│   └── StudentController.java
│
├── service/
│   └── StudentService.java
│
├── repository/
│   └── StudentRepository.java
│
└── entity/
    └── Student.java
```

Later we may also add:

```text
dto/
exception/
config/
security/
```

---

# Real-World Example — E-commerce Order

Suppose the client sends:

```http
POST /orders
```

## Controller

Receives the HTTP request.

## Service

May:

```text
Check product exists
↓
Check stock
↓
Calculate total price
↓
Apply discount
↓
Validate payment rules
↓
Create order
↓
Reduce stock
```

## Repository

Reads and writes:

```text
Products
Orders
Users
```

## Database

Stores the actual data.

This is why separating layers becomes important in real applications.

---

# Important Annotations

```java
@RestController
```

Used for API Controllers.

```java
@RequestMapping
```

Defines a base endpoint.

```java
@Service
```

Marks a Service class.

```java
@Repository
```

Marks a Repository component.

For Spring Data JPA Repository interfaces, `@Repository` is often optional because Spring can detect them automatically.

```java
@Entity
```

Marks an Entity.

```java
@Id
```

Marks the primary key.

```java
@GeneratedValue
```

Configures automatic ID generation.

---

# Day 07 Summary

Today I learned:

* Controller layer
* Service layer
* Repository layer
* Entity layer
* Layered architecture
* Business logic
* Database access
* `JpaRepository`
* `@RestController`
* `@Service`
* `@Repository`
* `@Entity`
* `@Id`
* `@GeneratedValue`
* Dependency Injection
* Constructor Injection
* Model vs Entity

Main flow:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

---

# Day 07 Practice Task — Book API

Create the conceptual structure for a Book API.

## Book Entity

The Book should contain:

```java
private Long id;
private String title;
private String author;
private double price;
```

Create:

```java
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String author;
    private double price;

    public Book() {
    }

    // getters and setters
}
```

---

## Book Repository

Create:

```java
public interface BookRepository
        extends JpaRepository<Book, Long> {
}
```

---

## Book Service

Create:

```java
@Service
public class BookService {

    private final BookRepository bookRepository;

    public BookService(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    public List<Book> getAllBooks() {

        return bookRepository.findAll();
    }

    public Book getBookById(Long id) {

        return bookRepository
                .findById(id)
                .orElse(null);
    }
}
```

---

## Book Controller

Create:

```java
@RestController
@RequestMapping("/books")
public class BookController {

    private final BookService bookService;

    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    @GetMapping
    public ResponseEntity<List<Book>> getBooks() {

        return ResponseEntity.ok(
                bookService.getAllBooks()
        );
    }

    @GetMapping("/{id}")
    public ResponseEntity<Book> getBook(
            @PathVariable Long id) {

        return ResponseEntity.ok(
                bookService.getBookById(id)
        );
    }
}
```

---

# Understand the Practice Flow

If the client sends:

```http
GET /books/10
```

the flow should be:

```text
GET /books/10
      ↓
BookController
      ↓
BookService
      ↓
BookRepository
      ↓
Database
```

Then identify where each responsibility belongs:

```text
Receive GET /books
→ Controller

Check that price is not negative
→ Service

findAll()
→ Repository

id, title, author, price
→ Entity
```

## Practice Questions

1. Which layer receives `GET /books/5`?
2. Which layer should check whether a book price is negative?
3. Which layer should call `findById()`?
4. Which layer defines `id`, `title`, `author`, and `price`?
5. Why should the Controller not contain all database and business logic?
6. What does `JpaRepository<Book, Long>` mean?
7. Why do we use constructor injection?
8. Trace the complete flow of:

```http
GET /books/5
```

from client to database and back.
