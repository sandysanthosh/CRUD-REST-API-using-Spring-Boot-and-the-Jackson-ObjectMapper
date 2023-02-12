# CRUD-REST-API-using-Spring-Boot-and-the-Jackson-ObjectMapper


#### Pom.xml:

```

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
</dependencies>


```

### Pojo:

```
public class Employee {
    private long id;
    private String firstName;
    private String lastName;

    // getters and setters...
}

```

#### Create a repository to store and retrieve data:

```

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.atomic.AtomicLong;

import org.springframework.stereotype.Repository;

@Repository
public class EmployeeRepository {
    private final List<Employee> employees = new ArrayList<>();
    private final AtomicLong counter = new AtomicLong();

    public List<Employee> findAll() {
        return employees;
    }

    public Employee findById(long id) {
        return employees.stream()
                .filter(employee -> employee.getId() == id)
                .findFirst()
                .orElse(null);
    }

    public Employee save(Employee employee) {
        employee.setId(counter.incrementAndGet());
        employees.add(employee);
        return employee;
    }

    public void deleteById(long id) {
        employees.removeIf(employee -> employee.getId() == id);
    }
}


```

#### Create a REST controller to handle HTTP requests::


```

import java.util.List;

import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/employees")
public class EmployeeController {
    private final EmployeeRepository repository;

    public EmployeeController(EmployeeRepository repository) {
        this.repository = repository;
    }

    @GetMapping
    public List<Employee> findAll() {
        return repository.findAll();
    }

    @GetMapping("/{id}")
    public Employee findById(@PathVariable long id) {
        return repository.findById(id);
    }

    @PostMapping
    public Employee save(@RequestBody Employee employee) {
        return repository.save(employee);
    }

    @PutMapping("/{id}")
    public Employee update(@PathVariable long id, @RequestBody Employee employee) {
        Employee existingEmployee = repository.findById(id);
        if (existingEmployee == null) {
            return null;
        }
        existingEmployee.setFirstName(employee.getFirstName());
        existingEmployee.setLastName(employee.getLastName());
        return repository.save(existingEmployee);
    }

    @DeleteMapping("/{id}")
    public void deleteById(@PathVariable long id) {
        repository.deleteById(id);
    }
}


```

In this example, the EmployeeController class uses the @RestController annotation to indicate that it is a RESTful web service.

The @RequestMapping annotation maps the class to a URL, in this case /employees. 

The class includes methods to handle the basic CRUD operations (Create, Read, Update, Delete) using HTTP methods (GET, POST, PUT, DELETE). 

The @PathVariable annotation is used to bind a URL path parameter to a method argument, and the @RequestBody annotation is used to bind the body of an HTTP request to a method argument.

With this code, you can start the Spring Boot application and perform CRUD operations on Employee objects by sending HTTP requests to the appropriate URLs. 

You can use tools like Postman or cURL to test your API.
