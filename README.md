# EXp_03_-Entity-Student-and-build-a-CRUD-operations-using-Spring-Boot-Hibernate-Configuration

## AIM:
To develop a Spring Boot application that performs CRUD (Create, Read, Update, Delete) operations on a Student entity using Spring Data JPA (Hibernate).

## ALGORITHM:
Create Spring Boot Project

Add dependencies: Spring Web, Spring Data JPA, H2 Database or MySQL, Spring Boot DevTools

Configure application.properties

Define database connection

Enable Hibernate auto DDL

Create Student Entity Class

Annotate with @Entity

Define fields with @Id, @GeneratedValue, etc.

Create StudentRepository

Extend JpaRepository<Student, Long> for CRUD methods

Create StudentController

Handle HTTP methods:

POST /students → Add student

GET /students → Get all students

GET /students/{id} → Get student by ID

PUT /students/{id} → Update student

DELETE /students/{id} → Delete student

##PROGRAM CODE

### pom.xml
<dependencies>
    <!-- Spring Boot Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Boot JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- H2 Database (In-memory) -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
 ### application.properties

spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
### Student.java
```
package com.example.jpademo.model;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import lombok.Data;

@Data
@Entity
public class Student {
    @Id
    private int rno;
    private String name;
    private String gender;
    private String technology;
}

```
### StudentRepository.java
```
package com.example.jpademo.repository;

import com.example.jpademo.model.Student;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface StudentRepo extends JpaRepository<Student,Integer> {
    List<Student> findByTechnology(String tech);
    @Query(nativeQuery = true, value ="SELECT * from student WHERE gender=:gender AND technology=:technology")
    List<Student> findByGenderandTechnology(String gender,String technology);

}

```
###StudentService.java
```
package com.example.jpademo.service;

import com.example.jpademo.model.Student;
import com.example.jpademo.repository.StudentRepo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class StudentService {
    @Autowired
    StudentRepo studentRepo;
    public List<Student> getAllStudents() {
        return studentRepo.findAll();
    }

    public Student addStudent(Student student) {
        return studentRepo.save(student);
    }

    public Student getStudentsbyRno(int rno) {
        return studentRepo.findById(rno).orElse(new Student());
    }

    public Student updateStudent(Student student) {
        return studentRepo.save(student);

    }

    public void deletestudentbyid(int rno) {
        studentRepo.deleteById(rno);
    }

    public List<Student> getStudentsByTechbology(String tech) {
        return studentRepo.findByTechnology(tech);
    }

    public List<Student> findbygenderandtechnology(String gender, String technology) {
        return studentRepo.findByGenderandTechnology(gender,technology);
    }
}

```
### StudentController.java
```
package com.example.jpademo.controller;

import com.example.jpademo.model.Student;
import com.example.jpademo.service.StudentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.repository.query.Param;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
public class StudentController {
    @Autowired
    StudentService studentservice;
    @GetMapping("/students")
    public List<Student> getAllStudents() {
        return studentservice.getAllStudents();
    }
    @GetMapping("/students/{rno}")
    public Student getStudentsbyRno(@PathVariable int rno) {
        return studentservice.getStudentsbyRno(rno);
    }
    @PostMapping("/students")
    public Student addStudent(@RequestBody Student student) {
        return studentservice.addStudent(student);
    }
    @PutMapping("/students")
    public String updateStudent(@RequestBody Student student) {
        studentservice.updateStudent(student);
        return "updated Successfully";
    }
    @DeleteMapping("/students/{rno}")
    public String deleteStudent(@PathVariable int rno) {
        studentservice.deletestudentbyid(rno);
        return "deleted Successfully";
    }
    @GetMapping("/students/technology/{tech}")
    public List<Student> getStudentsByTechnology(@PathVariable String tech) {
        return studentservice.getStudentsByTechbology(tech);
    }
    @GetMapping("/students/filter")
    public List<Student> findbygenderandtechnology(@Param("gender") String gender,@Param("technology") String technology) {
        return studentservice.findbygenderandtechnology(gender,technology);
    }
}

```
### JpaDemoApplication.java
```
package com.example.jpademo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class JpademoApplication {

	public static void main(String[] args) {
		SpringApplication.run(JpademoApplication.class, args);
	}

}
```
### Output

<img width="1845" height="941" alt="image" src="https://github.com/user-attachments/assets/45309d0c-6aa1-4dea-9f3a-de59bd5c92b2" />
