# CineRate Microservices Architecture

This document provides an overview of the CineRate microservices architecture, illustrating the system components, their relationships, and data flow.

## Architecture Diagram

```mermaid
graph TB
    %% Client
    Client[Frontend Client]
    
    %% API Gateway
    Gateway[API Gateway]
    
    %% Microservices
    UserService[User Service]
    ReviewService[Review Service]
    WatchlistService[Watchlist Service]
    
    %% Databases
    PostgreSQL[(PostgreSQL)]
    MongoDB[(MongoDB)]
    Redis[(Redis)]
    
    %% Monitoring & Logging
    Prometheus[Prometheus]
    Grafana[Grafana]
    ELK[ELK Stack]
    Filebeat[Filebeat]
    
    %% Client to Gateway
    Client -->|HTTP Requests| Gateway
    
    %% Gateway to Services
    Gateway -->|/api/user/*| UserService
    Gateway -->|/api/review/*| ReviewService
    Gateway -->|/api/watchlist/*| WatchlistService
    
    %% Services to Databases
    UserService -->|CRUD Operations| PostgreSQL
    ReviewService -->|CRUD Operations| MongoDB
    WatchlistService -->|CRUD Operations| MongoDB
    
    %% Redis Caching
    UserService -.->|Cache| Redis
    ReviewService -.->|Cache| Redis
    WatchlistService -.->|Cache| Redis
    Gateway -.->|Cache Responses| Redis
    
    %% Monitoring & Logging
    UserService -.->|Metrics| Prometheus
    ReviewService -.->|Metrics| Prometheus
    WatchlistService -.->|Metrics| Prometheus
    Gateway -.->|Metrics| Prometheus
    
    Prometheus -->|Visualization| Grafana
    
    UserService -.->|Logs| Filebeat
    ReviewService -.->|Logs| Filebeat
    WatchlistService -.->|Logs| Filebeat
    Gateway -.->|Logs| Filebeat
    
    Filebeat --> ELK
    
    %% Kubernetes Components
    subgraph "Kubernetes Cluster"
        Gateway
        UserService
        ReviewService
        WatchlistService
        PostgreSQL
        MongoDB
        Redis
        Prometheus
        Grafana
        ELK
        Filebeat
    end
    
    %% Service Details
    subgraph "User Service"
        UserAuth[Authentication]
        UserProfiles[User Profiles]
        UserPreferences[User Preferences]
    end
    
    subgraph "Review Service"
        MovieReviews[Movie Reviews]
        ReviewRatings[Ratings]
        ReviewComments[Comments]
    end
    
    subgraph "Watchlist Service"
        AddToWatchlist[Add Movies]
        RemoveFromWatchlist[Remove Movies]
        GetWatchlist[Get User Watchlist]
    end
    
    %% Circuit Breaker Pattern
    Gateway -->|Circuit Breaker| UserService
    Gateway -->|Circuit Breaker| ReviewService
    Gateway -->|Circuit Breaker| WatchlistService
    
    %% Persistent Storage
    subgraph "Persistent Storage"
        PostgreSQLPVC[PostgreSQL PVC]
        MongoDB-PVC[MongoDB PVC]
    end
    
    PostgreSQL --- PostgreSQLPVC
    MongoDB --- MongoDB-PVC

    %% Add legend
    classDef service fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff
    classDef database fill:#f9a825,stroke:#fff,stroke-width:1px,color:#fff
    classDef monitoring fill:#00897b,stroke:#fff,stroke-width:1px,color:#fff
    classDef client fill:#e53935,stroke:#fff,stroke-width:1px,color:#fff
    classDef storage fill:#8e24aa,stroke:#fff,stroke-width:1px,color:#fff
    
    class UserService,ReviewService,WatchlistService,Gateway service
    class PostgreSQL,MongoDB,Redis database
    class Prometheus,Grafana,ELK,Filebeat monitoring
    class Client client
    class PostgreSQLPVC,MongoDB-PVC storage
```

## Core Components

### API Gateway
- Entry point for all client requests
- Routes requests to appropriate microservices
- Implements circuit breaker pattern for fault tolerance
- Handles caching of responses using Redis

### User Service
- Manages user authentication and profiles
- Uses PostgreSQL for persistent data storage
- Implements circuit breaker for external service calls
- Exposes metrics for monitoring

### Review Service
- Handles movie reviews and ratings
- Uses MongoDB for document storage
- Implements caching for frequently accessed reviews
- Exposes health endpoints and metrics

### Watchlist Service
- Manages user movie watchlists
- Uses MongoDB with persistent storage (StatefulSet)
- Implements proper cache invalidation
- Provides CRUD operations for watchlist items

## Infrastructure Components

### Databases
- PostgreSQL: User data (with persistent volume)
- MongoDB: Reviews and watchlists (with persistent volume)
- Redis: Caching layer for all services

### Monitoring & Logging
- Prometheus: Metrics collection
- Grafana: Metrics visualization and dashboards
- ELK Stack: Centralized logging
- Filebeat: Log shipping agent

### Kubernetes Resources
- Deployments for stateless services
- StatefulSets for databases
- Services for network access
- Secrets for sensitive configuration
- PersistentVolumeClaims for data persistence

## Key Design Patterns

### Circuit Breaker Pattern
- Prevents cascading failures
- Provides fallback responses when services are down
- Automatically recovers when services become available

### Caching Strategy
- Redis used for caching frequently accessed data
- Cache invalidation on write operations
- TTL-based expiration for different data types

### Health Monitoring
- All services expose health endpoints
- Prometheus scrapes metrics from all services
- Grafana dashboards visualize service health
