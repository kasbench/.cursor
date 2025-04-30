# Requirements for the Blotter Microservice

## Business Requirements


* The Trade Blotter microservice collects trade orders and provides the functions to allow traders to _work_ trades.  Specifically, traders can decide how much of each order to place, combine trades from multiple orders into a combined _block_, and send orders to the Trade Execution Service.  These actions are available to traders on the company's Intranet.  Orders and trades are stored in a PostgreSQL database.


The name of the Kubernetes microservice is `globeco-trade-blotter`

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





## Technical Requirements

* The service will use default `postgres` database.  It will be created automatically.  You should not try to drop or create the `postgres` database.
* Use OpenAPI 3.0 or higher when generating documentation.  Use Apache 2.0 for the license and noah@kasbench.org for the contact.