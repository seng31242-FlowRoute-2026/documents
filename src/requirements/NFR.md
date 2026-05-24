# SRS Section 5: Non-Functional Requirements (NFRs)

## 5.1 Performance Requirements
* **NFR-PER-01 Dashboard Latency:** The internal Pod Workload Dashboard must refresh and display data updates in real-time, with an end-to-end data propagation latency not exceeding 1.5 seconds from the moment a task status change is logged in the database.
* **NFR-PER-02 Concurrency:** The platform must support up to 50 concurrent active connections from internal editors and administrators updating statuses simultaneously without degrading dashboard performance.
* **NFR-PER-03 Media Processing:** Database writes and indexing for uploaded video files must complete within 2 seconds of the file successfully reaching the storage bucket cloud destination.

## 5.2 Security Requirements
* **NFR-SEC-01 Access Control:** Authentication must be enforced for all system interfaces. Clients shall only access their own uploaded assets and briefs, while Editing Pod members shall only access tasks actively routed to their respective pods.
* **NFR-SEC-02 Data Retention & Lifespan:** To comply with early prototype infrastructure limitations, all uploaded video assets must be automatically and permanently deleted from the cloud storage system exactly 7 days after their initial upload timestamp.
* **NFR-SEC-03 Data Protection:** All data transfers containing user credentials, project briefs, or video download tokens must be encrypted in transit utilizing Transport Layer Security (TLS 1.3).

## 5.3 Usability Requirements
* **NFR-USA-01 Learnability:** The platform interface must be intuitive enough that an internal editor can view their task queue, open a project brief, and update a video workflow status with less than 10 minutes of initial onboarding.
* **NFR-USA-02 Error Prevention:** The client asset upload portal must clearly display file constraints prior to initiating an upload, and immediately halt files that violate protocol with an explanatory user error message.

## 5.4 Scalability & Storage Constraints
* **NFR-SCA-01 Prototype Size Limit:** To manage storage overhead and stick strictly within the defined AWS prototype tier budget caps, the system must enforce a hard constraint rejecting any individual video asset upload that exceeds a maximum file size of 50 Megabytes (50MB).
* **NFR-SCA-02 Architecture Extensibility:** The backend routing logic and data models must be structurally decoupled so that additional editing pods (beyond the initial 8 core pods) can be added to the infrastructure with zero changes to the underlying database schema.