# Recommendation Service - Folder Contents Breakdown

## Overview
The `recommendation` folder contains a **Go-based microservice** that serves as the Daily Origami Recommendation Engine for the Craftista application. This service provides random origami suggestions and system status information through RESTful API endpoints.

## Architecture & Technology Stack
- **Language**: Go (Version 1.20+)
- **Framework**: Gin Web Framework
- **Purpose**: Backend service for origami recommendations
- **Port**: 8080
- **Module Name**: `recommendation`

## Folder Structure

```
recommendation/
├── .gitignore                 # Git ignore rules for Go projects
├── README.md                  # Build and deployment instructions  
├── config.json               # Application configuration
├── go.mod                    # Go module definition
├── go.sum                    # Go module checksums
├── main.go                   # Main application entry point
├── api/                      # API layer
│   └── api.go               # REST API endpoints implementation
├── data/                     # Data layer
│   └── data.go              # Origami data structures and mock data
├── static/                   # Static web assets
│   ├── css/
│   │   └── style.css        # CSS styling for web interface
│   └── images/
│       └── logo.png         # Craftista logo
├── templates/                # HTML templates
│   └── index.html           # Main web interface template
└── tests/                    # Test files
    └── api_test.go          # API endpoint tests
```

## Core Components

### 1. Main Application (`main.go`)
**Purpose**: Entry point and HTTP server configuration

**Key Functions**:
- `main()`: Initializes Gin router, sets up routes, starts server
- `renderHomePage()`: Handles root path requests, renders HTML template
- `getRecommendationStatus()`: Returns service health status
- `GetSystemInfo()`: Collects system information (hostname, IP, container status)
- `loadConfig()`: Loads application configuration from JSON

**Routes Configured**:
- `GET /` - Home page with service information
- `GET /api/origami-of-the-day` - Random origami recommendation
- `GET /api/recommendation-status` - Service health check
- Static file serving at `/static`

### 2. API Layer (`api/api.go`)
**Purpose**: REST API endpoint implementations

**Key Functions**:
- `GetOrigamiOfTheDay()`: Returns a randomly selected origami from the data layer
- `StartAPI()`: Alternative server startup (currently unused)

**API Response Format**:
```json
{
  "name": "Bird",
  "description": "Detailed description...",
  "image_url": "/static/images/origami/day1.png"
}
```

### 3. Data Layer (`data/data.go`)
**Purpose**: Data structures and mock data storage

**Data Structure**:
```go
type Origami struct {
    Name        string `json:"name"`
    Description string `json:"description"`
    ImageUrl    string `json:"image_url"`
}
```

**Available Origami**:
1. **Bird** - Symbol of freedom and poise
2. **Rabbit** - Embodiment of gentleness and charm
3. **Dragon** - Mystical creature with fiery charisma
4. **Elephant** - Majestic testament to gentle might
5. **Cat** - Playful spirit and sleek elegance
6. **Butterfly** - Symbol of transformation and beauty
7. **Windmill** - Symbol of sustainable energy and rustic charm

### 4. Web Interface (`templates/index.html`)
**Purpose**: User-facing web interface for the service

**Features**:
- Service description and purpose
- API endpoint links for testing
- System information display (hostname, IP, container status)
- Application version display
- Responsive layout with header, main content, and footer

**Template Variables**:
- `{{.Version}}` - Application version from config
- `{{.Year}}` - Current year for copyright
- `{{.SystemInfo.*}}` - System information (hostname, IP, container flags)

### 5. Static Assets (`static/`)
**CSS Styling** (`static/css/style.css`):
- Flexbox layout for responsive design
- Green-themed header and footer (#4CAF50)
- System info sidebar with light background
- Version circle indicator
- Logo positioning and sizing

**Images** (`static/images/`):
- `logo.png` - Craftista brand logo

### 6. Configuration (`config.json`)
```json
{
    "version": "1.0.0"
}
```
Simple version configuration for the application.

### 7. Go Module Configuration
**`go.mod`**: Defines module name and Go version requirements
- Module: `recommendation`
- Go Version: 1.20
- Primary Dependency: `github.com/gin-gonic/gin v1.9.1`

**`go.sum`**: Contains checksums for all dependencies ensuring integrity

### 8. Build Configuration (`.gitignore`)
Excludes common Go build artifacts:
- Binary executables (*.exe, *.dll, *.so, *.dylib)
- Test binaries (*.test)
- Coverage files (*.out)
- IDE files (.idea)
- OS files (.DS_Store)
- Built application binary (`app`)

## API Endpoints

### GET `/`
**Purpose**: Main web interface
**Response**: HTML page with service information and system details

### GET `/api/origami-of-the-day`
**Purpose**: Get a random origami recommendation
**Response**: JSON object with origami details
**Example**:
```json
{
  "name": "Dragon",
  "description": "Marvel at the mystical aura...",
  "image_url": "/static/images/origami/day3.png"
}
```

### GET `/api/recommendation-status`
**Purpose**: Service health check
**Response**: Service operational status
**Example**:
```json
{
  "status": "operational"
}
```

### Static Files `/static/*`
**Purpose**: Serve CSS, images, and other static assets
**Examples**:
- `/static/css/style.css` - Application styling
- `/static/images/logo.png` - Craftista logo

## Build & Deployment

### Build Process
```bash
# Install dependencies
go mod tidy

# Build application
go build -o app

# Run application
./app
```

### Runtime Requirements
- **Go Runtime**: Version 1.20 or higher
- **Port**: 8080 (configurable)
- **Dependencies**: Automatically managed via Go modules

### Testing
```bash
# Run all tests
go test ./...
```

## Integration with Craftista Ecosystem

This recommendation service integrates with the broader Craftista application:

1. **Frontend Service**: Calls `/api/origami-of-the-day` to display daily recommendations
2. **Health Monitoring**: Frontend checks `/api/recommendation-status` for service availability
3. **Static Assets**: Images referenced in responses must be accessible to frontend
4. **Configuration**: Version information displayed on frontend dashboard

## System Information Features

The service automatically detects and reports:
- **Hostname**: Server/container hostname
- **IP Address**: Primary network interface IP
- **Container Status**: Whether running in Docker container
- **Kubernetes Status**: Whether running in Kubernetes cluster (basic detection)

This information helps with debugging and monitoring in containerized environments.

## Future Enhancement Opportunities

1. **Database Integration**: Replace mock data with persistent storage
2. **User Preferences**: Add personalized recommendations
3. **Categories**: Filter origami by difficulty or theme
4. **Caching**: Add Redis/memory cache for frequently requested data
5. **Metrics**: Add Prometheus metrics for monitoring
6. **Authentication**: Add API key or JWT token validation
7. **Rate Limiting**: Protect endpoints from abuse
8. **Logging**: Structured logging for better observability