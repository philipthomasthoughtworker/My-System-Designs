# System Design ([Going Green](http://fundamentalsofsoftwarearchitecture.com/katas/kata?id=GoingGreen))
#### Clarify and agree on the scope of the system
* **Problem statement**
  * A large electronics store wants to get into the electronics recycling business and needs a new system to support it. Customers can send in their small personal electronic equipment (or use local kiosks at the mall) and possibly get money for their used equipment if it is in working condition.
* **User cases**
  * Who is going to use it?
    * Hundreds, hopefully thousands to millions of customers that has accessibilty to either the internet and public or private transportation
    * **Clarifying questions**
       * Is this the DAU, MAU or AAU (Daily, monthly and annual average user count)? **Assumptions can be made here**
       * Are there peak times or spikes **Assumptions can be made**
  * How are they going to see it?
    * Web site via the internet
    *  **Clarifying questions**
       * Does this need to be accessed nationally, internationally, or globally **Assumptions can be made here**
    * Kiosk at a mall
      * **Clarifying questions**
        * Is the service providing the equipment for the kiosk? Or is rented/leased/purchased by third party? **Assumptions can be made here**
        * How many devices can a kiosk support? **Assumptions can be made here**
        * What is the impact to business when dealing with device malfunctions that require hardware maintenance? Replacements? **Assumptions can be made here**
        * Are the devices using the same web solution as the home experience? **Assumptions can be made**
* **Functional Requirements**
  * The system needs to provide customer with a qoute for their personal electronic equipment (Phones, Cameras, etc.) based on it's classification
    * **Clarifying questions** 
      * Will the system allow customers to request multiple quotes (for multiple personal electronic equipments) within the same transaction to the service provider? **Assumptions can be made here**
      * What is the expiration date of the quote?
  * The system needs to be able to arrange delivery of personal electronic equipment to the requesting customer
    * Deliver the size appropriate shipping materials, label and postage based on the dimensions of the personal electronic equipment to the customer
      * Service-level agreement on receiving the shipping materials, label and postage to the customer from the service provider
      * Service-level agreement on sending the personal electronic equipment back to the service provider
   * The system needs to be capable of managing personal electronic equipment inventory
   * The system needs to be capable of inspecting and ranking the condition of the personal electronic equipment based on it's classification
   * The system needs to be capable of arranging delivery of the personal electronic equipment to an approved recycling facility
   * The system needs to be capable of creating an Ebay auction of the personal electronic equipment
   * The system needs to be capable of arranging delivery of the personal electronic equipment to an Ebay auction winner
* **Non-functional Requirements**
  * Availability, Reliability, Maintainability, Scalability, Access Security, Accessibility, Elasticity, Extensibility, Response time, Serviceability, Testability, Privacy, Integrity, Survivability, Usability, Verifiability, Interoperability, Portability
* **Constraints**
  * Mainly identify traffic and data handling constraints at scale.
  * Scale of the system such as requests per second, requests types, data written per second, data read per second
    * DAU - 1,000 Daily average user
    * MAU - 30,400 Monthly average user
    * AAU - 365,000 Annual average user
    * Guestimation on size of requests to store
       * [Sample request](https://pastebin.com/cHZTncv9)
       * 462 bytes per request
       * DAU * 462 B = 462,000 bytes (0.000462 GB) daily
       * MAU * 462 B = 1,405,404 bytes (0.001405404 GB) monthly
       * AAU * 462 B = 16,863,000 bytes (0.016863 GB) annually
    * **NOTE: Need to talk bandwidth, throughput and latency**
  * Special system requirements such as multi-threading, read or write oriented.
#### High level architecture design (abstract design)
* **Sketch the important components and connections between them, but don't do into some details.**
  * Application service layer (serves the requests)
    * Capable of multi-factor authentication
    * Capable of securely inputting personal information about themselves
    * Capable of securely inputting personal electronic equipment
    * Capable of securely returning quote information
    * Capable of requesting shipping material, labels and postage based on dimensions of the personal electronic equipment
    * Capable of securely providing information for sending payment for the personal electronic equipment
    * Capable of caching sessions to be completed at a later time within a certain window of time.
    * Capable of delivering receipts (Email, Text, Mail)
    * Capable of uploading images of devices
  * List different services required.
    * Content Delivery Network (Eq. Azure CDN)
  * Data Storage layer
    * Caching (Eq. Azure Redis for Cache)
    * Object storage (Eq. Azure Blob Storage)
    * NoSQL JSON document database solution (Eg. Azure Cosmos Db SQL Core API)
      * Partion key with highest cardinality to avoid Hot Partitioning and Cross Partition Querying
#### Component Design
* **Component + specific APIs required for each of them.**
  * Flagship API
  * Ancillary services
* **Object oriented design for functionalities**
  * Map features to modules: One scenario for one module.
  * Consider the relationships among modules
    * Certain functions must have unique instance (Singletons)
    * Core object can be made up of many other objects (composition).
    * One object is another object (inheritance)
* **Data schema design.**
#### Understanding Bottlenecks
#### Scaling your abstract design
* **Vertical scaling**
* **Horizontal scaling**
* **Caching**
* **Load balancing**
#### Resources
  * [System design cheat sheet](https://gist.github.com/vasanthk/485d1c25737e8e72759f)
  * [Bandwidth calculator](https://www.calculator.net/bandwidth-calculator.html)
  * [String length and byte converter](https://mothereff.in/byte-counter)
