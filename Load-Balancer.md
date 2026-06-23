

# Load Balancer – Complete Theory + Practical + Interview (Spring Boot Microservices)
---

# 1. What is Load Balancer?

Load Balancer is a mechanism used to **distribute requests among multiple instances of the same service.**

Simple definition:

> Load Balancer distributes traffic to avoid overload and improve availability.

Example:

```text id="y5eplc"
ORDER-SERVICE

8081
8082
8083
```

Requests distributed automatically.

---

# 2. What Problem Existed Before Load Balancer?

Before:

```text id="7i3lhz"
Client
↓

ORDER-SERVICE
↓

8081
```

All requests:

```text id="umyb2s"
10000 Users
↓

Single Instance
```

Problems:

❌ Server overload
❌ Slow response
❌ Downtime risk
❌ Difficult scaling

---

# 3. Why Do We Use Load Balancer?

Reasons:

✔ Handle High Traffic
✔ High Availability
✔ Better Performance
✔ Horizontal Scaling
✔ Fault Tolerance
✔ Resource Optimization

Interview Answer:

> Load Balancer distributes requests among multiple instances to improve scalability, performance, and availability.

---

# 4. How Load Balancer Works

Suppose:

```text id="xemrwr"
Employee Service
↓

Department Service
```

Instances:

```text id="83vzyl"
8081
8082
8083
```

Flow:

```text id="hgh0zh"
Request
↓

Find Instances
↓

Choose Instance
↓

Execute
```

Actual:

```text id="q0bm93"
Feign
↓

Eureka

(Return All)

↓

Load Balancer

(Choose One)

↓

Call
```

---

# 5. Types of Load Balancer (VERY IMPORTANT)

## A. Client Side Load Balancer 🔥

Client itself selects instance.

Flow:

```text id="a6nz6x"
Client
↓

Service Registry

↓

Choose Instance

↓

Call Service
```

Example:

```text id="gxghb8"
Feign
WebClient
Spring Cloud LoadBalancer
```

Industry:

Most common in Spring Boot.

Example:

```java id="6y2thw"
@FeignClient(
name="DEPARTMENT-SERVICE"
)
```

Here:

```text id="7z9x6k"
Feign
↓

Eureka

↓

Load Balancer

↓

Instance
```

---

## B. Dedicated / Server Side Load Balancer 🔥

Separate server handles balancing.

Flow:

```text id="7v34zm"
Client
↓

Load Balancer

↓

Service
```

Examples:

```text id="xsz5b6"
Gateway
Nginx
HAProxy
AWS Load Balancer
```

Industry Example:

```text id="wnuz8r"
Internet
↓

Gateway

↓

Microservices
```

Difference:

| Client Side        | Dedicated Side          |
| ------------------ | ----------------------- |
| Client selects     | Separate server selects |
| Faster internally  | Central control         |
| Service-to-service | External traffic        |

---

# 6. Load Balancing Algorithms

## Round Robin 🔥

Sequential selection.

```text id="t0r8rk"
Req1 → 8081

Req2 → 8082

Req3 → 8083
```

Most common.

---

## Random

Random selection.

---

## Least Connection

Choose instance with minimum active requests.

---

## Weighted

Powerful server gets more requests.

Example:

```text id="oswm0d"
8081 → 70%

8082 → 30%
```

---

# 7. Eureka vs Load Balancer

Very Important.

| Eureka          | Load Balancer    |
| --------------- | ---------------- |
| Finds instances | Selects instance |
| Registry        | Distributor      |
| Returns all     | Picks one        |

Flow:

```text id="jsg2yd"
Eureka
↓

8081
8082
8083

↓

Load Balancer

↓

8082
```

Easy remember:

```text id="k1oq8t"
Eureka → Find

Load Balancer → Select
```

---

# 8. Practical Implementation

Goal:

Run multiple instances.

---

## Step 1 Run Eureka

Start:

```text id="17mwqq"
eureka-server
```

Open:

```text id="uh55ys"
localhost:8761
```

---

## Step 2 Department Service

Dependencies:

```text id="lbj0s9"
Spring Web
Eureka Discovery Client
```

Properties:

```properties id="4ypv2h"
server.port=8081

spring.application.name=DEPARTMENT-SERVICE

eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```

---

Controller:

```java id="s1dhn9"
@RestController
@RequestMapping("/dept")
public class DepartmentController {

@Value("${server.port}")
private String port;

@GetMapping
public String getDept(){

return "Handled By " + port;

}

}
```

---

## Step 3 Run Multiple Instances

Run:

```text id="9u50x4"
8081
```

VM Options:

```text id="o74oq1"
-Dserver.port=8082
```

Run.

Again:

```text id="8dbjbf"
-Dserver.port=8083
```

Dashboard:

```text id="1n4ytg"
8081
8082
8083
```

---

## Step 4 Employee Service

Dependencies:

```text id="o04ls6"
Spring Web
OpenFeign
```

Enable:

```java id="gq3h98"
@EnableFeignClients
```

Feign:

```java id="ck6t2c"
@FeignClient(
name="DEPARTMENT-SERVICE"
)
public interface DepartmentClient {

@GetMapping("/dept")
String getDept();

}
```

Controller:

```java id="f2z7r4"
@GetMapping("/employee")
public String get(){

return client.getDept();

}
```

---

## Step 5 Verify

Call:

```text id="m19ys7"
localhost:8082/employee
```

Output:

```text id="3vbjww"
Handled By 8081

Handled By 8082

Handled By 8083
```

Load balancing successful.

---

# 9. Failure Scenario

Stop:

```text id="kj8vhn"
8082
```

Result:

```text id="vj9i9k"
8081

8083
```

Application still works.

---

### Ribbon
Deprecated.

Current:
Spring Cloud LoadBalancer
---

### Gateway + Load Balancer

Gateway balances external traffic.

---

# 11. Interview Questions

### What is Load Balancer?

Distributes requests.

---

### Why use it?

Scalability and availability.

---

### Types?

Client Side and Dedicated/Server Side.

---

### Feign uses what?

Spring Cloud LoadBalancer.

---

### Does Eureka do balancing?

No.

---

### How do you verify practically?

Log server port.

---

# Final Revision

```text id="olx0qk"
Request
↓

Feign

↓

Eureka

↓

Load Balancer

↓

Selected Instance

↓

Response
```

