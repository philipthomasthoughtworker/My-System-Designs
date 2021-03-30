# System Design ([Going Green](http://fundamentalsofsoftwarearchitecture.com/katas/kata?id=GoingGreen))
* **Resources**
  * [System design cheat sheet](https://gist.github.com/vasanthk/485d1c25737e8e72759f)
  * [Bandwidth calculator](https://www.calculator.net/bandwidth-calculator.html)
  * [String length and byte converter](https://mothereff.in/byte-counter)
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
    * The basic customer that has accessibilty to either the internet and public or private transportation
  * How are they going to see it?
    * Web site via the internet
    * Kiosk at a mall
* **Functional Requirements**
  * The system needs to provide customer with a qoute for their personal electronic equipment (Phones, Cameras, etc.) based on it's classification
    * **Clarifying question** 
      * Will the system allow customers to request multiple quotes (for multiple personal electronic equipments) within the same transaction to the service provider?
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
* **Constraints**
  * Mainly identify traffic and data handling constraints at scale.
  * Scale of the system such as requests per second, requests types, data written per second, data read per second
  * Special system requirements such as multi-threading, read or write oriented.
#### High level architecture design (abstract design)
* **Sketch the important components and connections between them, but don't do into some details.**
  * Application service layer (serves the requests)
  * List different services required.
  * Data Storage layer
  * eg. Usually a scalable system includes webserver (load balancer), service (service partition), database (master/slave database cluster) and caching systems.
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
