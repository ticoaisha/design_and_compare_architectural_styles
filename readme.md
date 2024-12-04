# Designing and Comparing Architectural Styles

To illustarte architectural characteritics, advantages, and disadvantages of three architectural styles, we will focus on an e-commerse application. Let's discuss a monolithic architecture first.

## Monolithic architecture

![Monolithic architecture](/design_and_compare_architectural_styles/images/1_monolithic_architecture.png)

The above picture illusrates monolithic architecture for an e-commerece application and highlights the following **core functionalities:**
* User registration and management, that includes user sign-up and login with authentication (via email and password); profile management: update user details, view past orders, etc.; user roles (customer, admin).
* Product catalog, that includes viewinf products with details such as name, description, price, and category; searching and filtering products by categories, price range, and keywords; admin functionality to add, update, or remove products.
* Shopping cart, that includes adding / removing products to / from a shopping cart; updating the quantity of products; calculating the total cost including taxes, shipping, etc.
* Order processing, that includes checkout process (capture shipping information and payment details); order confirmation and status tracking; payment integration.

High-level architecture diagram is represented by the following **layers:**
* **UI layer (web UI / front-end)** built with technologies, like HTML/CSS, JavaScript, or a front-end framework (React, Angular etc.). This layer handles user interactions, forms, navigation, etc.
* **Business logic / back-end layer (application server)** built with Node.js, Python (Django/Flask), or another web framework. This layer handles business logic: user authentication, cart operations, product management, etc., as well as APIs for data fetching and processing.
* **Data interface layer (database)** could be a relational database (e.g., PostgreSQL, MySQL) or a NoSQL database (e.g., MongoDB). It stores user information, products, orders, cart data, etc.

This architectural design has its own advantages and disadvantages, as follows:
**Advantages of the monolithic architecture:**
* **Simplicity** - simple architecture allows for a quick development and is easy to understand for new developers.
* **Uniformity** - a consistent data flow across layers makes it easier to maintain and debug.
* **Rapid prototyping** - ideal for building an MVP (Minimum Viable Product) with a focus on core features.

**Disadvantages of the monolithic architecture:**
* **Scalability** - there might be challenges under high loads due to the limited horizontal scalability (e.g., growing traffic or data). There could arise a need for caching, database optimization, and potential service distribution to handle increasing demand.
* **Flexibility** - coupling of components (e.g., the front-end tightly coupled with back-end APIs) can make it harder to introduce new features without modifications across layers. This design is less modular and harder to adopt microservices or integrate with other complex systems.
* **Deployment** - initial deployments may be straightforward but scaling to multiple instances and managing state (e.g., shopping cart) in a distributed manner could introduce complications. Additional infrastructure components may be necessary (e.g., load balancers, session storage, container orchestration tools).

It is worth to note that this design is great for getting started but may need rearchitecting as traffic, complexity, or performance requirements grow. This leads us to analyze the microservices architecture.

## Microservices architecture

Now let's take the basic monolithic architecture for the e-commerce and break it down into the distinct microservices. This style will help to decouple the different functionalities, allowing for independent scaling and development. 

![Microservices architecture](/design_and_compare_architectural_styles/images/2_microservices_architecture.png)

Here identified the following primary microservices for this specific design (please note that the list of microservices presented here is not exhausting).
* User service handles user registration, authentication, and profile management, manages user data (roles, passwords, etc.).
* Product service manages the product catalog, including CRUD operations for products, handles product searches and filtering.
* Cart service manages user shopping carts, adds, removes, or updates product quantities in the cart.
* Order service handles the order processing workflow, including order creation, status tracking, and order history, integrates with payment gateways.
* Payment service manages payment processing and transaction records, integrates with external payment providers.

High-level architecture diagram is represented by the following **layers:**
* **UI layer (front-end)** interfaces with the API Gateway to interact with various services.
* **API Gateway** acts as a single entry point for client requests, routing them to appropriate microservices and handling cross-cutting concerns (authentication, logging, etc.).
* **Microservices:** each service manages a distinct set of business functions and has its own database to maintain data independence and optimized queries.

One of the goals to introduce microservices architecture for the e-commerce application is to overcome scalability and flexibility issues identified above for the monolithic architecture. So the **advantages of the microservices architecture** are the following:
* **Improved scalability** - each microservice can be scaled independently based on its resource demands. For example, the product service can scale to accommodate high search traffic without affecting other services. Load balancers can be used for individual services to handle spikes in traffic.
* **Enhanced flexibility** - microservices can be developed, deployed, and maintained independently, enabling faster iteration cycles. It means that different technologies can be used for different microservices based on specific requirements (e.g., faster databases for product catalogs). Also it might be easier to introduce new features or microservices without impacting the entire system.

At the same time microservices architecture has itw own **disadvantages:**
* **Deployment complexity** - managing deployments for multiple microservices can be challenging, especially with dependencies between services. That is why solutions, like containerization (Docker) and orchestration tools (Kubernetes) are often necessary for smooth deployments.
* **Development challenges** - microservices need well-defined interfaces (APIs) for communication, requiring careful design and versioning to avoid breaking changes. Developers must consider inter-service communication (e.g., REST) and error handling mechanisms.
* **Data consistency** - distributed data models can lead to consistency issues, especially when transactions span multiple microservices. That is why there could be a need in the eventual consistency models, distributed transaction patterns (e.g., Saga pattern), and ensuring services are loosely coupled.
* **Monitoring and debugging** - due to the nature of the microservices, observability across them is more complex. To detect and diagnose the potential issues effective logging, tracing, and monitoring tools, such as Prometheus, Grafana, Jaeger etc., are needed. 
* **Network overhead** - communication between microservices introduces network latency and potential bottlenecks, requiring optimizations, such as caching and efficient API design.

As we can see that microservices architecture provides long-term growth potential and robustness, but at the same time due to its complex nature, there is a need in the investment in infrastructure and management processes to mitigate that complexity.

## Serverless architecture

Moving further down the path of improving the architectural design, we can identify serverless functions that could benefit microservices.

![Serverless architecture](/design_and_compare_architectural_styles/images/3_serverless_architecture.png)

Let's identify some examples of these functions applicable to the e-commerce applicaiton:
* User authentication function - handles login, token generation, and password resets, triggered by API Gateway requests.
* Product recommendations function - generates personalized recommendations based on the user activity or product interactions.
* Cart expiry cleanup function - automatically clears abandoned carts after a specified period of inactivity to maintain a clean database and free up resources.
* Order notification function - sends notifications or emails when triggered by changes in order status.
* Payment processing function - processes payments when triggered by an order creation event from the order service.

Please note the position of the serverless layer in the above diagram, as it does not fit between the microservices layer and the data layer. Instead, it acts as a supporting layer alongside the microservices. Serverless functions interact with both the API Gateway and the microservices as needed. They are triggered by specific events or requests (e.g., user actions, events from microservices like order creation, etc.) but do not replace or act as an intermediary layer between microservices and databases. The serverless layer operates independently but works in conjunction with the microservices to handle specific tasks or processes. Serverless layer closely interacts with other components:
* **API Gateway** - serverless functions can be triggered directly by the API Gateway for specific functions, such as user authentication or other event-driven workflows.
* **Microservices** - serverless functions can respond to events emitted by the microservices (e.g., an order completion event triggers an order notification function).
* **Data layer** - serverless functions can directly access data storage (e.g., reading/writing to databases) if needed, but this is typically done for event-driven tasks or isolated operations.

Serverless architecture has its own advantages and disadvantages, that are worth to know and consider while choosing an architectural style.

**The key benefits of the serverless architecture are:**
* **Scalability** - serverless functions automatically scale based on demand, accommodating spikes in traffic without manual intervention.
* **Cost efficiency** - only pay for what you use, as serverless functions are charged based on the number of executions and duration.
Ideal for tasks with unpredictable or intermittent usage patterns.
* **Reduced operational management** - serverless functions eliminate the need to manage infrastructure, patch servers, or handle OS-level configurations, provides faster deployment times for specific functionalities.

**Disadvantages of the serverless architecture:**
* **Cold start latency** - serverless functions may have a delay (cold start) when invoked after being idle, which can affect user experience during the initial request.
* **State management** - serverless functions are stateless, meaning state has to be managed externally using databases, caches, or other storage solutions. This can complicate scenarios, like user sessions or long-running processes.
* **Limited execution time** - serverless functions have execution time limits (for example, AWS Lambda allows a maximum of 15 minutes), which could be a challenge for longer-running processes.
* **Complex integration** - using serverless alongside microservices can lead to more complex integrations and workflows that must be carefully designed to ensure efficient communication and data flow.
* **Dependency management** - handling dependencies (e.g., libraries) in serverless functions requires packaging them correctly and may lead to larger deployment artifacts.

As we can see, microservices allow more granular control over long-running processes and stateful applications. They offer flexibility to use different technology stacks and can be easily containerized. Serverless emphasizes simplicity for specific tasks and reduces infrastructure management 

In general, introducing serverless architecture can help to optimize resource usage, cost, and operational complexity, but might not be suitable for all application components due to state limitations and execution constraints.

## Comparison between the monolithic, microservices and serverless architectures

After reviewing specifics of each of the architectural styles, let's make a comparison between them concentrating on certain key aspects, such as scalability, development complexity, deployment, maintenance, and cost implications.

**1. Scalability**
**Monolithic Architecture:**
* Pros: easy to scale vertically by adding more resources (CPU, memory) to the server hosting the application.
* Cons: Limited horizontal scalability as it involves replicating the entire application for every instance, leading to inefficient resource usage.
**Use Cases:** Best suited for applications with predictable and stable loads but not ideal for high-growth, high-traffic applications.

**Microservices Architecture:**
* Pros: each microservice can be independently scaled based on its unique load requirements. This leads to the optimized resource utilization.
* Cons: more complex to manage due to service orchestration, monitoring, and inter-service communication.
**Use Cases:** Suitable for large-scale applications with multiple services that require independent scaling and for applications with varying workloads across different functionalities.

**Serverless Architecture:**
* Pros: automatically scales with demand; no manual intervention needed for resource allocation. Ideal for workloads with variable demand, including unpredictable traffic spikes.
* Cons: cold start latency may occur if functions are inactive for a while.
**Use cases:** Excellent for applications with highly variable or unpredictable traffic and for components that need to handle periodic spikes (e.g., seasonal sales).

**2. Development complexity:**
**Monolithic Architecture:**
* Pros: initially it is simpler to develop as the codebase is centralized. Easier for smaller teams to work on and understand.
* Cons: as the codebase grows, the application becomes harder to manage and test.
**Use cases:** Best suited for small teams and projects with straightforward, well-defined functionality.

**Microservices Architecture:**
* Pros: easier to manage code changes and enhancements as each service is self-contained, enabling parallel development across teams.
* Cons: requires coordination between different services, making development more complex. Service dependencies need careful management to avoid breaking changes.
**Use cases:** Ideal for large teams and complex projects that can benefit from modular development and where teams specialize in different domains.

**Serverless Architecture:**
* Pros: simplifies the development of specific tasks or event-driven workflows, as developers focus on business logic rather than infrastructure.
* Cons: statelessness of serverless functions can complicate stateful workflows. Managing multiple functions can become cumbersome if there are a large number of functions and dependencies.
**Use cases:** Well-suited for developing isolated, event-driven functionalities or rapid prototyping.

**3. Deployment:**
**Monolithic Architecture:**
* Pros: easier to deploy as a single unit with minimal dependencies.
* Cons: even small changes require the entire application to be redeployed.
**Use cases:** Good for applications with infrequent updates.

**Microservices Architecture:**
* Pros: services can be deployed independently, reducing downtime.
* Cons: deployment orchestration and service discovery require sophisticated tools (e.g., Kubernetes).
**Use cases:** Best for projects with frequent updates and different release cycles for different components.

**Serverless Architecture:**
* Pros: simplifies deployments; functions can be deployed independently without affecting other parts of the system.
* Cons: deployment limits and versioning need careful attention.
**Use cases:** Suitable for frequent and isolated updates with quick deployments.

**4. Maintenance:**
**Monolithic Architecture:**
* Pros: maintenance is simpler with a single codebase and uniformity across the application.
* Cons: as complexity grows, maintaining monolithic applications becomes challenging due to tight coupling.
**Use cases:** Ideal for simpler applications that can be maintained by a small team.

**Microservices Architecture:**
* Pros: individual services are easier to maintain, upgrade, and replace.
Bug fixes and feature additions can be localized to specific services.
* Cons: requires distributed monitoring, logging, and debugging.
**Use cases:** Suitable for applications that evolve quickly and require modular maintenance.

**Serverless Architecture:**
* Pros: reduced infrastructure management (handled by cloud providers).
Maintenance focuses on code and logic rather than server upkeep.
* Cons: monitoring and debugging distributed serverless functions can be challenging.
**Use cases:** Best for applications where reducing infrastructure maintenance is a priority.

**5. Cost implications:**
**Monolithic Architecture:**
* Pros: lower initial cost as only one server is typically required.
* Cons: high scaling costs due to inefficient resource utilization.
**Use cases:** Suitable for low-budget projects with stable traffic patterns.

**Microservices Architecture:**
* Pros: more cost-effective scaling, as only the necessary services are scaled.
* Cons: increased operational costs due to the complexity of managing many services.
**Use cases:** Best for projects with high traffic and large user bases where selective scaling matters.

**Serverless Architecture:**
* Pros: pay-as-you-go model reduces costs for applications with variable usage, no costs for idle services.
* Cons: high-volume applications may incur unexpected costs if usage is not monitored.
**Use cases:** Ideal for applications with unpredictable traffic and infrequent activity.

