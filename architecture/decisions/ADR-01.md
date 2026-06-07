# ADR-01: Separation of API and Routing Engine

Date: 2026-06-06
Status: Proposed

## Context

FlowRoute has two distinct technical demands. The Client Intake Portal and Pod Workload Dashboard require high-concurrency, real-time I/O capabilities. Conversely, the "Smart Pipeline" Routing Engine requires complex algorithmic processing, array manipulation, and potential future integration with AI/ML libraries to handle capacity sorting and tie-breaker logic.

## Decision

We will adopt a Service-Oriented Architecture (SOA), separating the backend into two distinct components:

1. **API/WebSocket Layer:** Built with Node.js (Express).
2. **Algorithmic Routing Engine:** Built with Python (FastAPI).

To handle communication between these services without blocking the main web threads, they will communicate asynchronously via a **Message Queue (e.g., RabbitMQ or Redis)**. Node.js will push tasks to the queue, and the Python engine will consume them, process the routing, and return the assignment.

## Rationale

This separation of concerns is explicitly chosen to maximize **Scalability** and **Maintainability**.

- Node.js handles thousands of concurrent connections and I/O operations (like video uploads and dashboard WebSockets) highly efficiently.
- Python is the industry standard for computational flexibility and data processing.
  By separating them, if video uploads surge, we can horizontally scale the Node.js containers. If the routing queue backs up, we can spin up additional Python workers independently, optimizing our server resource usage.

## Alternatives Considered

| Alternative                    | Pros                                                          | Cons                                                                                                                   | Reason Rejected                                                                  |
| ------------------------------ | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Monolithic Node.js Application | Single codebase, simplified deployment.                       | Single-threaded nature means heavy algorithmic calculations could block the event loop, delaying real-time UI updates. | Fails to provide the computational flexibility required by the routing engine.   |
| Monolithic Python Application  | Excellent for algorithmic logic and potential AI integration. | Less efficient at managing thousands of concurrent WebSocket connections compared to Node.js.                          | API responsiveness and real-time dashboard updates could suffer under high load. |

## Consequences

- **Easier:** Scaling specific parts of the system independently. Writing complex routing logic is much easier in Python.
- **More difficult:** Deployment complexity increases. We now have to manage, monitor, and deploy two separate runtimes (Node and Python) as well as maintain a robust Message Queue infrastructure.

## References

- SENG 31242 Course Guidelines: Section 7.2.1
- SRS Document: UC-02 (Automated Task Routing)
- SDS Document: Section 3 (Architectural Design)
