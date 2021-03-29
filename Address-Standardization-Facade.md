# System Design (Address Standardization Facade)
* **Resources**
  * https://gist.github.com/vasanthk/485d1c25737e8e72759f
  * https://www.calculator.net/bandwidth-calculator.html
  * https://convertlive.com/u/convert/bytes/to/kilobytes
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
     * 645 requests hourly \* 53B per request \= 34185KB storage hourly
     * average request was about 53B (average size of an address)
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

## Resources
* [Melissa Data Quality Suite](https://www.g2.com/products/melissa-data-quality-suite/pricing?__cf_chl_captcha_tk__=7cb9c2b3d41d2ccbfcd5c9088c278761467bb4f4-1617033984-0-Af9wiakf2Y15CFzj7uoDOHTuS6-bP-AEHJEOwfzIJ5d1kN-gHoXtFT6QpzzXrb2qtCsuen6ziKfZ_aiD0F6wNmK-jfzep_6INAIIxvi6POsgimsfLLAi63yXrFk8MI5MmRrn9yxCdkC5Ux9W8mJNyhOUfXmJUebgrYPZGOvhOnM-T1S2zMoWTkH1qEOpxDuLZ6O0UO8kQZt_1Rl9F5Rc-ok9pcAeiQbrD7wHyZL44QQeWQS8-pQur5s8_t9759odicmia8-SaOtOUgsNsqyqgW7g8x95wxue-fPQ9bL8JJB6tPjOT3Mj5XYORgjhRJrZYLC2ytCN0mJhT0h1j77AbWRH6IXMaWrfczJFedIWScfJl3YILOzy3uOIOTE5k3JNi1Plj7XE1g4Qw0oIfIVaCrgOc8RTOS9JeGFrLEapyKmXiG1ask7oftqcrffsTrJGCPnQGG_aRq-bY4Ioa533QrPc7bZ1ohd09jqfv1FEis30UatH1mZYhSVChABRa_Ul6J-Z5zr0ptrkG77Kvy8CW6Lui8tx3F6b2Mf7RU5PNhHKVajDa5_8gPMbaBy2B9gbOWSXY6Vv6KRwdqPmSRFagdBeHkAXq19LdX3Ud_Jpcu92QmYR7XJ65wsvMeUpi7qAEXH51TD5Bwwbz_Nd7uRr314)
* [Bing API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/)
* [Azure Cache for Redis](https://azure.microsoft.com/en-us/pricing/details/cache/)
