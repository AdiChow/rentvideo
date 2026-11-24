# RentVideo – Video Rental System

A RESTful API built using Spring Boot, Spring Security, Spring Data JPA, and MySQL for managing a video rental catalogue with role-based access control.

## Tech Stack

- Java 21
- Spring Boot 4.2.0
- Spring Security (HTTP Basic Authentication)
- Spring Data JPA / Hibernate
- MySQL 8.4
- Gradle
- Docker Compose
- BCrypt password hashing

## Features

- User registration with email and password.
- BCrypt password encryption.
- HTTP Basic Authentication.
- Role-based authorization (`CUSTOMER` and `ADMIN`).
- Public registration defaults to `CUSTOMER`.
- Admin account provisioning through environment variables.
- Browse and retrieve videos.
- Admin-only video creation, update, and deletion.
- Request validation and centralized exception handling.
- Appropriate HTTP status codes.

## Prerequisites

- Java 21
- Docker Desktop / Docker Engine
- Git

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/AdiChow/rentvideo.git
cd rentvideo
```

### 2. Configure environment variables

Create your local environment file:

```bash
cp .env.example .env
```

Update `.env` with your own credentials:

```properties
MYSQL_ROOT_PASSWORD=your_root_password
MYSQL_PASSWORD=your_mysql_password
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=your_admin_password
```

Do not commit your `.env` file.

### 3. Start MySQL using Docker Compose

```bash
docker compose up -d
```

This starts MySQL 8.4 and creates the `rentvideo_db` database.

### 4. Run the application

```bash
./gradlew bootRun
```

The application starts at:

`http://localhost:8080`

Hibernate automatically manages the database schema using the configured JPA settings.

## API Endpoints

### Authentication

| Method | Endpoint | Description | Access |
|---|---|---|---|
| POST | `/auth/register` | Register a user | Public |

Example registration request:

```json
{
  "email": "customer@example.com",
  "password": "StrongPass123",
  "firstName": "John",
  "lastName": "Doe"
}
```

The role defaults to `CUSTOMER`. Public registration does not permit ADMIN self-assignment.

### Video Management

| Method | Endpoint | Description | Access |
|---|---|---|---|
| GET | `/videos` | Get all videos | CUSTOMER, ADMIN |
| GET | `/videos/{id}` | Get video by ID | CUSTOMER, ADMIN |
| POST | `/videos` | Add a video | ADMIN |
| PUT | `/videos/{id}` | Update a video | ADMIN |
| DELETE | `/videos/{id}` | Delete a video | ADMIN |

Example video request:

```json
{
  "title": "Interstellar",
  "director": "Christopher Nolan",
  "genre": "Sci-Fi"
}
```

New videos are created with availability set to `true`.

## Authentication

The application uses HTTP Basic Authentication.

Credentials are supplied with each request using the user's registered email and password.

Example:

```bash
curl -u customer@example.com:StrongPass123 \
http://localhost:8080/videos
```

An ADMIN account is provisioned automatically at application startup using `ADMIN_EMAIL` and `ADMIN_PASSWORD` from the environment.

## Authorization Rules

| Operation | CUSTOMER | ADMIN |
|---|---|---|
| Register | Yes | Yes |
| Browse videos | Yes | Yes |
| View video by ID | Yes | Yes |
| Create video | No | Yes |
| Update video | No | Yes |
| Delete video | No | Yes |

## HTTP Status Codes

| Status | Meaning |
|---|---|
| 200 | Request successful |
| 201 | Resource created |
| 204 | Resource deleted successfully |
| 400 | Invalid request or validation failure |
| 401 | Authentication required or invalid credentials |
| 403 | Insufficient permissions |
| 404 | Resource not found |
| 409 | Duplicate email registration |

## Project Structure

```text
src/main/java/com/example/rentvideo/
├── config/
├── controller/
├── dto/
├── entity/
├── exception/
├── repository/
├── security/
└── service/
```

The application follows a layered architecture:

- **Controller:** Handles HTTP requests and responses.
- **Service:** Contains business logic.
- **Repository:** Handles database operations.
- **Entity:** Represents database entities.
- **DTO:** Defines request and response objects.
- **Security:** Handles authentication and authorization.
- **Exception:** Centralizes API exception handling.

## Stopping MySQL

```bash
docker compose down
```

To remove the database volume and all persisted MySQL data:

```bash
docker compose down -v
```

## License

This project was developed as a take-home assignment.
