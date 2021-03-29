# System Design (Customer Scoring Matching)
* **Resources**
  * [System design cheat sheet](https://gist.github.com/vasanthk/485d1c25737e8e72759f)
  * [Bandwidth calculator](https://www.calculator.net/bandwidth-calculator.html)
  * [String length and byte converter](https://mothereff.in/byte-counter)
  * [Damerau-Levenshtein minimum edit distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)
#### Clarify and agree on the scope of the system
* **User cases**
  * Who is going to use it?
    * Data governance team
       * governance managing control and access to the data assets
    * Customer mastering (System I designed and delivered)
       * Mastering new and existing customer regardless of the originating systems
  * How are they going to see it?
    * Consuming web service 
       * Facade web service to external paid API
* **Constraints**
  * Mainly identify traffic and data handling constraints at scale.
     * Expected traffic was based on 1 of 10 consumers
       * however, we thin-sliced the MVP delivery by only working with two (2) consumers based on need
  * Scale of the system such as requests per second, requests types, data written per second, data read per second
     * based on need
     * request types
       * RPC API
     * DAU was 10 users
       * no spikes or peak hours
     * MAU was 300 users
     * 645 requests hourly
     * 5,000 requests daily
     * 150,000 requests annually
     * average request was about 53B (average size of an address)
     * average buyer and supplier on an invoice without a line address 2 was about 107B
     * 645 invoice approvals hourly \* 107B per buyer and supplier \= 69.01KB storage hourly
     * 69.01KB \* 8 hours \= 552.08KB storage daily
     * 552.08KB \* 261 actual working days \= 144092.88KB storage annually
       * Melissa Cost
         * 500,000 address validations annually
         * $0.0062 per record per year
         * 150,000 requests per year at $0.0062 per request = $3,100 annually
       * Bing Local Business Search Cost
         * S10 offerring
         * 100 Transactions per second (TPS)
         * $2.50 per 1,000 transactions = 150,000 / 1,000) \* $2.50 \= $375.00
       * Azure Cache for Redis
  * Special system requirements such as multi-threading, read or write oriented.
#### High level architecture design (abstract design)
* **Sketch the important components and connections between them, but don't do into some details.**
  * Application service layer (serves the requests)
    * Need a service to accept buyer and supplier addresses for standardization and validation.
  * List different services required.
    * No other services are required
  * Data Storage layer
    * Since this is a facade to an externally paid service API, no data storage is required.
  * eg. Usually a scalable system includes webserver (load balancer), service (service partition), database (master/slave database cluster) and caching systems.
    * Need for a caching solution for address standardization to help mitigate cost from using third-party service.
    * Need for rate limiting to avoid spamming on address standardization service to help mitigate cost from using third-party service.
#### Component Design
* **Component + specific APIs required for each of them.**
  * Flagship API
     * C# App Service Web App for Containers
        * Address standardization
  * Ancillary services
     * Azure Cache for Redis
       * Caching standardized addresses for mitigating per call cost
* **Object oriented design for functionalities**
  * Map features to modules: One scenario for one module.
  * Consider the relationships among modules
    * Certain functions must have unique instance (Singletons)
    * Core object can be made up of many other objects (composition).
    * One object is another object (inheritance)
* **Data schema design.**
  * Azure Cache for Redis
    * Key being a hash of a standardized customer address
    * Value is the actual standadized customer address (without additional vendor specific information on response)
    * Eviction policy was least frequently used (LFU)
    * Cache invalidated once a month after third-party vendor performs updates.
#### Understanding Bottlenecks
* **Data integrity from the originating legacy invoice systems.**
* **Determining the most complete and reliable address standardization vendor.**
#### Scaling your abstract design
* **Vertical scaling**
  * No spikes or peak hours that would require more processing power.
* **Horizontal scaling**
  * No spikes that would require more servers.
* **Caching**
  * Caching on address standardization service to store standardized address to help mitigate cost.
* **Load balancing**
  * No need for load balancing on the initial thin slice of work.

