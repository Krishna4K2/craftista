# Craftista Microservices Communication Diagram

This diagram shows how requests flow between the microservices and how they are connected to each other.

```mermaid
graph TD
    %% External
    User[👤 User Browser]
    
    %% Microservices
    Frontend[🌐 Frontend<br/>Node.js:3000]
    Catalogue[📚 Catalogue<br/>Python:5000]
    Recommendation[🎯 Recommendation<br/>Go:8080]
    Voting[🗳️ Voting<br/>Java:8080]
    
    %% Databases
    CatalogueDB[(Catalogue DB)]
    VotingDB[(Voting DB)]
    
    %% User to Frontend
    User -.->|1. HTTP Requests| Frontend
    Frontend -.->|2. Web Pages & Data| User
    
    %% Frontend to Backend Services
    Frontend -->|3a. GET /api/products<br/>Fetch origami catalog| Catalogue
    Frontend -->|3b. GET /daily-origami<br/>Daily recommendation| Recommendation
    Frontend -->|3c. POST /vote, GET /votes<br/>Voting operations| Voting
    Frontend -->|3d. Health checks<br/>Service status| Catalogue
    Frontend -->|3e. Health checks<br/>Service status| Recommendation
    Frontend -->|3f. Health checks<br/>Service status| Voting
    
    %% Backend to Frontend Responses
    Catalogue -->|4a. Product data| Frontend
    Recommendation -->|4b. Daily origami| Frontend
    Voting -->|4c. Vote results| Frontend
    
    %% Service-to-Service Communication
    Voting -->|5. Periodic sync (60s)<br/>GET /api/products<br/>Fetch latest origami| Catalogue
    Catalogue -->|6. Origami data<br/>for synchronization| Voting
    
    %% Database Operations
    Catalogue <-->|7a. CRUD operations| CatalogueDB
    Voting <-->|7b. CRUD operations| VotingDB
    
    %% Request Flow Details
    subgraph "Request Flow Examples"
        Flow1[1. View Catalog:<br/>User → Frontend → Catalogue → DB]
        Flow2[2. Vote for Origami:<br/>User → Frontend → Voting → DB]
        Flow3[3. Daily Recommendation:<br/>User → Frontend → Recommendation]
        Flow4[4. Data Sync:<br/>Voting → Catalogue (every 60s)]
    end
    
    %% Service Configuration
    subgraph "Service Endpoints"
        FE[Frontend: localhost:3000<br/>• Routes all user requests<br/>• Aggregates backend data]
        CAT[Catalogue: catalogue:5000<br/>• /api/products<br/>• /api/products/:id]
        REC[Recommendation: recco:8080<br/>• /api/origami-of-the-day<br/>• /api/recommendation-status]
        VOT[Voting: voting:8080<br/>• /api/origamis<br/>• /api/origamis/:id/vote<br/>• /api/origamis/:id/votes]
    end
    
    %% Styling
    classDef frontend fill:#e3f2fd,stroke:#1976d2,stroke-width:3px
    classDef backend fill:#f1f8e9,stroke:#388e3c,stroke-width:2px
    classDef database fill:#fff8e1,stroke:#f57c00,stroke-width:2px
    classDef user fill:#fce4ec,stroke:#c2185b,stroke-width:3px
    
    class User user
    class Frontend frontend
    class Catalogue,Recommendation,Voting backend
    class CatalogueDB,VotingDB database
```

## Communication Patterns

### 1. **Frontend as API Gateway**
- All user requests go through the Frontend service first
- Frontend routes requests to appropriate backend services
- Responses are aggregated and sent back to users

### 2. **Synchronous API Calls**
- Frontend → Catalogue: Product data retrieval
- Frontend → Recommendation: Daily origami selection
- Frontend → Voting: Vote submission and retrieval
- Frontend → All: Health status checks

### 3. **Asynchronous Data Synchronization**
- Voting service periodically (every 60 seconds) fetches origami data from Catalogue
- Ensures Voting service has latest product information for vote tracking

### 4. **Database Isolation**
- Each service manages its own database
- No direct database sharing between services
- Data consistency through API calls and synchronization

## Request Flow Examples

### Viewing Origami Catalog
```
User → Frontend → Catalogue Service → Database
     ← ← Product Data ← ←
```

### Voting for an Origami
```
User → Frontend → Voting Service → Voting Database
     ← Vote Confirmation ← ←
```

### Getting Daily Recommendation
```
User → Frontend → Recommendation Service
     ← Daily Origami ← 
```

### Background Data Sync
```
Voting Service → Catalogue Service (every 60s)
               ← Latest Origami List ←
```