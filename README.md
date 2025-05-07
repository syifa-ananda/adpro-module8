## Reflection

**1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?**

A unary call corresponds to a single request and a single response, much like invoking a synchronous function, it’s ideal for simple lookups or updates. Server‐streaming sends one request but returns a sequence of messages, useful for delivering logs, notifications, or large datasets in chunks. Bidirectional streaming allows both client and server to send independent message streams over a single connection, which is best suited for real‑time interactions such as chat or collaborative applications.

**2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?**

To secure a Rust gRPC service, we should enforce strong authentication (for example, validating JWTs or using mutual TLS certificates), apply role‑based authorization checks in interceptors, and ensure all traffic is encrypted via TLS (using crates like rustls). We should also mitigate replay attacks, enforce a regular certificate‑rotation policy, and perform ongoing log audits to detect any unauthorized access.

**3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?**

It can get tricky, we have got to balance the flow so neither side overwhelms the other, shut down streams cleanly, and bounce back from errors. In Rust that means coordinating our `Stream` and `Sink` to avoid deadlocks, handling network hiccups smoothly, and keeping messages in order if our chat needs it.

**4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?**

`ReceiverStream` makes it super easy to turn a Tokio mpsc channel into a gRPC stream with minimal code. But since it uses the channel’s built‑in buffering, a slow consumer can let messages pile up or even drop them, and we give up some of the fine‑grained control we would have by writing our own Stream.

**5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?**

Break our Protobufs into per‑service files, keep our business logic in its own modules (like auth or billing), and share common traits for things like logging or errors. That way, we can swap out or extend parts without touching the main server boilerplate.

**6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?**

Beyond simple debit/credit, we would add idempotency tokens so we don’t process things twice, plug in retry and circuit‑breaker logic, keep a persistent audit trail, and hook into fraud‑detection systems. We might even layer in a saga or two‑phase commit for distributed workflows and set up detailed monitoring.

**7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?**

Using gRPC means we define our API in Protobuf first, generate stubs for clients and servers, and get type safety everywhere. We also get built‑in streaming and skip writing serializers, though it does add a code‑gen step and means we have to manage our Protobuf versions carefully.

**8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?**

HTTP/2 provides multiplexed streams over a single connection, header compression, and built‑in flow control—features that align perfectly with gRPC’s needs. While one can approximate persistent, bidirectional communication using HTTP/1.1 with WebSockets, that approach requires building custom flow‑control logic and header optimizations that HTTP/2 handles natively.

**9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?**

With REST we fire off a request and wait for a single response, perfect for CRUD. gRPC’s streaming frees us from per‑call handshakes, letting us push and pull messages continuously for real‑time updates with lower latency.

**10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?**

Protocol Buffers enforce a well‑defined, strongly typed schema that compiles to efficient binary on the wire, offering versioning guarantees and faster parsing. JSON in REST offers more flexibility by allowing arbitrary fields and loose typing, but that comes at the cost of larger payloads, runtime parsing errors, and less compile‑time validation.