# Day 08 — Spring Data JPA and Database Integration

## Goal

Today I learned how a Spring Boot application communicates with a database using:

- Spring Data JPA
- JPA
- Hibernate
- ORM
- Entity
- Repository
- Service
- Controller
- `JpaRepository`
- H2 Database
- Database configuration

The complete architecture is:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Spring Data JPA
  ↓
Hibernate
  ↓
Database
```

---

# 1. What is JPA?

JPA stands for:

**Java Persistence API**

JPA provides a standard way for Java applications to work with relational database data.

Suppose we have a Java class:

```java
public class Book {

    private Long id;
    private String title;
}
```

And a database table:

```text
book

id | title
-------------------
1  | Java Basics
2  | Spring Boot
```

JPA helps connect Java objects with database data.

```text
Java Object
    ↓
JPA
    ↓
Database Table
```

---

# 2. What is Spring Data JPA?

Spring Data JPA makes database access easier in Spring Boot.

Without it, we may have to manually write a lot of database access code.

With Spring Data JPA, we get methods such as:

```java
findAll();
findById(id);
save(book);
deleteById(id);
```

These methods are commonly available through:

```java
JpaRepository
```

---

# 3. What is ORM?

ORM stands for:

**Object Relational Mapping**

ORM connects Java objects with relational database tables.

Example:

```text
Java Field       Database Column

id        ↔      id
title     ↔      title
author    ↔      author
price     ↔      price
```

For example:

```java
Book book = new Book();
```

can represent a row inside a database table.

Conceptually:

```text
Java Book Object
       ↕
      ORM
       ↕
Database Book Row
```

---

# 4. What is Hibernate?

Hibernate is an ORM framework commonly used in Spring Boot applications.

It helps convert operations involving Java Entities into database operations.

For example:

```java
bookRepository.save(book);
```

can eventually result in an SQL operation similar to:

```sql
INSERT INTO book (...);
```

We normally do not have to manually write basic SQL for simple CRUD operations when using Spring Data JPA.

Simplified flow:

```text
Spring Data JPA
      ↓
JPA
      ↓
Hibernate
      ↓
SQL
      ↓
Database
```

---

# 5. Entity Layer

An Entity represents persistent data that can be stored in a database.

Example:

```java
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String author;
    private double price;
}
```

Conceptually:

```text
Java Entity
   Book
    ↓
Database Table
   book
```

---

# 6. What is @Entity?

```java
@Entity
```

marks a Java class as a JPA Entity.

Example:

```java
@Entity
public class Book {
}
```

It tells JPA that the class represents persistent data.

---

# 7. What is @Id?

```java
@Id
```

marks the primary key.

Example:

```java
@Id
private Long id;
```

A primary key uniquely identifies each database row.

Example:

```text
id | title
---------------------
1  | Java Basics
2  | Spring Boot
3  | SQL Basics
```

Each `id` is unique.

---

# 8. What is @GeneratedValue?

Example:

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)
```

This allows the database to generate the ID automatically.

Client may send:

```json
{
  "title": "Java Basics",
  "author": "John",
  "price": 499
}
```

After saving, the database may create:

```json
{
  "id": 1,
  "title": "Java Basics",
  "author": "John",
  "price": 499
}
```

The client does not need to manually provide:

```text
id = 1
```

---

# 9. Complete Book Entity

```java
package com.example.bookapi.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

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

    public Book(String title, String author, double price) {
        this.title = title;
        this.author = author;
        this.price = price;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}
```

---

# 10. Repository Layer

The Repository layer handles database access.

Example:

```java
package com.example.bookapi.repository;

import com.example.bookapi.entity.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository
        extends JpaRepository<Book, Long> {
}
```

This interface may look empty, but `JpaRepository` already provides many useful database methods.

---

# 11. What Does JpaRepository<Book, Long> Mean?

Consider:

```java
JpaRepository<Book, Long>
```

`Book` means:

```text
This repository manages Book entities.
```

`Long` means:

```text
The Book primary key has datatype Long.
```

Because the Book Entity contains:

```java
private Long id;
```

we use:

```java
JpaRepository<Book, Long>
```

---

# 12. Important JpaRepository Methods

## findAll()

```java
bookRepository.findAll();
```

Gets all Book records.

Conceptually:

```text
findAll()
   ↓
Repository
   ↓
Hibernate
   ↓
Database
   ↓
All Book rows
```

---

## findById()

```java
bookRepository.findById(5L);
```

Finds Book with ID 5.

Conceptually:

```text
findById(5)
    ↓
Repository
    ↓
Database
    ↓
Find Book where id = 5
```

---

## save()

```java
bookRepository.save(book);
```

For a new Book:

```text
New Book Object
      ↓
save()
      ↓
Hibernate
      ↓
INSERT
      ↓
Database
```

`save()` can also be used when persisting changes to an existing Entity.

---

## deleteById()

```java
bookRepository.deleteById(5L);
```

Deletes Book with ID 5.

Conceptually:

```text
deleteById(5)
      ↓
Repository
      ↓
Database
      ↓
Delete Book where id = 5
```

---

# 13. Complete Book Repository

```java
package com.example.bookapi.repository;

import com.example.bookapi.entity.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository
        extends JpaRepository<Book, Long> {
}
```

---

# 14. Service Layer

The Service layer contains business logic and communicates with the Repository.

Example responsibilities:

```text
Check if price is valid
Check whether a Book exists
Apply business rules
Call Repository methods
```

Complete example:

```java
package com.example.bookapi.service;

import com.example.bookapi.entity.Book;
import com.example.bookapi.repository.BookRepository;
import org.springframework.stereotype.Service;

import java.util.List;

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

    public Book createBook(Book book) {

        return bookRepository.save(book);
    }

    public void deleteBook(Long id) {

        bookRepository.deleteById(id);
    }
}
```

---

# 15. Controller Layer

The Controller receives HTTP requests from the client.

It communicates with the Service layer.

Complete example:

```java
package com.example.bookapi.controller;

import com.example.bookapi.entity.Book;
import com.example.bookapi.service.BookService;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/books")
public class BookController {

    private final BookService bookService;

    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    @GetMapping
    public ResponseEntity<List<Book>> getAllBooks() {

        return ResponseEntity.ok(
                bookService.getAllBooks()
        );
    }

    @GetMapping("/{id}")
    public ResponseEntity<Book> getBookById(
            @PathVariable Long id) {

        Book book = bookService.getBookById(id);

        return ResponseEntity.ok(book);
    }

    @PostMapping
    public ResponseEntity<Book> createBook(
            @RequestBody Book book) {

        Book savedBook =
                bookService.createBook(book);

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(savedBook);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteBook(
            @PathVariable Long id) {

        bookService.deleteBook(id);

        return ResponseEntity
                .noContent()
                .build();
    }
}
```

---

# 16. Complete Architecture

The Book API now follows:

```text
Client
   ↓
BookController
   ↓
BookService
   ↓
BookRepository
   ↓
Spring Data JPA
   ↓
Hibernate
   ↓
Database
```

Each layer has a separate job.

```text
Controller
→ Handles HTTP requests and responses

Service
→ Handles business logic

Repository
→ Handles database access

Entity
→ Represents persistent database data

Hibernate
→ Performs ORM/database mapping

Database
→ Stores actual records
```

---

# 17. POST Request Flow

Suppose the client sends:

```http
POST /books
```

Request body:

```json
{
  "title": "Java Basics",
  "author": "John",
  "price": 499
}
```

First, the Controller receives the JSON:

```java
@RequestBody Book book
```

Spring converts:

```text
JSON
 ↓
Book Java Object
```

Then:

```java
bookService.createBook(book);
```

The Service calls:

```java
bookRepository.save(book);
```

Then:

```text
Book Object
    ↓
Repository
    ↓
Spring Data JPA
    ↓
Hibernate
    ↓
SQL INSERT
    ↓
Database
```

The database may generate:

```text
id = 1
```

The response may be:

```text
201 Created
```

with:

```json
{
  "id": 1,
  "title": "Java Basics",
  "author": "John",
  "price": 499
}
```

---

# 18. GET All Books Flow

Request:

```http
GET /books
```

Controller:

```java
bookService.getAllBooks();
```

Service:

```java
bookRepository.findAll();
```

Flow:

```text
GET /books
    ↓
BookController
    ↓
BookService
    ↓
BookRepository
    ↓
findAll()
    ↓
Hibernate
    ↓
Database
```

Possible response:

```json
[
  {
    "id": 1,
    "title": "Java Basics",
    "author": "John",
    "price": 499
  },
  {
    "id": 2,
    "title": "Spring Boot",
    "author": "David",
    "price": 599
  }
]
```

---

# 19. GET Book By ID Flow

Request:

```http
GET /books/5
```

Flow:

```text
Client
  ↓
BookController
  ↓
BookService
  ↓
BookRepository
  ↓
findById(5)
  ↓
Hibernate
  ↓
Database
```

The database searches for:

```text
Book where id = 5
```

Then the result travels back:

```text
Database
  ↓
Repository
  ↓
Service
  ↓
Controller
  ↓
JSON
  ↓
Client
```

---

# 20. DELETE Book Flow

Request:

```http
DELETE /books/5
```

Controller calls:

```java
bookService.deleteBook(5L);
```

Service calls:

```java
bookRepository.deleteById(5L);
```

Flow:

```text
DELETE /books/5
       ↓
BookController
       ↓
BookService
       ↓
BookRepository
       ↓
deleteById(5)
       ↓
Database
```

Possible response:

```text
204 No Content
```

---

# 21. What is H2 Database?

H2 is a lightweight relational database.

It is useful for:

- Learning
- Testing
- Development

It can run as an in-memory database.

Example:

```properties
spring.datasource.url=jdbc:h2:mem:bookdb
```

Here:

```text
bookdb
```

is the database name.

Because it is in memory, its data may disappear when the application stops.

This is useful for learning because we do not need to install MySQL immediately.

---

# 22. Database Configuration

Database configuration is usually written inside:

```text
src/main/resources/application.properties
```

Example:

```properties
spring.datasource.url=jdbc:h2:mem:bookdb

spring.datasource.driverClassName=org.h2.Driver

spring.datasource.username=sa

spring.datasource.password=

spring.jpa.hibernate.ddl-auto=update

spring.h2.console.enabled=true
```

---

# 23. What Does ddl-auto=update Mean?

Example:

```properties
spring.jpa.hibernate.ddl-auto=update
```

This tells Hibernate to update the database schema based on the Entity classes.

Suppose the Book Entity contains:

```java
private String title;
private String author;
private double price;
```

Hibernate can create corresponding database columns.

For learning, `update` is convenient.

Production systems normally manage database schema changes more carefully.

---

# 24. Required Maven Dependencies

For a real Spring Boot project, we may use these dependencies:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

Purpose:

```text
Spring Web
→ REST APIs

Spring Data JPA
→ Database access using JPA

H2
→ Database used for learning/testing
```

---

# 25. Real-World Example

Imagine an e-commerce application.

Request:

```http
GET /products/10
```

Flow:

```text
ProductController
       ↓
ProductService
       ↓
ProductRepository
       ↓
Spring Data JPA
       ↓
Hibernate
       ↓
Database
```

Another request:

```http
POST /products
```

may perform:

```text
Receive JSON
↓
Convert JSON to Product object
↓
Validate product
↓
Service applies business rules
↓
Repository saves product
↓
Database stores product
↓
Return 201 Created
```

The same architecture can be used for:

- Products
- Users
- Orders
- Students
- Employees
- Books
- Tickets
- Payments
- Bookings

---

# 26. Day 08 Practice Task

Design a Book API with the following endpoints:

```http
POST /books
GET /books
GET /books/{id}
DELETE /books/{id}
```

For each request, identify the layers.

Example:

```text
POST /books
    ↓
Controller receives request
    ↓
Service handles business logic
    ↓
Repository calls save()
    ↓
Hibernate converts operation
    ↓
Database stores Book
```

---

# 27. Practice Questions

## Question 1

What does JPA stand for?

Answer:

```text
Java Persistence API
```

---

## Question 2

What does ORM stand for?

Answer:

```text
Object Relational Mapping
```

ORM connects Java objects with relational database data.

---

## Question 3

What does `@Entity` mean?

Answer:

It marks a Java class as persistent database data.

---

## Question 4

What does `@Id` mean?

Answer:

It marks the primary key.

---

## Question 5

What does this mean?

```java
JpaRepository<Book, Long>
```

Answer:

```text
Book
→ Entity managed by the Repository

Long
→ Datatype of the Book primary key
```

---

## Question 6

Who provides methods like:

```java
findAll();
findById();
save();
deleteById();
```

Answer:

```text
JpaRepository through Spring Data JPA
```

---

## Question 7

Which layer receives:

```http
GET /books
```

Answer:

```text
Controller
```

---

## Question 8

Which layer should call:

```java
bookRepository.findAll();
```

Answer:

```text
Service
```

---

## Question 9

Which layer communicates with the database?

Answer:

```text
Repository
```

---

## Question 10

Where should business logic normally be written?

Answer:

```text
Service Layer
```

---

## Question 11

What does:

```java
bookRepository.save(book);
```

do?

Answer:

It persists a Book into the database or persists changes to an existing Book Entity.

---

## Question 12

Where is database configuration normally written?

Answer:

```text
src/main/resources/application.properties
```

---

## Question 13

What is Hibernate?

Answer:

Hibernate is an ORM framework that helps map Java Entities to relational database operations.

---

## Question 14

Trace:

```http
GET /books/5
```

Answer:

```text
Client
  ↓
BookController
  ↓
BookService
  ↓
BookRepository
  ↓
Spring Data JPA
  ↓
Hibernate
  ↓
Database
```

Response:

```text
Database
  ↓
Hibernate
  ↓
Repository
  ↓
Service
  ↓
Controller
  ↓
Client
```

---

# 28. Day 08 Summary

Today I learned:

- JPA
- Spring Data JPA
- ORM
- Hibernate
- Entity
- Repository
- Service
- Controller
- `JpaRepository`
- `@Entity`
- `@Id`
- `@GeneratedValue`
- `findAll()`
- `findById()`
- `save()`
- `deleteById()`
- H2 Database
- `application.properties`
- Database configuration
- Java object to database mapping
- Complete Spring Boot database architecture

The most important architecture is:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Spring Data JPA
  ↓
Hibernate
  ↓
Database
```

Easy way to remember:

```text
Controller
→ Receives API request

Service
→ Handles business logic

Repository
→ Handles database operations

Entity
→ Represents database data

Hibernate
→ Maps Java objects and database operations

Database
→ Stores the actual data
```

---

# 29. GitHub Push

After saving this README file, run:

```bash
git status
```

Then:

```bash
git add .
```

Then:

```bash
git commit -m "Day 08: Learn Spring Data JPA and database integration"
```

Then:

```bash
git push origin main
```

---

# Day 08 Completed

Day 08 covers the basic connection between:

```text
Java
+
Spring Boot
+
Spring Data JPA
+
Hibernate
+
Database
```

The next step is to learn how to improve CRUD operations, especially:

```text
Update a record
Handle missing records
Optional
Custom Repository methods
Better API responses
```