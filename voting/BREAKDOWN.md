# Voting Service - Contents Breakdown

## Overview
The `voting` folder contains a Spring Boot microservice application that handles voting functionality for origami products in the Craftista platform. This service allows users to vote on their favorite origamis and synchronizes product data from a catalogue service.

## Directory Structure

```
voting/
├── src/
│   ├── main/
│   │   ├── java/com/example/voting/
│   │   │   ├── VotingApplication.java           # Main Spring Boot application class
│   │   │   ├── config/                          # Configuration classes
│   │   │   │   ├── AppConfig.java              # Bean configurations (RestTemplate)
│   │   │   │   └── AppProperties.java          # External service URL configuration
│   │   │   ├── controller/                     # REST API controllers
│   │   │   │   ├── VotingController.java       # Main voting API endpoints
│   │   │   │   └── CustomErrorController.java  # Custom error handling
│   │   │   ├── model/                          # Data models
│   │   │   │   └── Origami.java               # Origami entity with JPA annotations
│   │   │   ├── repository/                     # Data access layer
│   │   │   │   └── OrigamiRepository.java     # JPA repository for Origami entities
│   │   │   └── service/                        # Business logic layer
│   │   │       ├── OrigamiService.java        # Core origami business logic
│   │   │       └── OrigamiSynchronizationService.java # Scheduled sync with catalogue
│   │   └── resources/
│   │       ├── application.properties          # Application configuration
│   │       └── templates/                      # Thymeleaf HTML templates
│   │           ├── welcome.html               # Welcome page template
│   │           └── error.html                 # Error page template
│   └── test/
│       └── java/com/example/voting/
│           └── VotingApplicationTests.java     # Test suite (placeholder tests)
├── pom.xml                                     # Maven build configuration
├── mvnw                                        # Maven wrapper script (Unix)
├── mvnw.cmd                                    # Maven wrapper script (Windows)
├── README.md                                   # Build and deployment instructions
├── HELP.md                                     # Spring Boot help documentation
└── .gitignore                                  # Git ignore patterns
```

## Core Components

### 1. Application Entry Point
- **VotingApplication.java**: Main Spring Boot application class with `@SpringBootApplication` and `@EnableScheduling` annotations

### 2. Configuration Layer
- **AppConfig.java**: Defines Spring beans including `RestTemplate` for HTTP communications
- **AppProperties.java**: Handles external configuration properties with `@ConfigurationProperties` for catalogue service URL

### 3. Web Layer (Controllers)
- **VotingController.java**: REST API controller providing endpoints for:
  - `GET /api/origamis` - Retrieve all origamis
  - `GET /api/origamis/{id}` - Get specific origami by ID
  - `GET /api/origamis/{id}/votes` - Get vote count for specific origami
  - `POST /api/origamis` - Add new origami
  - `POST /api/origamis/{id}/vote` - Vote for specific origami
- **CustomErrorController.java**: Custom error handling for HTTP error responses

### 4. Data Layer
- **Origami.java**: JPA entity representing origami products with fields:
  - `origamiId` (Long) - Primary key
  - `name` (String) - Origami name
  - `votes` (int) - Vote count
- **OrigamiRepository.java**: JPA repository interface extending `JpaRepository` with custom query method `countByOrigamiId`

### 5. Business Logic Layer
- **OrigamiService.java**: Core business logic for origami operations including vote retrieval
- **OrigamiSynchronizationService.java**: Scheduled service that:
  - Fetches origami data from catalogue service every minute
  - Synchronizes local database with remote catalogue
  - Handles data consistency and error recovery

## Configuration Files

### application.properties
```properties
# External service integration
catalogue.service-url=http://catalogue:5000/api/products

# H2 Database configuration
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password

# JPA/Hibernate settings
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
```

### pom.xml Dependencies
- **Spring Boot Starter Web**: REST API framework
- **Spring Boot Starter Data JPA**: Database ORM
- **Spring Boot Starter Thymeleaf**: Template engine
- **H2 Database**: In-memory database for runtime
- **Lombok**: Code generation for boilerplate reduction
- **SLF4J/Logback**: Logging framework
- **Spring Boot Starter Test**: Testing framework

## API Endpoints

| Method | Endpoint | Description | Response |
|--------|----------|-------------|----------|
| GET | `/api/origamis` | Get all origamis | List of origami objects |
| GET | `/api/origamis/{id}` | Get origami by ID | Single origami object |
| GET | `/api/origamis/{id}/votes` | Get vote count | Integer vote count |
| POST | `/api/origamis` | Add new origami | Created origami object |
| POST | `/api/origamis/{id}/vote` | Vote for origami | Updated origami object |

## Key Features

### 1. Data Synchronization
- Scheduled synchronization with catalogue service every 60 seconds
- Fetches product data from `http://catalogue:5000/api/products`
- Updates local database while preserving vote counts
- Error handling for network failures

### 2. Voting System
- Increment vote count for specific origami products
- Persistent vote storage in H2 database
- Vote retrieval API for analytics

### 3. Database Management
- H2 in-memory database for development/testing
- JPA/Hibernate ORM for data persistence
- Automatic schema generation and updates
- H2 console available at `/h2-console`

### 4. Error Handling
- Custom error controller for user-friendly error pages
- Exception handling in API endpoints
- Graceful degradation for service failures

## Build and Deployment

### Build Requirements
- **Build Tool**: Maven 3.19+
- **Java Runtime**: OpenJDK 17 (specified in pom.xml)
- **Port**: 8080 (default Spring Boot port)

### Build Commands
```bash
# Compile and package
mvn package -DskipTests

# Run application
java -jar target/voting-0.0.1-SNAPSHOT.jar
```

### Development Features
- Maven wrapper included (`mvnw`, `mvnw.cmd`)
- Hot reload support with Spring Boot DevTools
- H2 console for database inspection
- Comprehensive logging configuration

## Testing
- **VotingApplicationTests.java**: Contains test placeholders for:
  - Context loading verification
  - User authentication testing
  - Vote counting functionality
  - Database connection testing
  - Security configuration validation
  - Email notification testing

## Dependencies and Integration
- **Catalogue Service**: External dependency for product synchronization
- **H2 Database**: Internal database for vote storage
- **Spring Framework**: Core application framework
- **Thymeleaf**: Template rendering for web interface

## Security Considerations
- Basic error handling implemented
- Database credentials in configuration (development setup)
- Service-to-service communication via HTTP

This voting service is designed as a microservice component of a larger origami crafting platform, providing specialized voting functionality while integrating with other services in the ecosystem.