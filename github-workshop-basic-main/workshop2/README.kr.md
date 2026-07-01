# 시작하기

## 애플리케이션을 실행하는 방법

저는 `mvn clean spring-boot:run`을 사용하여 애플리케이션을 실행합니다. 또는 IDE에서 애플리케이션을 실행하거나 애플리케이션을 빌드한 후 터미널에서 jar 파일을 실행하거나 웹 서버에 배포할 수 있습니다.

### 애플리케이션 URL

애플리케이션의 기본 URL은 `http://localhost:8080/employees`입니다.

- 직원을 받기 위한 정적 엔드포인트 `http://localhost:8080/employees/all`
- 데이터베이스에서 직원을 가져오는 엔드포인트: `http://localhost:8080/employees`
- 직원을 추가할 엔드포인트: `http://localhost:8080/employees/add`

## 애플리케이션 구성

### application.properties

1. src/main/resources`로 이동하여 `application.properties` 파일을 업데이트합니다. 
2. 다음 내용을 추가합니다:

```properties
spring.datasource.url=jdbc:h2:./data/test
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

### 메인 클래스

메인 클래스는 `src/main/java/com/example/workshop2` 디렉터리에 있는 `com.example.workshop2.Workshop2Application`입니다. 

이 클래스에서는 별도의 작업이 필요하지 않습니다.

# 데모 1: 컨트롤러 추가하기 

이 워크샵2에서는 직원 이름의 정적 목록을 반환하는 간단한 Spring Boot 컨트롤러를 만들어 보겠습니다.

1. src/main/java/com/example/workshop2` 디렉토리에 `controller`라는 이름의 디렉토리를 생성합니다.
2. 컨트롤러 빈 클래스 `com.example.demo.controller.EmployeeController`를 추가합니다.
3. 클래스에 주석을 추가하여 수행하려는 작업을 설명합니다. (아래 참조)
4. 제안을 수락하거나 거부합니다.

```java
// EmployeeController라는 새 컨트롤러 클래스를 만들고
// 라는 새 컨트롤러 클래스를 생성하고 @RestController로 주석을 달아요.
// 요청 매핑을 /employees에 추가합니다.
```

<details>
<summary>가능한 GitHub 코파일럿 제안</summary>

```java
// EmployeeController라는 컨트롤러 클래스를 새로 생성하고
// 라는 새 컨트롤러 클래스를 생성하고 @RestController로 주석을 달아요.
// 요청 매핑을 /employees에 추가합니다.

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


# 데모 2: 데이터베이스에서 데이터를 가져오도록 API 개선하기

## 엔티티 클래스 및 리포지토리 생성하기

데이터베이스에서 데이터를 가져올 수 있도록 API를 개선하겠습니다. 이를 위해 Copilot에 `Employee` 엔티티인 `EmployeeRepository`에 대한 코드를 제안해 달라고 요청하겠습니다. 

먼저 `com.example.workshop2.model` 패키지에 `Employee`라는 클래스를 생성하고 Copilot에게 해당 클래스의 코드를 제안하도록 요청합니다. Copilot에게 클래스의 코드를 제안하도록 요청하는 방법에는 두 가지가 있습니다: 채팅 또는 인라인 제안.

### 단계: 1단계

1. src/main/java/com/example/workshop2` 디렉토리에 `model` 폴더를 생성합니다.
2. 방금 생성한 디렉토리에 `Employee.java`를 생성합니다.
3. 3. 수행하려는 작업을 설명하는 주석을 추가합니다. (아래 참조)

부조종사에게 프롬프트합니다:

```text
롬복을 사용하여 Employee 엔티티 클래스 만들기
"@Data: 게터 및 세터 생성
모든 필드가 있는 생성자를 생성합니다.
NoArgsConstructor: 필드가 없는 생성자 생성
@Entity: 이 클래스를 엔티티 클래스로 표시
@Table: 이 클래스를 employees라는 테이블에 매핑합니다.
Id: 이 필드를 기본 키로 표시합니다.
생성된 값: 이 필드의 값을 자동으로 생성합니다.
@Column: 이 필드를 id라는 열에 매핑합니다.
Column: 이 필드를 이름이라는 열에 매핑합니다.
Column: 이 필드를 이메일이라는 열에 매핑합니다.
"
```

<details>
<summary>이 코파일럿이 제안한 코드입니다</summary>

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

### 단계: 파트 2

1. com.example.demo.model` 패키지에 새 클래스 `EmployeeRepository`를 만듭니다. 
2. Copilot에게 클래스에 대한 코드를 제안해 달라고 요청합니다. 

다음은 제가 Copilot에게 물어본 질문입니다:

```text
 com.example.demo.model 패키지에서 Employee 엔티티 클래스를 관리하는 JPA 리포지토리 인터페이스 EmployeeRepository 만들기
```

<details>
<summary>이 코파일럿이 제안한 코드입니다</summary>

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```
</details>

## 컨트롤러에 리포지토리 포함하기

이제 컨트롤러에 리포지토리를 포함시켜야 합니다. 코파일럿에게 컨트롤러에 대한 코드를 제안해 달라고 요청했습니다. 다음은 제가 Copilot에게 물어본 질문입니다:

```text
EmployeeRepository를 사용하여 데이터베이스에서 모든 직원을 가져오는 EmployeeController에 새 메서드를 만듭니다.
```

<details>
<summary>이 코파일럿이 제안한 코드입니다</summary>

```java
@Autowired
private EmployeeRepository employeeRepository;

@GetMapping
public List<Employee> getAllEmployees() {
    return employeeRepository.findAll();
}
```
</details>

### 새 엔드포인트를 추가하여 새 직원 추가하기

Copilot에게 컨트롤러가 새 직원을 추가할 수 있는 코드를 제안해 달라고 요청했습니다. 다음은 제가 Copilot에게 물어본 질문입니다:

```Java
    // 데이터베이스 엔드포인트에 새 직원을 추가하기 위해 새 엔드포인트를 만듭니다.
    // 이 메서드는 HTTP POST 요청과 Employee 유형의 요청 본문을 수락해야 합니다.
    // 메서드는 추가된 직원을 반환해야 합니다.
```

<details>
<summary>이 코파일럿이 제안한 코드입니다</summary>
```Java
    @PostMapping("/add")
    public Employee addEmployee(@RequestBody Employee employee) {
        return employeeRepository.save(employee);
    }
```
</details>

### 새 직원 추가하기

Curl 명령을 사용하여 새 직원을 추가합니다:

```bash
curl --location 'http://localhost:8080/employees/add' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "siavash",
    "email": "siavash@mail.com"
}'
```

### 모든 직원 목록

모든 직원을 나열하는 Curl 명령입니다:

```bash
curl --location --request GET 'http://localhost:8080/employees/employees'
```

# 데모 3: API에 더 많은 엔드포인트 추가하기

## 직원 한 명 나열하기
직원 한 명의 아이디를 지정하여 해당 직원의 정보를 가져오는 API를 추가합니다.
이 시점부터는 프롬프트를 작성하지 않으므로 직접 조작할 수 있습니다. 코파일럿은 여기까지 코드를 작성하면서 많은 것을 배웠기 때문에 새로운 줄을 작성하는 것만으로도 메서드를 추가할 수 있습니다.

<details>
<summary>직원 한 명 나열하기</summary>
</br>
코파일럿에게 컨트롤러가 직원 한 명을 나열하는 코드를 제안해 달라고 요청했습니다. 다음은 제가 Copilot에게 물어본 질문입니다:

```java
// 아이디 경로로 데이터베이스에서 단일 직원을 가져오는 새 엔드포인트를 만듭니다: /employees/{id}
// 엔드포인트는 HTTP GET 요청과 Long 유형의 경로 변수를 수락해야 합니다.
// 엔드포인트는 주어진 아이디를 가진 직원을 반환해야 합니다.

@GetMapping("/{id}")
public Employee getEmployeeById(@PathVariable Long id) {
    return employeeRepository.findById(id).orElse(null);
}
```
</details>

## 직원 업데이트

<details>
<summary>직원 업데이트하기</summary>
</br>
Copilot에게 컨트롤러가 직원을 업데이트할 수 있는 코드를 제안해 달라고 요청했습니다. 다음은 제가 Copilot에게 물어본 질문입니다:

```java
// 아이디 경로로 데이터베이스에서 직원을 업데이트하는 새 엔드포인트를 만듭니다: /employees/{id}
// 엔드포인트는 HTTP PUT 요청, Long 유형의 경로 변수, Employee 유형의 요청 본문을 수락해야 합니다.
// 엔드포인트는 업데이트된 직원을 반환해야 합니다.

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

Curl 명령을 사용하여 직원을 업데이트합니다:

```bash
curl --location --request PUT 'http://localhost:8080/employees/1' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "john",
    "email": "john@mail.com"
}'
```

</details>

## 직원 삭제

<details>
<summary>직원 삭제하기</summary>
</br>
Copilot에게 관리자가 직원을 삭제할 수 있는 코드를 제안해 달라고 요청했습니다. 다음은 제가 Copilot에게 물어본 질문입니다:

```java 
// 아이디 경로로 데이터베이스에서 직원을 삭제하는 새 엔드포인트를 만듭니다: /employees/{id}
// 엔드포인트는 HTTP DELETE 요청과 Long 유형의 경로 변수를 수락해야 합니다.
// 엔드포인트는 삭제된 직원을 반환해야 합니다.

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

직원을 삭제하는 Curl 명령:

```bash
    curl --location --request DELETE 'http://localhost:8080/employees/1'
```
</details>