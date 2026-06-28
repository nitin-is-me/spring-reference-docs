# Spring Boot Documentation

This is **not a Spring Boot course**.

These articles are **reference documents** I wrote while learning Spring Boot to explain concepts that took me a long time to truly understand. They are meant for future revision whenever I forget *why* something exists or *how* different Spring components work together.

Official documentation already explains *what* something is. These articles focus on *why* it exists and the mental models that finally made everything click.

---

# 📖 Reading Order

There is **no fixed order**.

Each article is independent and can be read whenever I need to refresh a specific concept.

However, if I'm completely new to Spring Boot, this order makes the most sense:

1. Beans, IoC and Dependency Injection
2. Spring Request Lifecycle
3. Spring Layers
4. Authentication, Authorization, JWT and Spring Security
5. JDBC, JPA, Hibernate and Drivers
6. JPA Relationships

---

# Articles

---

## 1. Beans, IoC and Dependency Injection

**Read this if you forgot:**

* Why Spring creates objects instead of me.
* What a Bean actually is.
* What IoC (Inversion of Control) means.
* Constructor Injection vs Field Injection.
* Why Dependency Injection exists.
* How Spring wires objects together automatically.

You'll understand:

* `@Component`
* `@Service`
* `@Repository`
* `@Controller`
* `@Autowired`
* Constructor Injection

---

## 2. Spring Request Lifecycle

**Read this if you forgot:**

* What happens after an HTTP request reaches my application.
* Which component executes first.
* Where Filters fit.
* Where Controllers fit.
* Where Services and Repositories execute.
* How the response goes back to the client.

You'll understand the complete request flow from browser → database → browser.

---

## 3. Spring Layers

**Read this if you forgot:**

* Why Spring applications are divided into layers.
* What belongs inside Controller.
* What belongs inside Service.
* What belongs inside Repository.
* Why business logic shouldn't exist inside Controllers.
* Why Controllers shouldn't directly access databases.

This article explains proper separation of responsibilities.

---

## 4. Authentication, Authorization, JWT and Spring Security

**Read this if you forgot:**

* Authentication vs Authorization.
* JWT lifecycle.
* AuthenticationManager.
* AuthenticationProvider.
* UserDetailsService.
* PasswordEncoder.
* SecurityContext.
* JWT Filter.
* Security Filter Chain.
* Why Spring Security uses interfaces everywhere.
* OAuth2 Resource Server.
* Keycloak overview.

This is the main reference for everything related to Spring Security.

---

## 5. JDBC, JPA, Hibernate and Drivers

**Read this if you forgot:**

* How Java talks to PostgreSQL.
* What the PostgreSQL Driver actually does.
* What JDBC is.
* Why JDBC still exists.
* Why Hibernate exists.
* What ORM means.
* Why JPA exists.
* Difference between JPA and Hibernate.
* Spring Data JPA.
* Repository flow.
* Complete database communication pipeline.

This article explains the entire persistence stack from Java code to database.

---

## 6. JPA Relationships

**Read this if you forgot:**

* One-to-One
* One-to-Many
* Many-to-One
* Many-to-Many
* Foreign Keys
* Join Tables
* Owning Side vs Inverse Side
* `mappedBy`
* Cascade Types
* Fetch Types
* When each relationship should be used.

This is the reference whenever designing relational database models using JPA.

---

# 📌 Documentation Philosophy

These articles are **not replacements for official documentation**.

Instead, they explain concepts that were difficult for me to understand.

I intentionally **do not document everything**.

I only document concepts that:

* Took a long time to understand.
* Required building a mental model.
* I'll likely forget in the future.

Everything else can be learned directly from:

* Official Spring Documentation
* Baeldung
* Java Documentation

---

# Future Articles

I'll only add new articles when I encounter concepts that genuinely require deeper understanding.

Possible future topics:

* Spring Transactions (`@Transactional`)
* Spring Profiles
* Caching (Redis)
* Spring Events
* Spring Validation
* Spring Testing
* Spring Boot Auto Configuration
* Concurrency & Async Processing

If official documentation or an existing cheat sheet is already sufficient, I won't duplicate it here.

---

> **Goal:** Build a small but high-quality personal knowledge base that explains *why* Spring works the way it does, rather than rewriting the official documentation.
