# Reflection

### 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Unary RPC is the simplest form of gRPC interaction, where the client sends a single request and receives a single response. This pattern is ideal for operations like login, creating a new user, or processing a one-time payment. Server streaming is appropriate when a client sends one request and expects a stream of responses in return — for example, retrieving a long list of transaction history or log entries. Bi-directional streaming allows both the client and server to send and receive streams of messages concurrently, making it well-suited for real-time applications like live chat, collaborative tools, or telemetry systems. Each pattern offers trade-offs between simplicity, performance, and flexibility.

---

### 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Security in gRPC services encompasses several critical aspects. Authentication can be managed via metadata headers using bearer tokens (e.g., JWT), API keys, or even mutual TLS (mTLS). Authorization involves checking whether a user is allowed to perform a specific action and can be enforced using interceptors or middleware logic within tonic. Data encryption is handled at the transport layer using TLS over HTTP/2, supported natively by the tonic ecosystem through libraries like rustls or OpenSSL. Additionally, developers must guard against denial-of-service attacks, implement input validation, and consider logging and auditing for compliance and traceability.

---

### 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Bi-directional streaming in Rust introduces complexities that are both architectural and technical. One of the main challenges lies in managing concurrent reading and writing over a single connection without blocking. Lifetime management, proper task spawning, and channel synchronization can be difficult to reason about. Rust’s ownership and borrow checker model, while ensuring safety, requires careful handling of data flow between asynchronous tasks. In real-time applications like chat systems, issues such as dropped messages, ungraceful stream termination, and backpressure from slow consumers can degrade performance if not properly addressed.

---

### 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Using ReceiverStream simplifies integration between tokio::mpsc channels and gRPC’s streaming APIs. It allows developers to stream dynamically generated data to clients with minimal boilerplate. The abstraction fits well with server-streaming and bi-directional services. However, this approach comes with trade-offs: channels may be closed prematurely, leading to stream interruptions, and error handling becomes slightly more complex. Performance-wise, custom stream implementations may be more efficient in high-throughput environments. Still, for many practical use cases, ReceiverStream offers a solid balance between simplicity and power.

---

### 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

A maintainable Rust gRPC project should be structured into clearly defined modules. Protocol Buffers definitions should be isolated in a proto/directory, and compiled using a dedicated build.rs script. The business logic for each service should reside in its own module or crate (service/, handler/), while the application entry points (e.g., server and client binaries) should be separated under src/bin/. Reusable utilities, error types, and interceptors should also be modularized. This separation ensures that the codebase remains clean, testable, and scalable as the system evolves.

---

### 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

A real-world payment service would require significantly more complexity than a basic RPC handler. It would involve validating user input, verifying payment limits, interacting with third-party payment gateways (e.g., Stripe, Midtrans), and handling asynchronous webhook responses. It should also persist transactions to a database, handle retries on transient failures, and log all steps for audit purposes. Integration with fraud detection systems, multi-currency support, and compliance with standards like PCI DSS might also be necessary depending on the business requirements.

---

### 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

gRPC influences system design by enforcing schema-first, strongly-typed communication across services. This improves clarity and reliability in distributed systems, especially those written in multiple programming languages. Code generation reduces boilerplate and ensures consistency across services. However, gRPC’s reliance on HTTP/2 means that browser clients require gRPC-Web adapters. Interoperability with REST-based or event-driven systems may require additional gateways or translation layers. Despite these limitations, gRPC fosters faster, safer, and more efficient service communication in back-end systems.

---

### 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

HTTP/2 offers multiplexing, flow control, and binary framing, allowing multiple streams over a single connection without blocking. These features enable gRPC to be highly performant and bandwidth-efficient compared to REST over HTTP/1.1, which suffers from head-of-line blocking and redundant headers. Compared to WebSockets, HTTP/2 with gRPC provides better tooling, message framing, and type safety. However, gRPC over HTTP/2 is not natively supported in most browsers, requiring translation to HTTP/1.1-compatible gRPC-Web. WebSocket remains preferable for lightweight browser communication, albeit with less structure and standardization.

---

### 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

REST APIs are inherently synchronous and unidirectional, with the client always initiating the request and the server returning a single response. This model is simple and widely supported but poorly suited for real-time communication. In contrast, gRPC supports bi-directional streaming, allowing both client and server to maintain an open channel and exchange data asynchronously. This results in lower latency and higher interactivity, making gRPC ideal for use cases such as real-time messaging, live collaboration, or telemetry data collection.

---

### 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Protocol Buffers (used by gRPC) enforce strict typing and predefined schemas, enabling compile-time validation, efficient binary serialization, and automatic client code generation. This improves performance, minimizes payload size, and reduces runtime errors. However, it introduces more tooling and build complexity compared to JSON, which is human-readable, flexible, and easier to use in rapid prototyping or loosely defined systems. While JSON provides convenience and adaptability, it can lead to inconsistent contracts and subtle bugs, especially in large-scale systems without validation layers.

---
