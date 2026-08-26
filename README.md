# ⚙️ Library Management System — Config Server

Centralized configuration service for the **Library Management System Microservices Architecture**, built using **Spring Cloud Config Server**.

The Config Server provides a single location for managing configuration across the application's microservices. Configuration is maintained in a dedicated GitHub repository and delivered to services such as the **API Gateway, Eureka Server, User Service, Book Service, and Record Service**.

---

## 📌 Overview

In a microservices architecture, each service requires configuration such as:

* Database connection details
* Service ports
* Eureka configuration
* API Gateway routes
* MongoDB configuration
* MySQL configuration
* Cloud service settings
* Application-specific properties

Instead of maintaining these configurations independently inside every service, this project uses **Spring Cloud Config Server** to centralize configuration management.

```text
                         ┌──────────────────────────┐
                         │     GitHub Repository     │
                         │                          │
                         │ library-config-server-   │
                         │ yml                       │
                         └────────────┬─────────────┘
                                      │
                                      │ Git
                                      ▼
                         ┌──────────────────────────┐
                         │      Config Server       │
                         │          :9000            │
                         │                          │
                         │  Spring Cloud Config     │
                         └────────────┬─────────────┘
                                      │
                ┌─────────────────────┼─────────────────────┐
                │                     │                     │
                ▼                     ▼                     ▼
        ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
        │ User Service │      │ Book Service │      │Record Service│
        └──────────────┘      └──────────────┘      └──────────────┘
```

---

## 🚀 Features

* Centralized configuration management
* Git-based configuration repository
* Spring Cloud Config Server
* Native configuration profile
* Git configuration profile
* Environment-specific configuration
* Centralized microservice configuration
* Integration with Eureka
* Integration with API Gateway
* Spring Boot Actuator
* Cloud-ready configuration architecture
* Reduced configuration duplication

---

## 🛠️ Technology Stack

| Technology           | Version / Usage               |
| -------------------- | ----------------------------- |
| Java                 | 25                            |
| Spring Boot          | 4.0.7                         |
| Spring Cloud         | 2025.1.2                      |
| Spring Cloud Config  | Config Server                 |
| Spring Web           | REST/Web support              |
| Spring Boot Actuator | Monitoring & health checks    |
| Maven                | Build & dependency management |
| Git                  | Version control               |
| GitHub               | Configuration repository      |

---

# 📂 Project Structure

```text
ConfigServer/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── libraryconfigserver/
│   │   │           └── ConfigServerApplication.java
│   │   │
│   │   └── resources/
│   │       └── application.yml
│   │
│   └── test/
│
├── pom.xml
└── README.md
```

---

# ☕ Main Application

The main application class enables Spring Cloud Config Server using `@EnableConfigServer`.

```java
package com.libraryconfigserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

The `@EnableConfigServer` annotation enables the application to act as a **Spring Cloud Config Server**.

---

# ⚙️ Server Configuration

The Config Server runs on port **9000**.

```yaml
server:
  port: 9000
```

### Local URL

```text
http://localhost:9000
```

### Cloud URL

During cloud deployment, other microservices can access the Config Server through:

```text
http://config.platform:9000
```

---

# 🌐 Git Configuration Repository

The Config Server retrieves configuration from the dedicated GitHub repository:

```text
https://github.com/DTY17/library-config-server-yml.git
```

The repository uses the `main` branch.

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/DTY17/library-config-server-yml.git
          default-label: main
          search-paths:
            - configuration
            - platform
            - service
```

### Configuration Search Paths

```text
configuration/
platform/
service/
```

This allows configuration to be separated according to its purpose.

---

# 📁 Configuration Repository Structure

The Git configuration repository follows this structure:

```text
library-config-server-yml/
│
│
├── platform/
│   ├── api-gateway.yml
│   ├── eureka-server.yml
│   └── config-server.yml
│
└── service/
    ├── user-service.yml
    ├── book-service.yml
    └── record-service.yml
```

### Platform Configuration

The `platform` directory contains configuration for infrastructure-level components:

```text
platform/
├── api-gateway.yml
├── eureka-server.yml
└── config-server.yml
```

### Service Configuration

The `service` directory contains configuration for application microservices:

```text
service/
├── user-service.yml
├── book-service.yml
└── record-service.yml
```

---

# 🔄 Git Profile

The Config Server can be configured to use the **Git profile**.

```yaml
spring:
  profiles:
    active: git
```

Git-based configuration:

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/DTY17/library-config-server-yml.git
          default-label: main
          search-paths:
            - configuration
            - platform
            - service
```

When the Config Server starts:

```text
Config Server
      │
      ▼
Connect to GitHub
      │
      ▼
Clone / access repository
      │
      ▼
Search configuration paths
      │
      ▼
Load required configuration
      │
      ▼
Provide configuration to clients
```

---

# 🗂️ Native Profile

The project also supports a **native configuration profile**.

The native profile allows configuration files to be loaded from the application's classpath instead of GitHub.

```yaml
spring:
  config:
    activate:
      on-profile: native

  cloud:
    config:
      server:
        native:
          search-locations:
            - classpath:/configuration
            - classpath:/configuration/platform
            - classpath:/configuration/service
```

This provides an alternative configuration source for local development or environments where Git-based configuration is not required.

---

# 🔍 Configuration Retrieval Flow

The complete configuration retrieval process is:

```text
┌──────────────────────────┐
│    Microservice Client   │
│                          │
│ User / Book / Record     │
└────────────┬─────────────┘
             │
             │ Request configuration
             ▼
┌──────────────────────────┐
│      Config Server       │
│          :9000           │
└────────────┬─────────────┘
             │
             │ Read configuration
             ▼
┌──────────────────────────┐
│   GitHub Configuration   │
│       Repository         │
└────────────┬─────────────┘
             │
             ▼
       Configuration
             │
             ▼
┌──────────────────────────┐
│      Config Server       │
│   Returns configuration  │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│     Microservice         │
│  Starts with centralized │
│      configuration       │
└──────────────────────────┘
```

---

# 🔗 Microservice Integration

Other Spring Boot microservices import their configuration from the Config Server.

For example:

```yaml
spring:
  config:
    import: optional:configserver:http://config.platform:9000
```

For local development:

```yaml
spring:
  config:
    import: optional:configserver:http://localhost:9000
```

The `optional:` prefix allows the application to start even if the Config Server is temporarily unavailable.

For environments where the Config Server is mandatory, the import can be configured without `optional:`.

---

# 📡 Services Using Config Server

The Config Server provides centralized configuration to:

```text
                         ┌────────────────────┐
                         │    Config Server    │
                         │        :9000        │
                         └─────────┬──────────┘
                                   │
             ┌─────────────────────┼─────────────────────┐
             │                     │                     │
             ▼                     ▼                     ▼
      ┌─────────────┐       ┌─────────────┐       ┌─────────────┐
      │   Eureka    │       │ API Gateway │       │User Service │
      │   Server    │       │    :8081    │       │             │
      └─────────────┘       └─────────────┘       └─────────────┘
  
```

---

# 🏗️ Complete Microservices Architecture

The Library Management System follows a distributed microservices architecture.

```text
                              ┌─────────────────┐
                              │    Frontend     │
                              └────────┬────────┘
                                       │
                                       ▼
                              ┌─────────────────┐
                              │   API Gateway   │
                              │      :8081      │
                              └────────┬────────┘
                                       │
                                       ▼
                         ┌──────────────────────────┐
                         │      Eureka Cluster      │
                         │                          │
                         │ Node A :8888             │
                         │ Node B :8888             │
                         │ Node C :8888             │
                         └────────────┬─────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                 │
                    ▼                 ▼                 ▼
             ┌────────────┐    ┌────────────┐    ┌──────────────┐
             │User Service│    │Book Service│    │Record Service│
             └──────┬─────┘    └──────┬─────┘    └──────┬───────┘
                    │                 │                 │
                    ▼                 ▼                 ▼
               ┌─────────┐       ┌─────────┐       ┌─────────┐
               │  MySQL  │       │ MongoDB │       │ MongoDB │
               └─────────┘       └─────────┘       └─────────┘


                         ┌──────────────────┐
                         │  Config Server   │
                         │      :9000       │
                         │config.platform   │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │ GitHub Config    │
                         │   Repository     │
                         └──────────────────┘
```

---

# 📋 Configuration Endpoints

Spring Cloud Config Server provides configuration through standard REST endpoints.

### Application + Profile

```text
GET /{application}/{profile}
```

Example:

```text
http://localhost:9000/book-service/default
```

```text
http://localhost:9000/user-service/default
```

```text
http://localhost:9000/record-service/default
```

### Application + Profile + Label

```text
GET /{application}/{profile}/{label}
```

For example:

```text
http://localhost:9000/book-service/default/main
```

The Config Server uses these values to determine which configuration files should be returned.

#

---

# ▶️ Running the Application

## 1. Clone the Repository

```bash
git clone https://github.com/your-username/config-server.git
```

Navigate into the project:

```bash
cd config-server
```

---

## 2. Build the Project

```bash
mvn clean package
```

---

## 3. Run the Application

Using Maven:

```bash
mvn spring-boot:run
```

Or run the generated JAR:

```bash
java -jar target/config-server.jar
```

The server will be available at:

```text
http://localhost:9000
```

---

# 🧪 Testing

Run the Maven test suite:

```bash
mvn test
```

After starting the application, verify the health endpoint:

```text
http://localhost:9000/actuator/health
```

You can also test configuration retrieval:

```text
http://localhost:9000/book-service/default
```

```text
http://localhost:9000/user-service/default
```

---

# 📦 Maven Dependencies

The project uses the following major dependencies:

* Spring Cloud Config Server
* Spring Boot Starter Web
* Spring Boot Actuator
* Spring Boot DevTools
* Spring Boot Test
* Spring Cloud dependencies
* Maven

Example dependency structure:

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
    </dependency>

</dependencies>
```

---

# ☁️ Cloud Deployment

The Config Server is designed to operate as a centralized service within the cloud infrastructure.

Example cloud architecture:

```text
                     ┌─────────────────────────┐
                     │      Cloud Network       │
                     └────────────┬────────────┘
                                  │
                                  ▼
                     ┌─────────────────────────┐
                     │      Config Server       │
                     │          :9000           │
                     │     config.platform      │
                     └────────────┬────────────┘
                                  │
                                  │ HTTPS / Git
                                  ▼
                     ┌─────────────────────────┐
                     │   GitHub Configuration   │
                     │       Repository         │
                     └─────────────────────────┘
```

Backend services can access the Config Server through:

```text
http://config.platform:9000
```

This allows services running on different VM instances or deployment environments to use the same centralized configuration source.

---

# 🌍 Environment Configuration

The Config Server supports environment-specific configuration using Spring profiles.

For example:

```text
user-service-dev.yml
user-service-test.yml
user-service-prod.yml
```

A service can request a specific profile:

```text
http://localhost:9000/user-service/dev
```

or:

```text
http://localhost:9000/user-service/prod
```

This makes it possible to maintain different settings for development, testing, and production environments.

---

# 🔐 Security Considerations

Sensitive information should **not** be committed directly to the Git configuration repository.

Avoid storing:

```text
Database passwords
MongoDB credentials
Cloud service account keys
API keys
JWT secrets
Private keys
Access tokens
```

For production deployments, sensitive configuration should be provided through secure mechanisms such as:

* Environment variables
* Secret managers
* Cloud Secret Manager
* Encrypted configuration
* Workload identity mechanisms

The Git repository should primarily contain **non-sensitive application configuration**.

---

# 🎯 Responsibilities

The Config Server is responsible for:

* Centralizing microservice configuration
* Loading configuration from GitHub
* Providing configuration to Spring Boot applications
* Supporting Git and native configuration profiles
* Supporting environment-specific configuration
* Reducing duplicated configuration
* Providing centralized configuration endpoints
* Supporting cloud-based microservice deployment
* Providing health monitoring through Actuator

---

# 🔄 Startup Sequence

A typical service startup process is:

```text
1. Microservice starts
          │
          ▼
2. Microservice contacts Config Server
          │
          ▼
3. Config Server reads Git repository
          │
          ▼
4. Required configuration is located
          │
          ▼
5. Configuration is returned
          │
          ▼
6. Microservice loads configuration
          │
          ▼
7. Microservice starts
          │
          ▼
8. Microservice registers with Eureka
```

This approach separates **application configuration** from **application code**.

---

# 📌 Key Benefits

### Centralized Management

Configuration can be managed from one location rather than being duplicated across multiple services.

### Version Control

Git provides configuration history, versioning, and rollback capabilities.

### Environment Support

Different profiles can be used for development, testing, and production.

### Scalability

New microservices can consume configuration from the same Config Server without creating a separate configuration mechanism.

### Cloud Ready

The Config Server can be deployed as a centralized service within the cloud infrastructure.

---

# 📄 License

This project is developed as part of a **Library Management System Microservices Project**.

---

## 👨‍💻 Author

**Dinan Themika Yehansith**

Library Management System — Microservices Architecture
