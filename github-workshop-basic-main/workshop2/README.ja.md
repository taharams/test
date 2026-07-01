# はじめに

## アプリケーションの実行方法

私は `mvn clean spring-boot:run` を使用してアプリケーションを実行します。代わりに、IDEからアプリケーションを実行したり、アプリケーションをビルドしてjarファイルをターミナルから実行したり、ウェブサーバーにデプロイしたりすることもできます。

### アプリケーションURL

アプリケーションのベースURLは `http://localhost:8080/employees` です。

- 従業員を取得する静的エンドポイント: `http://localhost:8080/employees`
- データベースから従業員を取得するエンドポイント: `http://localhost:8080/employees/`
- 従業員を追加するエンドポイント: `http://localhost:8080/employees/add`

## アプリケーションの設定

### application.properties

1. `src/main/resources` に移動し、`application.properties` ファイルを更新します。
2. 以下の内容を追加します。

```properties
spring.datasource.url=jdbc:h2:./data/test
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

### メインクラス

メインクラスは `com.example.workshop.Workshop2Application` で、ディレクトリ `src/main/java/com/example/workshop2` に位置しています。

このクラスでは何もアクションは必要ありません。

# デモ1: コントローラーの追加

このデモでは、従業員の名前の静的リストを返すシンプルなSpring Bootコントローラーを作成します。

1. `src/main/java/com/example/workshop` ディレクトリに `controller` という名前のディレクトリを作成します。
2. 空のコントローラークラス `com.example.workshop.controller.EmployeeController` を追加します。
3. クラスにコメントを追加して、何をしたいのかを説明します（下記参照）。
4. 提案されたものを受け入れるか、拒否します。

```java
// EmployeeControllerという新しいコントローラークラスを作成し、
// それを@RestControllerで注釈します
// /employeesにRequestMappingを追加します
```

<details>
<summary>GitHub Copilotの提案可能性</summary>

```java
// EmployeeControllerという新しいコントローラークラスを作成し、
// それを@RestControllerで注釈します
// /employeesにRequestMappingを追加します

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


# デモ2: データベースからデータを取得するAPIを改善する

## エンティティクラスとリポジトリの作成

APIを改善してデータベースからデータを取得するようにします。そのために、Copilotに `Employee` エンティティ、`EmployeeRepository` のコードを提案してもらいます。

まず、`com.example.workshop2.model` パッケージに `Employee` というクラスを作成し、Copilotにクラスのコードを提案してもらいます。Copilotにクラスのコードを提案してもらう方法は2つあります：チャットまたはインライン提案。

### ステップ: パート1
1. `src/main/java/com/example/workshop2` ディレクトリに `model` という名前のフォルダを作成します。
2. 作成したディレクトリに `Employee.java` を作成します。
3. Copilot Chatに以下プロンプトを入力してコードを生成します

Copilotへのプロンプト：

```text
lombokを使用してEmployeeエンティティクラスを作成します。
"@Data: ゲッターとセッターを生成します
@AllArgsConstructor: 全フィールドを持つコンストラクタを生成します
@NoArgsConstructor: フィールドを持たないコンストラクタを生成します
@Entity: このクラスをエンティティクラスとしてマークします
@Table: このクラスをemployeesという名前のテーブルにマッピングします
@Id: このフィールドを主キーとしてマークします
@GeneratedValue: このフィールドの値を自動生成します
@Column: このフィールドをidという名前のカラムにマッピングします
@Column: このフィールドをnameという名前のカラムにマッピングします
@Column: このフィールドをemailという名前のカラムにマッピングします
"
この時、javaxパッケージではなくjakartaパッケージをインポートしてください。
```

<details>
<summary>GitHub Copilotの提案可能性</summary>

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
2. Copilot Chatに以下を聞いてください

Copilot へのプロンプト:

```text
@workspace EmployeeRepositoryクラスを追加して
```

<details>
<summary>This is the code suggested by Copilot</summary>

```java
mport org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```
</details>

## Include the repository in the controller
次に、コントローラにリポジトリをインクルードする必要があります。Copilotにコントローラのコードを提案しましょう。

```text
@workspace EmployeeController に新しいメソッドを作成し、EmployeeRepository を使用してデータベースからすべての従業員を取得します。
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

### 新しい従業員を追加するための新しいエンドポイントを追加

Copilotに、新しい従業員を追加するコントローラーのコードを提案してもらいましょう

```Java
新しい従業員をデータベースに追加するための新しいエンドポイントを作成します。
メソッドは、HTTP POST リクエストと Employee 型のリクエスト・ボディを受け付ける必要があります。
メソッドは追加された従業員を返します
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

### 従業員の追加

curlで従業員を追加します

```bash
curl --location 'http://localhost:8080/employees/add' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "siavash",
    "email": "siavash@mail.com"
}'
```

### 全従業員の取得

Curlで全従業員を取得

```bash
curl --location --request GET 'http://localhost:8080/employees'
```

# Demo 3: APIエンドポイントをもっと追加する

## Listing a single employee
idを指定して従業員一人の情報を取得するAPIを追加します。
ここからはあえてプロンプトなどは書きませんので各々操作してみてください。ここまでのコードを記載してCopilotも学習が進んでいるので、ただ改行するだけでメソッドを追加してくれるかもしれません。

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
従業員を更新します

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
従業員を削除します。
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