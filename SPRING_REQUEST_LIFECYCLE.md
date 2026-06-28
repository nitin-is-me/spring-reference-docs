# Spring Request Lifecycle Explained Like a Normal Human

## The Problem We're Solving

Suppose somebody visits:

```http
GET /api/projects
```

Question:

```text
How does this request travel through Spring?
```

Most Spring tutorials show:

```java
@GetMapping("/projects")
public List<Project> getProjects()
{
    return projectService.getProjects();
}
```

but never explain:

```text
Who calls this method?
How did Spring find it?
How did JSON become Java?
How did Java become JSON?
```

This document answers that.

---

# The Big Picture

A request usually travels like this:

```text
Browser / Client
        ↓
Tomcat
        ↓
Filters
        ↓
Spring DispatcherServlet
        ↓
Controller
        ↓
Service
        ↓
Repository
        ↓
Database
        ↓
Repository
        ↓
Service
        ↓
Controller
        ↓
JSON Response
        ↓
Client
```

---

# Step 1: Client Sends Request

Example:

```http
GET /api/projects
Authorization: Bearer xyz
```

This leaves the browser.

---

# Step 2: Tomcat Receives Request

Spring Boot contains an embedded server.

Usually:

```text
Tomcat
```

Think:

```text
Mini Web Server
```

inside your application.

When Spring Boot starts:

```java
@SpringBootApplication
```

Tomcat starts automatically.

---

Without Tomcat:

```text
No HTTP Requests
No API
```

---

# Step 3: Filters Run

Before reaching your controller:

```text
Request
↓
Filter 1
↓
Filter 2
↓
JwtAuthenticationFilter
```

Filters are similar to:

```js
app.use(...)
```

in Express.

---

Example:

```java
JwtAuthenticationFilter
```

might:

```text
Read JWT
Validate JWT
Store User
```

before allowing the request through.

---

# Step 4: DispatcherServlet

This is the real boss.

Every request reaches:

```text
DispatcherServlet
```

Think:

```text
Traffic Controller
```

for all HTTP requests.

---

Its job:

```text
Find correct controller
Call controller
Return response
```

---

# Example

Request:

```http
GET /api/projects
```

DispatcherServlet asks:

```text
Which controller handles /api/projects?
```

---

Spring finds:

```java
@RestController
public class ProjectController
{
    @GetMapping("/projects")
    public List<Project> getProjects()
    {
    }
}
```

and calls it.

---

# Step 5: Controller

Controller handles:

```text
HTTP Layer
```

Example:

```java
@GetMapping("/projects")
public List<Project> getProjects()
{
    return projectService.getProjects();
}
```

Controller should NOT contain:

```text
Database Logic
Business Logic
```

Its job is:

```text
Receive Request
Call Service
Return Response
```

---

# Step 6: Service Layer

Controller calls:

```java
projectService.getProjects()
```

Service contains:

```text
Business Logic
```

Example:

```text
Project Creation Rules
Permission Checks
Validation
```

---

Think:

```text
Controller
=
HTTP

Service
=
Business Rules
```

---

# Step 7: Repository Layer

Service calls:

```java
projectRepository.findAll()
```

Repository handles:

```text
Database Communication
```

Usually through:

```text
Spring Data JPA
Hibernate
```

---

# Step 8: Database

Eventually:

```text
Repository
↓
Hibernate
↓
JDBC
↓
PostgreSQL Driver
↓
PostgreSQL
```

SQL executes.

Data returns.

---

# Return Journey

Now the process reverses.

---

Database returns:

```text
Rows
```

---

Hibernate converts:

```text
Rows
↓
Java Objects
```

---

Repository returns:

```java
List<Project>
```

---

Service returns:

```java
List<Project>
```

---

Controller returns:

```java
List<Project>
```

---

# Step 9: JSON Conversion

Question:

```text
Who converts Java objects into JSON?
```

Answer:

```text
Jackson
```

Spring Boot includes Jackson automatically.

---

Example:

```java
return project;
```

becomes:

```json
{
  "id": 1,
  "name": "TeamVault"
}
```

automatically.

---

You never write:

```java
toJson(...)
```

yourself.

Spring does it.

---

# Step 10: Response Sent

Finally:

```json
[
  {
    "id": 1,
    "name": "Project A"
  }
]
```

returns to the client.

Request complete.

---

# TeamVault Example

Request:

```http
GET /api/projects
```

Flow:

```text
Browser
↓
Tomcat
↓
JwtAuthenticationFilter
↓
DispatcherServlet
↓
ProjectController
↓
ProjectService
↓
ProjectRepository
↓
PostgreSQL
↓
Repository
↓
Service
↓
Controller
↓
Jackson
↓
JSON
↓
Browser
```

---

# Express Equivalent

Express:

```js
Request
↓
Middleware
↓
Route Handler
↓
Service
↓
Database
↓
JSON Response
```

---

Spring:

```text
Request
↓
Filter
↓
DispatcherServlet
↓
Controller
↓
Service
↓
Repository
↓
Database
↓
JSON Response
```

Very similar architecture.

Different names.

---

# The Simplest Mental Model

Whenever a request arrives:

```text
Filters
↓
DispatcherServlet
↓
Controller
↓
Service
↓
Repository
↓
Database
```

Whenever a response leaves:

```text
Database
↓
Repository
↓
Service
↓
Controller
↓
Jackson
↓
JSON
```

If you remember only one thing:

```text
Controller
=
HTTP

Service
=
Business Logic

Repository
=
Database

DispatcherServlet
=
Traffic Controller

Jackson
=
Java ↔ JSON Converter
```
