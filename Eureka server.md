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

