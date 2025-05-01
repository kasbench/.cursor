# Requirements for the Blotter Microservice

## General Requirements

We are developing a suite of applications called the GlobeCo Suite.  The GlobeCo Suite is being built as part of a Kubernetes Autoscaling Benchmark called KASBench.  The suite consists primarily of microservices written in Java, Go, and 
Python.

You are an elite software developer assisting on this project.  You are an expert at Kubernetes, Java, Spring Boot, Python, and Go.  You will always follow these instructions.

The following context applies to all microservices regardless of programming language:

* Microservices will be deployed on Kubernetes version 1.32
* Microservice should be easy to follow and transparent.  Prefer simplicity over efficiency.  It is important that researchers are able to read and understand the code.  Prioritize readability over elegance.
* These microservices are being developed to benchmark Kubernetes autoscalers.  They will never be production applications.  We want to make it as easy as possible to interpret the results of actions taken by autoscalers with as little interference as possible.  Therefore, microservices should avoid resiliency features such as circuit breakers, back pressure, rate limiting, load shedding, and retry logic.
* These microservices do not need to be secure.  There is no need for passwords or other security features.  We are trying to avoid anything that might interfere with the goal of benchmarking autoscalers. These microservices will never have real data or access to anything of significance.
* Microservices should not require encryption or anything that might result in export restrictions.
* Microservices should implement Kubernetes health checks, including readiness, liveness, and startup probes.
* Whenever required, use "Noah Krieger" as the author's name and noah@kasbench.org as the email address.
* The license for all microservices should be Apache V2.0.
* Take as much time as necessary to reflect on the problem and check your work.
* Provide complete and executable code solutions.
* Follow the normative practices for the programming language and frameworks being used.
* Assure interoperability between components that are meant to work together.
* Include logging following industry best practices.
* Follow best practices for REST APIs.
* All database tables have a `version` column defined as an integer.  Version columns are used for optimistic concurrency.  When generating ORM entities, identify these columns so that optimistic concurrency control is applied. 
* Before taking any action, review cursor-log.md to see what you have previously done.
* The project has been started using Spring Initializr.  
* The project uses Gradle for build automation.  Please review the build.gradle file in the project root to learn the Java version, Spring Boot version, and dependencies.  All the version numbers in the build.gradle file are valid.  The specified versions exist, even if you don't know about them yet.

This is very important:
* Log all prompts you receive in the file cursor-prompts.md in the project root.  Please do this for every prompt.
* Log all actions you take in the file cursor-log.md in the project root.  Please do this every time you take an action.


## Business Requirements


* The Trade Blotter microservice collects trade orders and provides the functions to allow traders to _work_ trades.  Specifically, traders can decide how much of each order to place, combine trades from multiple orders into a combined _block_, and send orders to the Trade Execution Service.  These actions are available to traders on the company's Intranet.  Orders and trades are stored in a PostgreSQL database.


The name of the Kubernetes microservice is `globeco-trade-blotter`.  The microservice will be written in Java 21 and Spring Boot.

The service consists of the following resources

### Security Type

Security type refers to the classification of a financial instrument based on its nature and how it represents ownership or debt.  Examples include common stock, preferred stock, corporate bond, treasury note, and call option.


The name of the resource is `securityType`

 Botters have the following fields:

| Database Name | API Name | API Datatype |  Description | Constraints |
| --- | --- | --- | --- | --- |
| id | securityTypeId | Integer | Immutable resource identifier. | Required |
| abbreviation | abbreviation | String | Abbreviation for the security type. | Required.  The maximum length is 20 characters. |
| description | description | String | Long description of the security type for display.| Required.  The maximum length is 100 characters. |
| version | versionId | Integer | Version field for concurrency management. | Required |

Supports the following operations

| Resource | Verbs | Description |
| --- | --- | --- |
| /securityType/ | GET | Retrieves all security types.  Include all fields above.  | 
| /securityType/ | POST | Add a new security type.  Payload includes all fields above. |
| /securityType/{securityTypeId} | GET | Retrieves the specified securityTypeId.  Include all fields above.|
| /securityType/{securityTypeId} | PUT, PATCH | Updates the specified security type.  Payload includes the securityTypeId and all fields required for a PUT or PATCH |
| /securityType/{securityTypeId} | DELETE | Deletes the securityTypeId record.  versionId must be passed as a query parameter. |

Use standard HTTP/REST return codes.

#### Sample Data for Security Type

| id | abbreviation | description | version |
| --- | --- | --- | --- |
| 1 | CS | Common Stock | 1 |
| 2 | PS | Preferred Stock | 1 |
| 3 | CB | Corporate Bond | 1 |
| 4 | MB | Muncipal Bond | 1 |
| 5 | TBond | Treasury Bond | 1 |
| 6 | TBill | Treasury Bill | 1 |
| 7 | TNote | Treasury Note | 1 |
| 8 | CALL | Call Option | 1 |
| 9 | PUT | Put Option | 1 |
| 10 | DA | Digital Asset | 1 |
| 11 | MF | Mutual Fund | 1 |
| 12 | ETF | Exchange Traded Fund | 1 |
| 13 | CUR | Currency | 1 |
| 14 | CP | Commercial Paper | 1 |



### Blotter

Botters are used to organize orders that are being worked by the trading desk. 


The name of the resource is `blotter`

 Botters have the following fields:

| Database Name | API Name | API Datatype |  Description | Constraints |
| --- | --- | --- | --- | --- |
| id | blotterId | Integer | Immutable resource identifier. | Required |
| name | name | String | Display name of the blotter. | Required.  The maximum length is 60 characters. |
| auto_populate | autoPopulate | Boolean | If true, any order with a security having a securityTypeId that matches the securityTypeId on this record, will automatically be moved to this blotter.| Not required.  Default to false. |
| security_type_id | securityTypeId | Integer | Used with autoPopulate to automatically populate blotters for orders with a matching securityTypeId. | Not required.|
| version | versionId | Integer | Version field for concurrency management. | Required |

Supports the following operations

| Resource | Verbs | Description |
| --- | --- | --- |
| /blotter/ | GET | Retrieves all blotters.  Include all fields above.  | 
| /blotter/ | POST | Add a new blotter.  Payload includes all fields above. |
| /blotter/{blotterId} | GET | Retrieves the specified blotterId.  Include all fields above.|
| /blotter/{blotterId} | PUT, PATCH | Updates the specified blotterId.  Payload includes the blotterId and all fields required for a PUT or PATCH |
| /blotter/{blotterId} | DELETE | Deletes the blotter.  versionId must be passed as a query parameter |

Use standard HTTP/REST return codes.

#### Sample Data for Blotter

| id | name | auto_populate | security_type_id |  version |
| --- | --- | --- | --- | --- |
| 1 | Default | 0::bit | null | 1 |
| 2 | Common Stock | 1::bit | 1 | 1 |
| 3 | Priority | 0::bit | null | 1 |
| 4 | Crypto | 1::bit | 





## Technical Requirements

* The service will use default `postgres` database.  It will be created automatically.  You should not try to drop or create the `postgres` database.
* Use OpenAPI 3.0 or higher when generating documentation.  Use Apache 2.0 for the license and noah@kasbench.org for the contact.