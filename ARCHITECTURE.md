# No Secret Movies - Architecture Documentation

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      Frontend Layer                         │
│  (HTML5, CSS3, JavaScript - Responsive Web UI)             │
└────────────────────┬────────────────────────────────────────┘
                     │ HTTP/HTTPS
┌────────────────────▼────────────────────────────────────────┐
│                     API Gateway                              │
│  (RESTful Endpoints, Request Routing, Load Balancing)       │
└────────────────────┬────────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
┌───────▼───┐ ┌──────▼──┐ ┌──────▼──────┐
│  Auth     │ │ Movie   │ │  Streaming  │
│  Service  │ │ Service │ │  Service    │
└───────┬───┘ └──────┬──┘ └──────┬──────┘
        │           │            │
        └───────────┼────────────┘
                    │
        ┌───────────▼───────────┐
        │  Database Layer       │
        │  (SQLite)             │
        └───────────────────────┘
```

## Component Details

### 1. HTTP Server (server.cpp)
- **Responsibility**: Handle incoming HTTP requests and responses
- **Technology**: OpenSSL for HTTPS, Boost for networking
- **Features**:
  - RESTful API endpoints
  - SSL/TLS encryption
  - Multithread request handling
  - CORS support
  - Rate limiting

### 2. Authentication Service (auth.cpp)
- **Responsibility**: User authentication and authorization
- **Features**:
  - User registration
  - Login/logout
  - JWT token generation
  - Password hashing (SHA1 + salt)
  - Token validation
  - Refresh token mechanism

### 3. Database Layer (database.cpp)
- **Responsibility**: Data persistence and retrieval
- **Database**: SQLite for simplicity and portability
- **Tables**:
  - users (id, username, email, password_hash, created_at)
  - movies (id, title, description, genre, release_year, rating, file_path, poster_url, duration)
  - watch_history (id, user_id, movie_id, watched_at, duration_watched)
  - reviews (id, user_id, movie_id, rating, comment, created_at)

### 4. Movie Service (movie.cpp)
- **Responsibility**: Movie management operations
- **Features**:
  - CRUD operations on movies
  - Movie search and filtering
  - Genre categorization
  - Rating calculations
  - Metadata management

### 5. Streaming Service (streaming.cpp)
- **Responsibility**: Video streaming and playback
- **Features**:
  - HTTP range requests (for resume capability)
  - Adaptive bitrate streaming
  - Quality selection (480p, 720p, 1080p)
  - Playback progress tracking
  - Session management
  - Access control validation

### 6. User Service (user.cpp)
- **Responsibility**: User profile management
- **Features**:
  - Profile CRUD operations
  - Watchlist management
  - Watch history tracking
  - Preference storage
  - Account settings

## API Flow Diagram

```
User Request → API Gateway → Authentication Check → Route Handler
                                                          │
                    ┌─────────────────────────────────────┼──────────────────┐
                    │                                      │                  │
                    ▼                                      ▼                  ▼
            Database Query            Business Logic      File Serving
                    │                      │                 │
                    └─────────────────────────────────────────┘
                                    │
                                    ▼
                            Response Formatter
                                    │
                                    ▼
                            HTTP Response
                                    │
                                    ▼
                            Client Browser
```

## Authentication Flow

```
User Credentials
      │
      ▼
┌─────────────────────┐
│ Verify Credentials  │
│ (Check database)    │
└─────────┬───────────┘
          │
    ┌─────▼─────┐
    │ Valid?    │
    └─────┬─────┘
      Yes│    No
         │      └──► Return Error 401
         │
    ┌────▼────────────────┐
    │ Generate JWT Token  │
    │ + Refresh Token     │
    └────┬───────────────┘
         │
    ┌────▼──────────────┐
    │ Store Session     │
    │ Return Tokens     │
    └────┬──────────────┘
         │
    ┌────▼────────────────────┐
    │ Send to Client          │
    │ (localStorage)          │
    └─────────────────────────┘
```

## Streaming Architecture

```
Client Request for Movie
        │
        ▼
┌──────────────────────┐
│ Check User Access    │
│ & Permissions        │
└──────┬───────────────┘
       │
       ▼
┌──────────────────────────┐
│ Validate HTTP Range      │
│ Request Headers          │
└──────┬───────────────────┘
       │
       ▼
┌──────────────────────────────┐
│ Determine Quality Based on:  │
│ - Network Speed              │
│ - User Preference            │
│ - Device Capability          │
└──────┬─────────────────────┘
       │
       ▼
┌──────────────────────────┐
│ Load Transcoded Video    │
│ at Selected Quality      │
└──────┬───────────────────┘
       │
       ▼
┌──────────────────────────┐
│ Serve Video Segments     │
│ with Range Headers       │
└──────┬───────────────────┘
       │
       ▼
┌──────────────────────────┐
│ Track Playback Progress  │
│ Update Database          │
└──────────────────────────┘
```

## Database Relationships

```
┌─────────────┐                    ┌──────────────┐
│   Users     │                    │    Movies    │
├─────────────┤                    ├──────────────┤
│ id (PK)     │                    │ id (PK)      │
│ username    │◄──────many─────────┤ title        │
│ email       │                    │ genre        │
│ password    │                    │ rating       │
│ created_at  │                    │ file_path    │
└─────────────┘                    └──────────────┘
       │                                   ▲
       │                                   │
       │ many          ┌──────────────────┐│ many
       │               │                  ││
       └──────────────►│ Watch_History    ││
                       │                  ││
                       ├──────────────────┼┘
                       │ user_id (FK)     │
                       │ movie_id (FK)    │
                       │ watched_at       │
                       │ progress         │
                       └──────────────────┘
```

## Security Architecture

```
┌────────────────────────────────────────┐
│       HTTPS/TLS Layer                  │
│   (Encrypted Communication)            │
└────────────────────────────────────────┘
                  │
┌────────────────▼────────────────────┐
│   Request Validation Layer           │
│   (Input Sanitization, CORS)        │
└────────────────▼────────────────────┘
                  │
┌────────────────▼────────────────────┐
│   Authentication Layer               │
│   (JWT Token Verification)          │
└────────────────▼────────────────────┘
                  │
┌────────────────▼────────────────────┐
│   Authorization Layer                │
│   (Permission Checks)               │
└────────────────▼────────────────────┘
                  │
┌────────────────▼────────────────────┐
│   Business Logic Layer               │
│   (Encrypted Data Processing)       │
└────────────────▼────────────────────┘
                  │
┌────────────────▼────────────────────┐
│   Database Layer                     │
│   (Parameterized Queries)           │
└────────────────────────────────────┘
```

## Deployment Architecture

```
┌──────────────────────────────────────┐
│         Load Balancer                │
│    (Distributes Traffic)             │
└──────┬──────────────────────┬────────┘
       │                      │
┌──────▼──────┐        ┌──────▼──────┐
│  Instance 1 │        │  Instance 2 │
│  Port 8080  │        │  Port 8081  │
├─────────────┤        ├─────────────┤
│ App Server  │        │ App Server  │
│ (C++ App)   │        │ (C++ App)   │
└──────┬──────┘        └──────┬──────┘
       │                      │
       └──────────┬───────────┘
                  │
         ┌────────▼────────┐
         │  Database Pool  │
         │   (SQLite)      │
         └─────────────────┘
```

## Performance Considerations

1. **Caching Strategy**
   - In-memory movie catalog cache
   - HTTP caching headers
   - Browser cache for static files

2. **Database Optimization**
   - Indexed queries for fast search
   - Connection pooling
   - Query optimization

3. **Streaming Optimization**
   - Adaptive bitrate selection
   - Transcoding on-demand
   - Segment caching

4. **Concurrency**
   - Thread pool for request handling
   - Async I/O for file serving
   - Database connection pooling

## Future Enhancements

1. **Microservices Architecture**
   - Separate services for each domain
   - API Gateway pattern
   - Service discovery

2. **Caching Layer**
   - Redis for session management
   - CDN for static content
   - Video segment caching

3. **Message Queue**
   - RabbitMQ for async tasks
   - Background job processing
   - Event streaming

4. **Monitoring & Logging**
   - Prometheus metrics
   - ELK stack for logging
   - Distributed tracing (Jaeger)

---

**Document Version**: 1.0
**Last Updated**: 2024
