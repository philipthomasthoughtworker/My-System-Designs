# System Design (Customer Mastering)
* **Resources**
  * [System design cheat sheet](https://gist.github.com/vasanthk/485d1c25737e8e72759f)
  * [Bandwidth calculator](https://www.calculator.net/bandwidth-calculator.html)
  * [String length and byte converter](https://mothereff.in/byte-counter)
  * [Azure Cosmos DB pricing](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/)
#### Clarify and agree on the scope of the system
* **Problem statement**
  * Basic format
    * Problem
    * Background
    * Relevance
    * Objectives
  * Describe how things _should_ work.
  * Explain the problem and state why it matters.
  * Explain your probem's financial cost.
  * Back up your claims.
  * Propose a solution.
  * Explain the benefits of your proposed solution(s).
  * Conclude by summarizing the problem and solution.
* **User cases**
  * Who is going to use it?
    * Legacy invoice systems
       * mastering new and existing supplier/buyer records
    * Data governance team
       * governance managing control and access to the data assets
    * Customer experience team
       * self customer registration
  * How are they going to see it?
    * Building a customer platform 
       * based on MDM-registry with RPC and REST APIs endpoints
    * Cross-functional team building a Customer experience
       * self customer registration
* **Constraints**
  * Mainly identify traffic and data handling constraints at scale.
     * Expected traffic was based on 1 of 10 originating legacy invoice systems
       * however, we thin-sliced the MVP delivery by only working with one (1) originating legacy invoice system based on invoice approval state change
  * Scale of the system such as requests per second, requests types, data written per second, data read per second
     * based on invoice approval state change
     * request types
       * either RPC or REST API
     * DAU was 10 users approving invoices on the originating legacy system
       * no spikes
     * MAU was 300 users approving invoices on the originating legacy system
     * 645 invoice approvals hourly
     * 5,000 invoice approvals daily
     * 150,000 invoice approvals annually
     * 645 invoice approvals hourly \* 70KB per invoice \= 45,150KB storage hourly
     * average invoice was about 50 to 75KB, **HOWEVER, WE ARE ONLY DEALING WITH A BUYER AND A SUPPLIER ON ANY GIVEN INVOICE**
     * average buyer and supplier on an invoice without a line address 2 was about 107B
       * 645 invoice approvals hourly \* 107B per buyer and supplier \= 69.01KB storage hourly
       * 69.01KB \* 8 hours \= 552.08KB storage daily
       * 552.08KB \* 261 actual working days \= 144092.88KB storage annually
       * 1RU = 1KB; 1RU = $0.008 per hour for Cosmos DB regional
         * 69.01KB \* $0.008 = $0.55 per hour ($1,148.4 annually)
       * 1RU = 1KB; 1RU = $0.016/per hour for Cosmos DB multi-regional support
         * 69.01KB \* $0.016 = $1.11 per hour ($2,317.68 annually)
  * Special system requirements such as multi-threading, read or write oriented.
#### High level architecture design (abstract design)
* **Sketch the important components and connections between them, but don't do into some details.**
  * Application service layer (serves the requests)
    * Need a service to master customer records from legacy originating invoice systems.
  * List different services required.
    * Need a service to apply thresholds with a weighted rating system to score buyer and supplier address information.
    * Need a service to act as a anti-corruption translation layer for legacy originating invoice systems.
    * Later, we needed a service for the Change Feed Processor for Azure Cosmos DB.
  * Data Storage layer
    * Need a database solution that was great a maintaining a seemingly infinite number of relationships between buyers, suppliers, accounts, etc.
  * eg. Usually a scalable system includes webserver (load balancer), service (service partition), database (master/slave database cluster) and caching systems.
    * No need for a load balancer or load balance cluster (Future state would require it)
    * Later, we needed to deal with partition keys on logical partitions
    * Need for rate limiting to avoid spamming on address standardization service to help mitigate cost from using third-party service.
#### Component Design
* **Component + specific APIs required for each of them.**
  * Flagship API
     * C# App Service Web App for Containers
        * Master data management customer platform
  * Ancillary services
     * C# Azure Function
        * Scoring
        * Get supplier and buyers published from originating legacy invoice systems and anti-corruption layer
     * C# App Service Web App for Containers
        * Azure Cosmos DB Change Feed Processor
          * for keeping edge data up-to-date
* **Object oriented design for functionalities**
  * Map features to modules: One scenario for one module.
  * Consider the relationships among modules
    * Certain functions must have unique instance (Singletons)
    * Core object can be made up of many other objects (composition).
    * One object is another object (inheritance)
* **Data schema design.**
  * Azure Cosmos DB Graph Gremlin API
    * Vertices(Nodes) are standardized and validated Customers
    * Edges defines the relationship between Customers (Buyer/Supplier). If a Customer is "buying from", "supplying to", or "billing" another Customer
#### Understanding Bottlenecks
* **Data integrity from the originating legacy invoice systems.**
* **Cross-functional teams confidence level on determining the correct events from originating legacy invoice systems to master buyers and suppliers.**
* **Determing ownership of the anti-corruption layers.**
* **Determining the most effecient partition key for logical partition.**
#### Scaling your abstract design
* **Vertical scaling**
  * No spikes or peak hours that would require more processing power.
* **Horizontal scaling**
  * No spikes that would require more servers.
* **Caching**
  * No spikes that would require more servers.
* **Load balancing**
  * No need for load balancing on the initial thin slice of work.
