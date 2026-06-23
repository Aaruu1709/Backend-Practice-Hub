# Service Registry vs Service Discovery (Interview Ready)

## Q1. What is Service Registry?

**Interview Answer:**

Service Registry is a **central place where all microservices register themselves.**

It stores information like:

* Service Name
* IP Address
* Port Number
* Instance Details
* Health Status

Simple explanation:

> Service Registry works like a phone directory for microservices.

Example:

```text
EMPLOYEE-SERVICE → 8081

DEPARTMENT-SERVICE → 8082

PAYMENT-SERVICE → 8083
```

Whenever a service starts, it registers itself into the registry.

---

## Q2. Why do we use Service Registry?

**Interview Answer:**

We use Service Registry to avoid hardcoded URLs and enable dynamic communication between services.

Without Registry:

```text
Employee
↓

http://localhost:8082
```

Problem:

* Port changes
* Scaling becomes difficult
* Maintenance increases

With Registry:

```text
Employee
↓

DEPARTMENT-SERVICE
↓

Registry finds location
```

---

## Q3. What is Service Discovery?

**Interview Answer:**

Service Discovery is the **process of finding another service from the Service Registry and communicating with it.**

Simple explanation:

> Discovery means searching and locating services.

Example:

```text
Employee Service
↓

Need Department Service
↓

Ask Registry
↓

Get Location
↓

Call Service
```

---

## Q4. Why do we use Service Discovery?

**Interview Answer:**

We use Service Discovery because service instances can change dynamically.

Benefits:

* Dynamic communication
* Loose coupling
* Easy scaling
* No hardcoded endpoints

---

## Q5. Difference Between Service Registry and Service Discovery

| Service Registry           | Service Discovery         |
| -------------------------- | ------------------------- |
| Stores service information | Finds service information |
| Central directory          | Communication process     |
| Maintains records          | Uses records              |
| Example: Eureka Server     | Example: Feign + Eureka   |

Easy remember:

```text
Registry → Store

Discovery → Find
```

---

## Q6. Real Industry Flow

```text
Department Service Starts
↓

Registers into Registry

(Service Registry)

↓

Employee Service Starts

↓

Needs Department

↓

Search Registry

(Service Discovery)

↓

Communication Happens
```

---

## Q7. Most Asked Interview Question

### Interviewer:

Explain Service Registry and Service Discovery with example.

### Your Answer:

In microservices architecture, Service Registry acts as a central directory where all services register themselves with details like service name, IP, and port.

When one service wants to communicate with another service, it does not call using a hardcoded URL. Instead, it performs Service Discovery by asking the registry for the service location.

For example, if Employee Service wants to call Department Service, it asks the registry for DEPARTMENT-SERVICE. The registry returns the available instance and communication happens.

This approach improves scalability and reduces tight coupling.

---

## Q8. Interview Trap Question

### Are Service Registry and Service Discovery same?

Answer:

No.

Service Registry stores information.

Service Discovery finds and uses information.

---

# Final One-Line Revision

```text
Service Registry
=
Store Services

Service Discovery
=
Find Services
```
