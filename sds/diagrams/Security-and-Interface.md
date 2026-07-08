# SDS: Security and Interface Design

This document outlines the security protocols and REST API endpoints for the FlowRoute system.

## 6. Security Design

### 6.1 Authentication and Sessions (JWT)
The FlowRoute system uses JSON Web Tokens (JWT) to securely manage user sessions. 
* When a Client or Product Owner logs in successfully, the Node.js API generates a unique JWT.
* The React SPA stores this token securely and sends it in the `Authorization` header (as a Bearer token) with every API request.
* The Node.js API uses a security middleware to verify the JWT signature before processing any request. If the token is missing or expired, the system returns a `401 Unauthorized` error.

### 6.2 File Upload Security (Addressing OWASP Top 10)
Because FlowRoute handles external video uploads, we have strict rules to prevent common security vulnerabilities:
* **Broken Access Control (A01):** The Node.js API always checks the user ID inside the JWT against the database. A client can only access or download tasks that belong to their specific account ID.
* **Cryptographic Failures (A02):** All video files and brief data are transferred using secure TLS 1.3 encryption to protect data in transit.
* **Injection & Malicious Uploads (A03):** To prevent malicious scripts from being uploaded to our AWS S3 storage, the system strictly validates file formats. It only accepts `.mp4`, `.mov`, and `.avi` files. 
* **Security Misconfiguration (A05):** We strictly enforce the 50MB file size limit at the API Gateway level to prevent attackers from crashing the server with massive file uploads.

---

## 7. Interface Design

### 7.1 Intake Portal REST API Endpoints
The following endpoints define the communication between the React SPA and the Node.js API for the Client Video Intake process (UC-01).

#### Endpoint: Create New Video Task
* **URI:** `/api/tasks`
* **Method:** `POST`
* **Purpose:** Creates a new project record in MongoDB and returns a secure S3 link for the heavy video upload.
* **Headers:** `Authorization: Bearer <token>`

**Request Payload (JSON):**
```json
{
  "projectTitle": "String",
  "priorityLevel": "String",
  "briefNotes": "String",
  "fileFormat": "String",
  "fileSizeMB": "Number"
}
```

**Success Response (201 Created):**

```json
{
  "taskId": "String",
  "status": "Queued",
  "uploadUrl": "String (AWS S3 Presigned URL)",
  "message": "Task created successfully. Proceed with file upload."
}
```
**Error Response (400 Bad Request):
Returned if the file size exceeds 50MB or if the format is invalid.**

#### Endpoint: Get Client Dashboard Tasks
* **URI:** `/api/tasks/client`
* **Method:** `GET`
* **Purpose:** Fetches all active and completed video tasks belonging to the logged-in client.
* **Headers:** `Authorization: Bearer <token>`

**Success Response (200 OK):**
```json
[
  {
    "taskId": "String",
    "projectTitle": "String",
    "status": "In Progress",
    "assignedPod": "String"
  }
]
```

### 7.2 Real-Time Communication & Notifications
To handle instant updates without page reloads, the system uses two additional interface protocols alongside the standard REST API:

#### WebSockets (Real-Time UI Updates)
* **Protocol:** `ws://` / `wss://`
* **Purpose:** Maintains a constant, two-way connection between the React SPA and the Node.js API.
* **Use Case:** When the Python Routing Engine assigns a video to a pod and updates the database, the Node.js backend instantly pushes this state change through the WebSocket. The Product Owner's dashboard UI updates immediately to show the new assignment without needing to refresh the page.

#### Webhooks (External Pod Notifications)
* **Protocol:** HTTP `POST`
* **Purpose:** Sends automated server-to-server requests to external URLs.
* **Use Case:** When a new video task is assigned, the backend looks up the specific Webhook URL for that Editing Pod in the database. It then sends a secure `POST` request with a JSON payload to that URL to alert the pod's external channel (like a messaging app or their own internal system) about the new task.

