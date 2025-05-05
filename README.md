# REFLECTION

1) What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

    - Unary RPC involves the client sending a single request and the server returning a single response, which is ideal for straightforward operations like fetching a user profile or submitting a form.
    - Server streaming RPC lets the client send one request and the server send back multiple responses over time, making it perfect for live data feeds or paged results.
    - Bidirectional streaming RPC opens two way streams so both client and server can send messages independently, which works best for real time, interactive applications like chat or collaborative editing

2) What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

    Clients and servers are authenticated using mutual TLS or by validating JWTs in an interceptor. Requests are authorized by checking user roles or permissions before each RPC method executes. All traffic is encrypted with TLS 1.3, and especially sensitive fields can be encrypted within the message payload. Logging, rate limiting, and security checks are centralized in gRPC interceptors, ensuring that no secrets get written to the logs. Certificates are rotated automatically and private keys are stored with strict filesystem permissions. Every incoming protobuf field is validated for size and format to prevent malformed or malicious data

3) What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

    If both client and server send messages at their own speed, the faster side can overwhelm the connection and cause delays or lost messages. Its also tricky to keep messages in the right order and know when to close the chat if someone disconnects or an error happens. Finally, you have to watch for too many open chats eating up memory and set timeouts so old connections don’t stay open forever

4) What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

    Using tokio_stream::wrappers::ReceiverStream makes it very easy to turn an MPSC channel into a gRPC response stream with its built in buffer providing natural backpressure and a clean separation between event generation and transport but it also means you have to pick the right buffer size, manually shut down the sender when the stream is canceled, devise your own way to propagate errors, and accept extra task wake ups compared to a hand rolled stream

5) In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

    Code can be split into distinct self contained components. Place protobuf definitions in one module, set up transport logic in another, implement business logic separately, and keep data models isolated. Define shared behavior using traits so implementations can be swapped for testing or alternate backends without affecting the rest of the code. Move common logic into middleware or utility functions for reuse across the project. Group related features into cohesive modules to keep the codebase tidy. This structure lets multiple developers work in parallel without conflict

6) In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

    Handling more complex payment processing logic may involve validating inputs, integrating with payment gateways, managing transactions in a database, implementing authentication and authorization, handling errors and retries, detecting fraud, logging and monitoring, notifying users, encrypting sensitive data, and designing for scalability and performance

7) What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

    Using gRPC in a distributed system improves communication by using fast, efficient binary messages over HTTP/2 and enforcing strict data structures with Protocol Buffers. It makes it easier for services to work together across different programming languages but requires all systems to follow the same schema. While great for real time, high performance systems, it can be harder to integrate with non gRPC systems without additional tools like gateways

8) What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

    HTTP/2 improves performance with multiplexing, header compression, and bidirectional streaming, making it faster and more efficient than HTTP/1.1. However, it requires more complex infrastructure

9) How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

    REST APIs work on a one request, one response basis over separate HTTP calls, so each new interaction incurs connection and negotiation overhead. gRPC bidirectional streaming keeps a single HTTP/2 connection open, letting client and server send messages asynchronously as events happen. This continuous stream cuts latency and boosts responsiveness for real time use cases like chat or live metrics  

10) What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

    gRPC uses Protocol Buffers, requiring a predefined schema for data, which ensures smaller, faster, and more reliable messages. On the other hand, REST with JSON is more flexible and easier to work with since it doesnt need a fixed structure, but its slower and can lead to more runtime errors. gRPC is ideal for performance and strict data handling, while JSON works well for simplicity and adaptability