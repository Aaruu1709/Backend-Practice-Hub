# Load Balancing – Theory + Practical (Spring Boot Microservices)

## Objective

Understand:

* What is Load Balancing
* Why do we use it
* Industry use case
* How Eureka and Load Balancer work together
* Practical implementation
* Interview questions

---

# 1. What is Load Balancing?

Load Balancing is a mechanism used to **distribute requests across multiple instances of the same microservice.**

Simple definition:

> Load Balancer distributes traffic so one server does not become overloaded.

---

# 2. Why Do We Use Load Balancing?

Suppose:

```text
DEPARTMENT-SERVICE → 8081
```

All requests go to:

```text
8081
```

Problem:

* High CPU usage
* Slow response
* Server crash risk
* No scalability

---

With Load Balancer:

```text
Request1 → 8081
Request2 → 8082
Request3 → 8083
```

Traffic gets distributed.

---

# 3. Industry Problem Solved

Suppose application receives:

```text
50,000 Requests
```

One service instance:

```text
ORDER-SERVICE → 8081
```

Cannot handle all traffic.

So company creates:

```text
ORDER-SERVICE

Instance1 → 8081
Instance2 → 8082
Instance3 → 8083
```

Load Balancer distributes requests.

Benefits:

✔ Scalability
✔ High Availability
✔ Better Performance
✔ Fault Tolerance

---

# 4. Load Balancer + Eureka

Important:

Eureka and Load Balancer are different.

```text
Eureka
↓

Returns all instances
```

Example:

```text
DEPARTMENT

8081
8082
8083
```

Load Balancer:

```text
Selects one instance
```

Example:

```text
Choose → 8082
```

---

# 5. Architecture

```text
Employee Service
↓

Feign
↓

Eureka
↓

Load Balancer
↓

Department Instance
```

Flow:

```text
Request
↓

Feign
↓

Eureka

(Find Services)

↓

Load Balancer

(Select Instance)

↓

Execute Request
```

---

# 6. Types of Load Balancing

## Client Side Load Balancer

Client selects instance.

Examples:

```text
Spring Cloud LoadBalancer
Feign
WebClient
```

Most used in Spring Boot.

---

## Server Side Load Balancer

Dedicated component distributes traffic.

Examples:

```text
Gateway
Nginx
```

---

# 7. Algorithms (Interview Important)

## Round Robin

```text
Request1 → 8081
Request2 → 8082
Request3 → 8083
```

Most common.

---

## Random

Random instance selection.

---

## Least Connection

Choose instance with minimum active requests.

---

# 8. Practical Implementation

Goal:

Run multiple instances of Department Service.

---

## Step 1 Run Eureka

Start:

```text
eureka-server
```

Open:

```text
http://localhost:8761
```

---

## Step 2 Run Department Service (Instance 1)

application.properties

```properties
server.port=8081

spring.application.name=DEPARTMENT-SERVICE

eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```

Run.

---

## Step 3 Run Another Instance

Create another run configuration.

Change VM Options:

```text
-Dserver.port=8082
```

Run again.

---

Run third instance:

```text
-Dserver.port=8083
```

Now Eureka Dashboard:

```text
DEPARTMENT-SERVICE

8081
8082
8083
```

---

# Step 4 Configure Employee Service

Dependencies:

```text
Spring Web
OpenFeign
Eureka Discovery Client
```

---

Enable Feign:

```java
@EnableFeignClients
@SpringBootApplication
```

---

## Create Feign Client

```java
@FeignClient(name="DEPARTMENT-SERVICE")
public interface DepartmentClient {

@GetMapping("/dept")
String getDepartment();

}
```

Notice:

```text
No localhost

No port
```

Only service name.

---

## Employee Controller

```java
@RestController
public class EmployeeController {

@Autowired
private DepartmentClient client;

@GetMapping("/employee")
public String getData(){

return client.getDepartment();

}

}
```

---

# Step 5 Verify Load Balancing

Department Controller:

```java
@RestController
@RequestMapping("/dept")
public class DepartmentController {

@Value("${server.port}")
private String port;

@GetMapping
public String getDept(){

return "Handled by Port : " + port;

}

}
```

Call:

```text
http://localhost:8082/employee
```

Multiple times.

Output:

```text
Handled by Port : 8081

Handled by Port : 8082

Handled by Port : 8083
```

Meaning:

Load Balancing works.

---

# 9. Failure Scenario

Stop:

```text
8082
```

Call API.

Output:

```text
8081
8083
```

Application still works.

Reason:

Load Balancer skips failed instance.

---

# 10. Interview Questions

### What is Load Balancing?

Distributing requests among multiple instances.

---

### Why use Load Balancing?

Scalability and high availability.

---

### Does Eureka perform Load Balancing?

No.

Eureka provides service locations.

Load Balancer selects instance.

---

### How does Feign support Load Balancing?

Feign uses Spring Cloud LoadBalancer internally.

---

### How do you implement Load Balancing?

Run multiple instances → register in Eureka → call by service name.

---

# Key Takeaway

```text
Eureka
↓

Find Services

Load Balancer
↓

Choose Instance

Feign
↓

Execute Request
```
