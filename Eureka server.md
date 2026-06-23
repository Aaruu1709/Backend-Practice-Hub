Topic 1: Eureka Server (tool)
--------------------
1. What is Eureka?
Eureka is a Service Registry server used in microservices to allow services to register themselves
and discover other services dynamically.
Eureka is a central directory where all microservices write their address and other services can find them.
Example:

EMPLOYEE-SERVICE → localhost:8081
DEPARTMENT-SERVICE → localhost:8082
PAYMENT-SERVICE → localhost:8083

   2. Why do we use Eureka?
 Employee Service
↓
http://localhost:8082/api/dept

Problem:

Port changes
Server changes
Service scales
URLs become hardcoded

port change department -> 8082  , 8083 then employee service will be bereaks
With Eureka:

Employee Service
↓
DEPARTMENT-SERVICE(here we use name of service not url)
↓
Eureka finds location
↓
Call success

**No hardcoded URL**
```
Now service calls:
http://PAYMENT-SERVICE

instead of:

http://192.168.0.100:8087
```
how to use:
@FeignClient(name="DEPARTMENT-SERVICE")

or

http://DEPARTMENT-SERVICE
Eureka stores:

Service Name
IP Address
Port
Status
Instance ID
Heartbeat

eureka only provide location
Eureka = Register + Discover
Eureka itself never calls services. It only tells WHERE service exists.
Important Terminologies
Eureka Server

Stores all registered services.

Example:

EMPLOYEE-SERVICE → 8081

DEPARTMENT-SERVICE → 8082
Eureka Client

Microservice which registers into Eureka.

Example:

Employee Service

Department Service
Service Discovery

Process of finding service location.

Example:

Employee
↓

Find Department
↓

Eureka

----------------------------------------------------------------------------------

# Eureka Server + Service Discovery (Spring Boot Microservices)

## Objective

Build a practical microservices setup using:

* Eureka Server
* Employee Service
* Department Service
* OpenFeign

Goal:
Register services into Eureka and enable service-to-service communication without hardcoded URLs.

---

# Architecture

```text
Employee Service
        ↓
     Eureka
        ↓
Department Service
```

Flow:

```text
Employee Service
↓
Feign Client
↓
Eureka Server
↓
Department Service
↓
Response
```

---

# Step 1: Create Eureka Server

Project Name:

```text
eureka-server
```

Dependencies:

```text
Spring Web
Eureka Server
Spring Boot Actuator
Lombok (Optional)
```

---

## Enable Eureka Server

Main Class:

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

public static void main(String[] args) {
SpringApplication.run(EurekaServerApplication.class,args);
}

}
```

---

## Configure application.properties

```properties
server.port=8761

spring.application.name=EUREKA-SERVER

eureka.client.register-with-eureka=false

eureka.client.fetch-registry=false
```

Explanation:

```text
register-with-eureka=false
→ Eureka should not register itself

fetch-registry=false
→ Eureka should not fetch registry
```

---

## Run Eureka

Open:

```text
http://localhost:8761
```

Expected:

```text
No instances available
```

---

# Step 2: Create Department Service

Project Name:

```text
department-service
```

Dependencies:

```text
Spring Web
Eureka Discovery Client
Spring Boot Actuator
Lombok
```

---

## Configure application.properties

```properties
server.port=8081

spring.application.name=DEPARTMENT-SERVICE

eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```

---

## Create Controller

```java
@RestController
@RequestMapping("/dept")
public class DepartmentController {

@GetMapping
public String getDepartment(){

return "Department Running";

}

}
```

---

## Run Department Service

Open Eureka Dashboard:

```text
http://localhost:8761
```

Expected:

```text
DEPARTMENT-SERVICE
UP
```

Meaning:

```text
Service registered successfully
```

---

# Step 3: Create Employee Service

Project Name:

```text
employee-service
```

Dependencies:

```text
Spring Web
Eureka Discovery Client
OpenFeign
Spring Boot Actuator
```

---

## Configure application.properties

```properties
server.port=8082

spring.application.name=EMPLOYEE-SERVICE

eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```

---

## Enable Feign

Main Class:

```java
@SpringBootApplication
@EnableFeignClients
public class EmployeeServiceApplication {

public static void main(String[] args) {
SpringApplication.run(EmployeeServiceApplication.class,args);
}

}
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

Explanation:

```text
Feign asks Eureka
↓
Eureka returns service location
↓
Request executed
```

---

## Create Employee Controller

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

# Step 4: Run All Services

Start order:

```text
1. Eureka Server
2. Department Service
3. Employee Service
```

Open:

```text
http://localhost:8761
```

Expected:

```text
DEPARTMENT-SERVICE

EMPLOYEE-SERVICE
```

---

# Test API

Call:

```text
GET
http://localhost:8082/employee
```

Response:

```text
Department Running
```

---

# Practical Proof Eureka Works

Change Department Service port:

```text
8081 → 9090
```

Restart.

Call:

```text
http://localhost:8082/employee
```

Result:

```text
Still works
```

Reason:

```text
Employee Service does not use hardcoded URL.
It uses service name.

Eureka dynamically resolves location.
```

---

# Interview Questions

### What is Eureka?

Service registry used for service discovery.

---

### Why Eureka?

To avoid hardcoded URLs.

---

### Does Eureka make API calls?

No.
It only provides service location.

---

### How does Employee Service find Department Service?

Using Feign + Eureka.

---

### What problem does Eureka solve?

Dynamic service discovery and loose coupling.

---

# Key Takeaway

```text
Eureka
=
Register + Discover
```

