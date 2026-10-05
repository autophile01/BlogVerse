# BolgVerse - Blogging Backend Application

A production-oriented **RESTful blogging backend** built with **Java and Spring Boot**. The application provides APIs for user management, blog posts, categories, comments, authentication, authorization, pagination, searching, sorting, validation, image uploads, and API documentation.

The project follows a layered backend architecture with **Controller → Service → Repository → Database** separation and uses **JWT-based stateless authentication** with role-based access control.

---

## 🚀 Features

### 👤 User Management
- User registration
- User login using JWT authentication
- Get user by ID
- Get all users
- Update user profile
- Delete users
- Password encryption using BCrypt
- DTO-based request/response handling

### 📝 Blog Post Management
- Create a blog post
- Update a blog post
- Delete a blog post
- Get a single post
- Get all posts
- Get posts by category
- Get posts created by a specific user
- Search posts using keywords
- Pagination
- Dynamic sorting in ascending/descending order
- Default image support for posts

### 💬 Comments
- Add comments to posts
- Delete comments
- Associate comments with their corresponding posts

### 🗂️ Categories
- Create categories
- Update categories
- Delete categories
- Get category by ID
- Get all categories

### 🔐 Security
- JWT authentication
- Stateless authentication
- BCrypt password hashing
- Role-based authorization
- Custom `UserDetailsService`
- Custom JWT authentication filter
- Custom authentication entry point
- Protected API endpoints

### 📄 Pagination, Search & Sorting

The post API supports configurable pagination and sorting.

Example:

```text
GET /api/post?pageNumber=0&pageSize=5&sortBy=postId&sortDir=asc
```

This allows clients to retrieve posts page-by-page instead of loading the complete dataset at once.

### 🖼️ Image Upload
- Upload images for blog posts
- UUID-based file names to avoid duplicate file names
- Configurable upload directory
- Retrieve stored images through API endpoints

### 📚 API Documentation
- Swagger / OpenAPI integration
- Interactive API documentation for testing endpoints

---

## 🏗️ Architecture

```text
                Client / Postman / Swagger
                         │
                         ▼
                 ┌─────────────────┐
                 │   Controllers   │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │    Services     │
                 │ Business Logic  │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │   Repositories  │
                 │ Spring Data JPA │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │     MySQL       │
                 └─────────────────┘

Security Flow:

Request
   │
   ▼
JWT Authentication Filter
   │
   ├── Invalid / Missing Token ──► Authentication Entry Point
   │
   ▼
Authenticated User
   │
   ▼
Controller
```

---

## 🧰 Tech Stack

| Technology | Purpose |
|---|---|
| Java 20 | Backend development |
| Spring Boot 3.1.3 | Application framework |
| Spring Web | REST APIs |
| Spring Data JPA | Database access |
| Hibernate | ORM |
| Spring Security | Authentication & authorization |
| JWT | Stateless authentication |
| BCrypt | Password hashing |
| MySQL | Relational database |
| ModelMapper | Entity ↔ DTO mapping |
| Jakarta Validation | Request validation |
| Lombok | Boilerplate reduction |
| SpringDoc OpenAPI | Swagger documentation |
| Maven | Dependency management & build |
| Postman | API testing |

---

## 📁 Project Structure

```text
src/
└── main/
    ├── java/com/blog/
    │   ├── config/
    │   │   ├── AppConstants.java
    │   │   ├── ContentConfig.java
    │   │   ├── SecurityConfig.java
    │   │   └── SwaggerConfig.java
    │   │
    │   ├── controllers/
    │   │   ├── AuthController.java
    │   │   ├── CategoryController.java
    │   │   ├── CommentController.java
    │   │   ├── PostController.java
    │   │   └── UserController.java
    │   │
    │   ├── entities/
    │   ├── exceptions/
    │   ├── payloads/
    │   ├── repositories/
    │   ├── security/
    │   └── service/
    │       ├── impl/
    │       ├── CategoryService.java
    │       ├── CommentService.java
    │       ├── FileService.java
    │       ├── PostService.java
    │       └── UserService.java
    │
    └── resources/
        ├── application.properties
        ├── application-dev.properties
        └── application-prod.properties
```

---

## 🗃️ Database Design

The application uses MySQL with JPA/Hibernate.

### Main entities

```text
User
 │
 ├──────────────< Post
 │                  │
 │                  └────── Category
 │
 └──────────────< Comment >──── Post
```

### User
Stores:
- User ID
- Name
- Email
- Password
- About
- Roles

### Post
Stores:
- Post ID
- Title
- Content
- Image
- Added date
- Author
- Category

### Category
Stores:
- Category ID
- Category title
- Category description

### Comment
Stores:
- Comment ID
- Content
- Associated post

Relationships are managed using JPA/Hibernate mappings.

---

## 🔐 Authentication Flow

The application uses JWT for stateless authentication.

```text
1. User registers
        ↓
2. Password is encrypted using BCrypt
        ↓
3. User logs in
        ↓
4. Credentials are authenticated
        ↓
5. JWT token is generated
        ↓
6. Client sends JWT with protected requests
        ↓
7. JWT Authentication Filter validates token
        ↓
8. Request is allowed or rejected
```

Protected requests should include:

```text
Authorization: Bearer <JWT_TOKEN>
```

The application uses a stateless session policy, so authentication is handled through the JWT rather than server-side sessions.

---

## 🔎 Search Flow

Post searching is handled through the service/repository layer.

Example:

```text
GET /api/post/search/{keyword}
```

The keyword is passed to the backend, where matching posts are retrieved and converted into DTOs before being returned to the client.

---

## 📄 Pagination & Sorting

Instead of returning every post in a single response, the API uses Spring Data's `Pageable` abstraction.

Conceptually:

```java
Pageable pageable =
    PageRequest.of(pageNumber, pageSize, sort);
```

The response contains information such as:

- Current page number
- Page size
- Total pages
- Total elements
- Whether the current page is the last page
- Posts belonging to the current page

This is useful for building scalable APIs when the number of posts grows significantly.

---

## 🔄 DTO Pattern

The project separates database entities from API payloads.

```text
Client
  │
  ▼
DTO
  │
  ▼
Service
  │
  ▼
Entity
  │
  ▼
Repository
  │
  ▼
MySQL
```

`ModelMapper` is used to simplify conversion between entities and DTOs.

This prevents database entities from being unnecessarily exposed directly through the API layer.

---

## ⚠️ Exception Handling

The project includes custom exception handling for cases such as:

- Resource not found
- Invalid API requests
- Authentication failures
- Validation errors

For example, when a post or user does not exist, the service layer throws a custom `ResourceNotFoundException`.

---

## 🖼️ File Upload

Uploaded images are stored using a configurable directory:

```properties
project.image=images/
```

To prevent collisions when multiple users upload files with the same name, the application generates a UUID-based file name.

Example:

```text
profile.jpg
      ↓
UUID-profile.jpg
```

---

## 📖 API Documentation

Swagger/OpenAPI is integrated into the project to make API exploration and testing easier.

After starting the application, open the Swagger UI endpoint configured by SpringDoc, typically:

```text
http://localhost:8080/swagger-ui/index.html
```

---

## ⚙️ Prerequisites

Make sure the following are installed:

- Java 20 or a compatible Java version supported by the project
- Maven (or use the included Maven Wrapper)
- MySQL
- Postman (optional)
- Git

---

## 🗄️ MySQL Setup

Create the database:

```sql
CREATE DATABASE blog_app_apis;
```

Then configure your database connection using environment-specific Spring Boot configuration.

**Do not commit real database passwords to GitHub.**

For local development, the project expects a MySQL database similar to:

```text
Host: 127.0.0.1
Port: 3306
Database: blog_app_apis
```

---

## ▶️ Running the Application

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd Blogging-Application
```

### 2. Configure database credentials

Set your local MySQL username/password in your local Spring configuration.

For a public GitHub repository, use environment variables or an ignored local configuration file rather than committing credentials.

### 3. Start the application

Using Maven Wrapper on Windows:

```bash
mvnw.cmd spring-boot:run
```

On macOS/Linux:

```bash
./mvnw spring-boot:run
```

Or with Maven:

```bash
mvn spring-boot:run
```

The application runs on:

```text
http://localhost:8080
```

---

## 🧪 Testing

The project includes Spring Boot test support.

You can run tests using:

```bash
mvnw.cmd test
```

You can also test the REST APIs using:

- Postman
- Swagger UI
- Any REST client

---

## 🔑 Important API Areas

The application is organized around the following API resources:

```text
/api/auth
/api/user
/api/post
/api/category
/api/comment
```

Typical operations include:

```text
POST   → Create
GET    → Read
PUT    → Update
DELETE → Delete
```

Exact endpoint paths should be checked in the controller classes and Swagger documentation.

---

## 🧠 Backend Concepts Demonstrated

This project demonstrates several important Spring Boot backend concepts:

- REST API development
- Layered architecture
- Dependency Injection
- Spring Data JPA
- Hibernate ORM
- Entity relationships
- DTO pattern
- ModelMapper
- Spring Security
- JWT authentication
- BCrypt password hashing
- Role-based authorization
- Stateless authentication
- Custom security filters
- Pagination
- Sorting
- Searching
- File upload
- Input validation
- Custom exceptions
- Global exception handling
- Swagger/OpenAPI
- Maven dependency management
- MySQL integration

---

## 📈 Future Improvements

For a stronger production-ready implementation, the following improvements can be added:

- Unit and integration test coverage
- Refresh-token based authentication
- JWT secret management through environment variables
- More granular role/permission checks
- Ownership checks for update/delete operations
- Centralized API response format
- Rate limiting
- Docker support
- CI/CD pipeline
- Cloud image storage such as S3
- Database indexing for frequently searched fields
- Caching using Redis
- Structured application logging
- API versioning
- Production monitoring and health checks

---

## 👨‍💻 Why This Project Is Resume-Worthy

This project goes beyond basic CRUD by implementing backend concepts commonly used in real applications:

**Authentication + Authorization + JWT + JPA Relationships + Pagination + Search + Sorting + Validation + File Upload + Exception Handling + API Documentation**

It demonstrates the ability to design and implement a structured Spring Boot REST API rather than only building simple database CRUD endpoints.

---

## 📜 License

This project is intended for learning and portfolio purposes.
