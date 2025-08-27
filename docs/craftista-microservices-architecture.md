# Craftista Microservices Architecture

## Architecture Overview

This Mermaid diagram shows the complete microservices architecture of the Craftista application, including all services, their communication patterns, databases, and request flows.

```mermaid
graph TB
    %% User/Client
    User[👤 User/Browser] 

    %% Frontend Service
    Frontend[🌐 Frontend Service<br/>Node.js/Express<br/>Port: 3000]
    
    %% Backend Microservices
    Catalogue[📚 Catalogue Service<br/>Python/Flask<br/>Port: 5000]
    Recommendation[🎯 Recommendation Service<br/>Go/Gin<br/>Port: 8080]
    Voting[🗳️ Voting Service<br/>Java/Spring Boot<br/>Port: 8080]
    
    %% Databases
    CatalogueDB[(🗄️ Catalogue DB<br/>PostgreSQL<br/>or JSON Files)]
    VotingDB[(🗄️ Voting DB<br/>H2 In-Memory<br/>or PostgreSQL)]
    
    %% User interactions
    User -->|HTTP Requests| Frontend
    Frontend -->|Serves Web UI| User
    
    %% Frontend to Backend Communications
    Frontend -->|GET /api/products<br/>Fetch origami catalog| Catalogue
    Frontend -->|GET /daily-origami<br/>Get recommendation| Recommendation
    Frontend -->|POST /api/origamis/:id/vote<br/>Submit votes| Voting
    Frontend -->|GET /api/origamis/:id/votes<br/>Get vote counts| Voting
    Frontend -->|GET /recommendation-status<br/>Health check| Recommendation
    Frontend -->|GET /votingservice-status<br/>Health check| Voting
    Frontend -->|GET /api/service-status<br/>Health check| Catalogue
    
    %% Backend Responses
    Catalogue -->|JSON: Product/Origami data| Frontend
    Recommendation -->|JSON: Daily origami| Frontend
    Voting -->|JSON: Vote data| Frontend
    Recommendation -->|JSON: Service status| Frontend
    Voting -->|JSON: Service status| Frontend
    Catalogue -->|JSON: Service status| Frontend
    
    %% Service-to-Service Communication
    Voting -->|Scheduled sync (every 60s)<br/>GET /api/products<br/>Fetch origami list| Catalogue
    Catalogue -->|JSON: Origami data<br/>for synchronization| Voting
    
    %% Database Connections
    Catalogue -->|CRUD Operations<br/>Store/Retrieve products| CatalogueDB
    Voting -->|CRUD Operations<br/>Store votes & origami| VotingDB
    
    %% API Endpoints Detail
    subgraph "API Endpoints"
        direction TB
        FrontendAPIs[Frontend APIs:<br/>• GET / - Main UI<br/>• GET /api/products<br/>• GET /daily-origami<br/>• POST /api/origamis/:id/vote<br/>• GET /api/origamis/:id/votes<br/>• GET /*-status endpoints]
        
        CatalogueAPIs[Catalogue APIs:<br/>• GET /api/products<br/>• GET /api/products/:id<br/>• GET / - Service UI]
        
        RecommendationAPIs[Recommendation APIs:<br/>• GET /api/origami-of-the-day<br/>• GET /api/recommendation-status<br/>• GET / - Service UI]
        
        VotingAPIs[Voting APIs:<br/>• GET /api/origamis<br/>• GET /api/origamis/:id<br/>• POST /api/origamis/:id/vote<br/>• GET /api/origamis/:id/votes<br/>• GET / - Service UI]
    end
    
    %% Configuration
    subgraph "Configuration"
        direction TB
        FrontendConfig[Frontend Config:<br/>• productsApiBaseUri: http://catalogue:5000<br/>• recommendationBaseUri: http://recco:8080<br/>• votingBaseUri: http://voting:8080]
        
        VotingConfig[Voting Config:<br/>• catalogue.service-url:<br/>  http://catalogue:5000/api/products<br/>• Database: H2 in-memory]
    end
    
    %% Styling
    classDef frontend fill:#e1f5fe,stroke:#01579b,stroke-width:3px
    classDef backend fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef database fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef user fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    
    class User user
    class Frontend frontend
    class Catalogue,Recommendation,Voting backend
    class CatalogueDB,VotingDB database
```

## Key Architecture Patterns

### 1. API Gateway Pattern
The Frontend service acts as an API Gateway that:
- Routes requests to appropriate backend services
- Aggregates responses from multiple services
- Provides a single entry point for clients
- Handles service discovery through configuration

### 2. Service-to-Service Communication
- **Synchronous**: HTTP REST APIs for real-time requests
- **Asynchronous**: Scheduled synchronization (Voting ↔ Catalogue)
- **Service Discovery**: Static configuration using service names

### 3. Database Per Service
- Each microservice has its own database
- Catalogue: PostgreSQL or JSON files
- Voting: H2 in-memory or PostgreSQL
- No shared databases between services

### 4. Health Monitoring
- Each service exposes health check endpoints
- Frontend aggregates service status
- Real-time service availability dashboard

## Communication Flow Examples

### 1. User Viewing Origami Catalog
```
User → Frontend → Catalogue Service → Database → Response Chain
```

### 2. User Voting for Origami
```
User → Frontend → Voting Service → Voting DB
                ↓
         Vote confirmation returned
```

### 3. Daily Recommendation
```
User → Frontend → Recommendation Service → Static Data → Random Selection
```

### 4. Data Synchronization
```
Voting Service → (Every 60s) → Catalogue Service → Sync origami list
```

## Technology Stack Summary

| Service | Language | Framework | Port | Database |
|---------|----------|-----------|------|----------|
| Frontend | Node.js | Express.js | 3000 | None |
| Catalogue | Python | Flask | 5000 | PostgreSQL/JSON |
| Recommendation | Go | Gin | 8080 | None (Static) |
| Voting | Java | Spring Boot | 8080 | H2/PostgreSQL |

## Service Responsibilities

- **Frontend**: UI rendering, request routing, service orchestration
- **Catalogue**: Product/origami data management, CRUD operations
- **Recommendation**: Daily origami selection, recommendation logic
- **Voting**: Vote tracking, origami popularity metrics, data synchronization