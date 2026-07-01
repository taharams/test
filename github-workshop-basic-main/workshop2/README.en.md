# Getting started
日本語 [README](README.ja.md)  
한국어 [README](README.kr.md)

## How to run the application

I use `mvn clean spring-boot:run` to run the application. Alternatively, you can run the application from your IDE, or build the application and run the jar file from your terminal or deploy to a web server.

### Application URL

The base url for the application is `http://localhost:8080/employees`

- Static endpoint to get employees: `http://localhost:8080/employees/all`
- Endpoint to get employees from the database: `http://localhost:8080/employees`
- Endpoint to add an employee: `http://localhost:8080/employees/add`

## Application configuration

### application.properties

1. Go to `src/main/resources` and update the file `application.properties`. 
2. Add the following content:

```properties
spring.datasource.url=jdbc:h2:./data/test
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

### main class

Main class is `com.example.workshop2.Workshop2Application` located in directory `src/main/java/com/example/workshop2`. 

No action needed in this class.

# Demo 1: Add a controller 

In this workshop2, we will create a simple Spring Boot controller that returns a static list of employee names.

1. Create a directory named `controller` in the directory `src/main/java/com/example/workshop2`
2. Add a controller empty class `com.example.demo.controller.EmployeeController`
3. Add in comments to the class to describe what you want to do. (see below)
4. Accept or reject the suggestions it gives.

```java
// create a new controller class called EmployeeController
// and annotate it with @RestController
// add RequestMapping to /employees
```

<details>
<summary>Possible GitHub Copilot Suggestion</summary>

```java
// create a new controller class called EmployeeController
// and annotate it with @RestController
// add RequestMapping to /employees

package com.example.workshop2.controller;

import java.util.ArrayList;
import java.util.List;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @GetMapping("/all")
    public List<String> getEmployees() {
        System.err.println("getEmployees called");
        List<String> employees = new ArrayList<String>();
        employees.add("John");
        employees.add("Jane");
        employees.add("Jack");
        employees.add("Jill");
        return employees;
    }
}
```
</details>


# Demo 2: Improve our API to get data from the database

## Creating an entity class and a repository

We will improve our API to get data from the database. To do so, we will ask Copilot to suggeest the code for `Employee` entity, `EmployeeRepository`. 

First create a class in `com.example.workshop2.model` package called `Employee` and ask Copilot to suggest the code for the class. There are 2 ways to ask Copilot to suggest the code for the class: Chat or inline suggestions.

### Steps: Part One

1. Create a folder named `model` in the directory `src/main/java/com/example/workshop2`
2. Create `Employee.java` in the directory you just created.
3. Add in comments to describe what you want to do. (see below)

Prompt to Copilot:

```text
create an entity class Employee using lombok
"@Data: generate getters and setters
@AllArgsConstructor: generate constructor with all fields
@NoArgsConstructor: generate constructor with no fields
@Entity: mark this class as an entity class
@Table: map this class to a table named employees
@Id: mark this field as the primary key
@GeneratedValue: generate the value for this field automatically
@Column: map this field to a column named id
@Column: map this field to a column named name
@Column: map this field to a column named email
"
```

<details>
<summary>This is the code suggested by Copilot</summary>

```java
package com.example.workshop2.model;

import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import jakarta.persistence.Id;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.Column;
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "employees")
public class Employee {

  @Id
  @GeneratedValue
  @Column(name = "id")
  private Long id;

  @Column(name = "name")
  private String name;

  @Column(name = "email")
  private String email;
}
```
</details>

### Steps: Part Two

1. Create a new class `EmployeeRepository` in the package `com.example.demo.model` 
2. Ask Copilot to suggest the code for the class. 

This is the question I asked Copilot:

```text
 create EmployeeRepository JPA repository interface managing Employee entity class in the com.example.demo.model package
```

<details>
<summary>This is the code suggested by Copilot</summary>

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```
</details>

## Include the repository in the controller

Now we need to include the repository in the controller. I asked Copilot to suggest the code for the controller. This is the question I asked Copilot:

```text
create a new method in the EmployeeController to get all employees from the database using the EmployeeRepository
```

<details>
<summary>This is the code suggested by Copilot</summary>

```java
@Autowired
private EmployeeRepository employeeRepository;

@GetMapping
public List<Employee> getAllEmployees() {
    return employeeRepository.findAll();
}
```
</details>

### add a new endpoint to add a new employee

Asked Copilot to suggest the code for the controller to add a new employee. This is the question I asked Copilot:

```Java
    // Create a new endpoint to add a new employee to the database endpoint is /add
    // The method should accept an HTTP POST request and a request body of type Employee
    // The method should return the added employee
```

<details>
<summary>This is the code suggested by Copilot</summary>
```Java
    @PostMapping("/add")
    public Employee addEmployee(@RequestBody Employee employee) {
        return employeeRepository.save(employee);
    }
```
</details>

### adding a new employee

Curl command to add a new employee:

```bash
curl --location 'http://localhost:8080/employees/add' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "siavash",
    "email": "siavash@mail.com"
}'
```

### list all employees

Curl command to list all employees:

```bash
curl --location --request GET 'http://localhost:8080/employees/employees'
```

# Demo 3: Add more endpoints to the API

## Listing a single employee
Add an API to obtain information on a single employee by specifying his/her id.
From this point on, I dare not write any prompts, so you can operate it by yourself. Since Copilot has learned a lot by writing the code up to this point, it may add a method just by writing a new line.

<details>
<summary>Listing a single employee</summary>
</br>
I asked Copilot to suggest the code for the controller to list a single employee. This is the question I asked Copilot:

```java
// Create a new endpoint to get a single employee from the database by id path: /employees/{id}
// The endpoint should accept an HTTP GET request and a path variable of type Long
// The endpoint should return the employee with the given id

@GetMapping("/{id}")
public Employee getEmployeeById(@PathVariable Long id) {
    return employeeRepository.findById(id).orElse(null);
}
```
</details>

## update an employee

<details>
<summary>Updating an employee</summary>
</br>
I asked Copilot to suggest the code for the controller to update an employee. This is the question I asked Copilot:

```java
// Create a new endpoint to update an employee in the database by id path: /employees/{id}
// The endpoint should accept an HTTP PUT request, a path variable of type Long, and a request body of type Employee
// The endpoint should return the updated employee

@PutMapping("/{id}")
public Employee updateEmployee(@PathVariable Long id, @RequestBody Employee employee) {
    Employee existingEmployee = employeeRepository.findById(id).orElse(null);
    if (existingEmployee != null) {
        existingEmployee.setName(employee.getName());
        existingEmployee.setEmail(employee.getEmail());
        return employeeRepository.save(existingEmployee);
    }
    return null;
}
```

Curl command to update an employee:

```bash
curl --location --request PUT 'http://localhost:8080/employees/1' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "john",
    "email": "john@mail.com"
}'
```

</details>

## delete an employee

<details>
<summary>Deleting an employee</summary>
</br>
I asked Copilot to suggest the code for the controller to delete an employee. This is the question I asked Copilot:

```java 
// Create a new endpoint to delete an employee from the database by id path: /employees/{id}
// The endpoint should accept an HTTP DELETE request and a path variable of type Long
// The endpoint should return the deleted employee

@DeleteMapping("/{id}")
public Employee deleteEmployee(@PathVariable Long id) {
    Employee existingEmployee = employeeRepository.findById(id).orElse(null);
    if (existingEmployee != null) {
        employeeRepository.delete(existingEmployee);
        return existingEmployee;
    }
    return null;
}
```

Curl command to delete an employee:

```bash
    curl --location --request DELETE 'http://localhost:8080/employees/1'
```
</details>