---
description: 
globs: 
alwaysApply: true
---
These are the requirements for the globeco-trade-blotter service.


* The Trade Blotter microservice collects trade orders and provides the functions to allow traders to _work_ trades.  Specifically, traders can decide how much of each order to place, combine trades from multiple orders into a combined _block_, and send orders to the Trade Execution Service.  These actions are available to traders on the company's Intranet.  Orders and trades are stored in a PostgreSQL database.

