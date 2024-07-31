# Spring Boot REST API Project
Here are the badges for Java and Spring Boot:

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=java&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)

This project demonstrates the creation of a simple back-end application using Spring Boot to implement a REST API. The primary focus of this project is to learn how to use Spring Boot for building robust and scalable RESTful web services.

## Project Description

The project involves managing student records through a REST API. It covers fundamental concepts of Spring Boot, including creating entities, repositories, services, and controllers. It also demonstrates how to configure the application and manage dependencies.

## Main Concepts

### 1. Spring Boot

Spring Boot simplifies the development of Java applications by providing a comprehensive framework that allows you to create standalone applications with minimal configuration. It includes embedded servers, such as Tomcat, and various starters to streamline the integration of different components.

### 2. REST API

A REST API (Representational State Transfer) allows communication between client and server using HTTP requests. In this project, the REST API handles CRUD (Create, Read, Update, Delete) operations for managing student records.

### 3. Entity

An entity represents a table in a database. In this project, the `Student` class is the entity that maps to a table storing student information.

```java
// Student.java
package com.example.demo.student;

import javax.persistence.*;
import java.time.LocalDate;
import java.time.Period;

@Entity
@Table
public class Student {
    @Id
    @SequenceGenerator(
            name = "student_sequence",
            sequenceName = "student_sequence",
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.SEQUENCE,
            generator = "student_sequence"
    )
    private Long id;
    private String name;
    private String email;
    private LocalDate dob;
    @Transient
    private Integer age;

    // Getters and setters
}
```

### 4. Repository

The repository is an interface that extends `JpaRepository`, providing various methods for performing database operations. It simplifies data access by abstracting common CRUD operations.

```java
// StudentRepository.java
package com.example.demo.student;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

### 5. Service

The service layer contains business logic and handles data processing. In this project, `StudentService` contains methods to retrieve, add, and delete students.

```java
// StudentService.java
package com.example.demo.student;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class StudentService {
    private final StudentRepository studentRepository;

    @Autowired
    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    public List<Student> getStudents() {
        return studentRepository.findAll();
    }

    public void addNewStudent(Student student) {
        studentRepository.save(student);
    }

    public void deleteStudent(Long studentId) {
        boolean exists = studentRepository.existsById(studentId);
        if (!exists) {
            throw new IllegalStateException("student with id " + studentId + " does not exist");
        }
        studentRepository.deleteById(studentId);
    }
}
```

### 6. Controller

The controller handles HTTP requests and maps them to corresponding service methods. It acts as the interface between the client and the server.

```java
// StudentController.java
package com.example.demo.student;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping(path = "api/v1/student")
public class StudentController {
    private final StudentService studentService;

    @Autowired
    public StudentController(StudentService studentService) {
        this.studentService = studentService;
    }

    @GetMapping
    public List<Student> getStudents() {
        return studentService.getStudents();
    }

    @PostMapping
    public void registerNewStudent(@RequestBody Student student) {
        studentService.addNewStudent(student);
    }

    @DeleteMapping(path = "{studentId}")
    public void deleteStudent(@PathVariable("studentId") Long studentId) {
        studentService.deleteStudent(studentId);
    }
}
```

### 7. Configuration

Configuration classes, such as `StudentConfig`, are used to set up initial data and customize Spring Boot behavior.

```java
// StudentConfig.java
package com.example.demo.student;

import org.springframework.boot.CommandLineRunner;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.time.LocalDate;
import java.time.Month;
import java.util.List;

@Configuration
public class StudentConfig {
    @Bean
    CommandLineRunner commandLineRunner(StudentRepository repository) {
        return args -> {
            Student mariam = new Student(
                    "Mariam",
                    "mariam.jamal@gmail.com",
                    LocalDate.of(2000, Month.JANUARY, 5)
            );
            Student alex = new Student(
                    "Alex",
                    "alex.jamal@gmail.com",
                    LocalDate.of(2004, Month.JANUARY, 5)
            );

            repository.saveAll(
                    List.of(mariam, alex)
            );
        };
    }
}
```

## Maven and Project Setup

### pom.xml

The `pom.xml` (Project Object Model) file is used by Maven to manage project dependencies, build configuration, and other project settings. It defines the project structure and manages the lifecycle of the project.

Key dependencies in this project:
- `spring-boot-starter-data-jpa`: Provides Spring Data JPA support.
- `spring-boot-starter-web`: Includes all the dependencies required to create a web application.
- `spring-boot-starter-test`: Includes testing libraries.

Example of a basic `pom.xml`:
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
        <relativePath /> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### mvnw and mvnw.cmd

The `mvnw` and `mvnw.cmd` files are Maven Wrapper scripts. The Maven Wrapper allows you to run Maven commands without having Maven installed on your machine. It ensures that the correct version of Maven is used for your project.

- `mvnw`: Script for Unix-based systems (Linux, macOS).
- `mvnw.cmd`: Script for Windows systems.

## Cloning and Running the Project

To clone and run the project locally:

1. Clone the repository:
    ```sh
    git clone <repository-url>
    ```

2. Navigate to the project directory:
    ```sh
    cd <project-directory>
    ```

3. Run the application using Maven Wrapper:
    ```sh
    ./mvnw spring-boot:run   # For Unix-based systems
    mvnw.cmd spring-boot:run # For Windows systems
    ```

## Conclusion

This project demonstrates the use of Spring Boot to create a REST API for managing student records. Key concepts such as entities, repositories, services, and controllers are implemented to provide a complete understanding of building a Spring Boot application. The project setup includes the use of Maven for dependency management and build configuration, ensuring a streamlined development process.