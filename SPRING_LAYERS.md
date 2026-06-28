# Spring Layers Explained Like a Normal Human

## The Problem We're Solving

Suppose you want to create a project.

Many beginners write:

```java
@PostMapping("/projects")
public Project createProject(
    @RequestBody CreateProjectRequest request
){
    Project project = new Project();

    project.setName(request.getName());

    projectRepository.save(project);

    return project;
}
```

Looks fine.

Question:

```text
Why do Spring developers keep creating:

Controller
Service
Repository

instead of putting everything here?
```

---

# The Short Answer

Because every layer has one job.

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
```

---

# The Big Picture

```text
Request
 ↓
Controller
 ↓
Service
 ↓
Repository
 ↓
Database
```

---

# What Is A Layer?

A layer is simply:

```text
A section of the application
with a specific responsibility.
```

Think:

```text
Restaurant

Waiter
 ↓
Chef
 ↓
Kitchen Storage
```

Different jobs.

Same business.

---

# Layer 1: Controller

Controller handles:

```text
HTTP
```

Examples:

```text
GET
POST
PUT
DELETE
Headers
Query Parameters
JSON
```

---

Example:

```java
@RestController
@RequestMapping("/projects")
public class ProjectController
{
}
```

---

Question Controller Answers:

```text
What endpoint was called?
What JSON was sent?
What response should be returned?
```

---

Question Controller DOES NOT Answer:

```text
Can user create project?
Can project be deleted?
How is data stored?
```

Those belong elsewhere.

---

# Controller Example

```java
@PostMapping
public Project createProject(
    @RequestBody CreateProjectRequest request
){
    return projectService
        .createProject(request);
}
```

Notice:

```text
Almost no logic.
```

Good.

---

# Layer 2: Service

Service contains:

```text
Business Rules
```

This is the heart of the application.

---

Examples:

```text
Maximum project limit
Permission checks
Validation rules
Ownership checks
```

---

Example:

```java
@Service
public class ProjectService
{
    public Project createProject(
        CreateProjectRequest request
    )
    {
        if(request.getName().isBlank())
        {
            throw new Exception();
        }

        return repository.save(...);
    }
}
```

---

Question Service Answers:

```text
Can this action happen?
What business rules apply?
```

---

Question Service DOES NOT Answer:

```text
How do I read JSON?
How do I send HTTP response?
```

Controller handles that.

---

# Layer 3: Repository

Repository handles:

```text
Database Access
```

Example:

```java
@Repository
public interface ProjectRepository
extends JpaRepository<Project, Long>
{
}
```

---

Question Repository Answers:

```text
How do I store data?
How do I fetch data?
```

---

Question Repository DOES NOT Answer:

```text
Can user create project?
Should project be deleted?
```

Business rules belong in Service.

---

# Why Not Put Everything In Controller?

Because controllers become monsters.

Imagine:

```java
@PostMapping
```

containing:

```text
Validation
Permission Checks
Project Logic
Database Queries
Response Logic
```

inside one method.

---

Soon:

```java
ProjectController.java
```

becomes:

```text
3000 lines
```

😭

---

# Real Example

Bad:

```java
@PostMapping
public Project createProject(...)
{
    validate();

    checkPermissions();

    createProject();

    saveProject();

    sendResponse();
}
```

Everything mixed together.

---

Good:

```java
@PostMapping
public Project createProject(...)
{
    return projectService
        .createProject(...);
}
```

---

Then:

```java
@Service
public Project createProject(...)
{
    validate();

    checkPermissions();

    return repository.save(...);
}
```

Cleaner.

---

# Reusability Problem

Imagine tomorrow:

```text
REST API
CLI Tool
Admin Dashboard
```

all need:

```text
Create Project
```

logic.

---

If logic is inside Controller:

```text
Can't reuse it.
```

---

If logic is inside Service:

```text
Everyone can call Service.
```

---

# TeamVault Example

Imagine:

```text
Create Project
```

logic exists.

---

Controller:

```java
@PostMapping
```

handles:

```text
HTTP Request
```

---

Service:

```java
createProject(...)
```

handles:

```text
Project Rules
```

---

Repository:

```java
save(...)
```

handles:

```text
Database
```

---

Everyone stays in their lane.

---

# The Mental Model

Think:

```text
Controller
=
Waiter

Service
=
Chef

Repository
=
Kitchen Storage

Database
=
Food Warehouse
```

---

Customer:

```text
I want food.
```

---

Waiter:

```text
Receives request.
```

---

Chef:

```text
Decides how to prepare it.
```

---

Storage:

```text
Provides ingredients.
```

---

Warehouse:

```text
Stores ingredients.
```

---

# Why Beginners Get Confused

Because for small projects:

```text
Controller Only
```

works perfectly.

---

The layered architecture becomes useful when:

```text
Application grows
More endpoints appear
More business rules appear
More developers join
```

---

# The Simplest Summary

```text
Controller
=
HTTP Layer

Service
=
Business Logic Layer

Repository
=
Database Layer
```

Controller should know:

```text
How to talk to clients.
```

Service should know:

```text
How the business works.
```

Repository should know:

```text
How data is stored.
```

If you ever wonder where code belongs, ask:

```text
Is this HTTP?
→ Controller

Is this Business Logic?
→ Service

Is this Database Access?
→ Repository
```

That's the entire layered architecture in one rule.
