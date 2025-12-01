# University Management System

## Overview

This project implements a comprehensive university management platform leveraging microservices architecture, built on Spring Boot, Spring Cloud, and containerized with Docker.

## Core Components

The system consists of the following integrated parts:

| Component | Purpose |
|-----------|---------|
| **Eureka Service Registry** | Enables dynamic service discovery across the platform |
| **API Gateway** | Provides unified routing and access point for all microservices |
| **Student Service** | Manages student profiles (PostgreSQL) |
| **Professor Service** | Handles faculty information (MySQL) |
| **Course Service** | Maintains course catalog (MongoDB) |
| **Grading Service** | Processes grades and academic records (H2) |
| **Frontend Application** | Responsive web interface for system interaction |

## Design Patterns & Best Practices

This implementation showcases several enterprise architecture patterns:

- **Service Discovery** - Eureka-based automatic service registration
- **Gateway Pattern** - Centralized request routing via Spring Cloud Gateway
- **Circuit Breaking** - Resilience4j fault tolerance in Student Service
- **Polyglot Persistence** - Multiple databases optimized per service needs
- **Synchronous Communication** - REST-based inter-service interactions

## Getting Started

### System Requirements

Before deployment, ensure you have:

- Docker Desktop with active daemon
- Minimum 8GB RAM allocation
- Available ports: 80, 8080-8084, 8761, 3306, 5432, 27017

### Launch Instructions

Deploy the complete ecosystem with a single command:

```powershell
docker-compose up --build -d
```

This orchestrates the following startup sequence:
1. Compilation and containerization of all 6 services
2. Database initialization (PostgreSQL, MySQL, MongoDB)
3. Eureka Service Registry startup
4. API Gateway deployment
5. Launch of all 4 core microservices
6. Frontend web server initialization

### Access Points

| Service | URL |
|---------|-----|
| **Web Interface** | http://localhost |
| **API Gateway** | http://localhost:8080 |
| **Service Registry** | http://localhost:8761 |
| **Student Service** | http://localhost:8081 |
| **Professor Service** | http://localhost:8082 |
| **Course Service** | http://localhost:8083 |
| **Grading Service** | http://localhost:8084 |

## Service Specifications

### Student Management Service
| Property | Details |
|----------|---------|
| Port | 8081 |
| Database | PostgreSQL (5432) |
| **Endpoints** | |
| List Students | `GET /api/students` |
| Retrieve Student | `GET /api/students/{id}` |
| Create Student | `POST /api/students` |
| Update Student | `PUT /api/students/{id}` |
| Remove Student | `DELETE /api/students/{id}` |
| Fetch Grades | `GET /api/students/{id}/grades` *(Circuit Breaker enabled)* |

### Professor Management Service
| Property | Details |
|----------|---------|
| Port | 8082 |
| Database | MySQL (3306) |
| **Endpoints** | |
| List Professors | `GET /api/professors` |
| Retrieve Professor | `GET /api/professors/{id}` |
| Create Professor | `POST /api/professors` |
| Update Professor | `PUT /api/professors/{id}` |
| Remove Professor | `DELETE /api/professors/{id}` |
| View Assignments | `GET /api/professors/{id}/assignments` |
| Assign Course | `POST /api/professors/assignments` |

### Course Management Service
| Property | Details |
|----------|---------|
| Port | 8083 |
| Database | MongoDB (27017) |
| **Endpoints** | |
| List Courses | `GET /api/courses` |
| Retrieve Course | `GET /api/courses/{id}` |
| Create Course | `POST /api/courses` |
| Update Course | `PUT /api/courses/{id}` |
| Remove Course | `DELETE /api/courses/{id}` |

### Grading Service
| Property | Details |
|----------|---------|
| Port | 8084 |
| Database | H2 (In-Memory) |
| **Endpoints** | |
| List Grades | `GET /api/grades` |
| Retrieve Grade | `GET /api/grades/{id}` |
| Query by Student | `GET /api/grades/student/{studentId}` |
| Query by Course | `GET /api/grades/course/{courseId}` |
| Create Grade | `POST /api/grades` |
| Update Grade | `PUT /api/grades/{id}` |
| Remove Grade | `DELETE /api/grades/{id}` |

## Circuit Breaker Demonstration

The system implements fault tolerance using Resilience4j within the Student Service for Grading Service calls.

### Configuration Parameters
```
Sliding Window: 10 calls
Failure Threshold: 50%
Recovery Timeout: 10 seconds
Half-Open State Calls: 3
```

### Validation Workflow

1. Navigate to http://localhost in your browser
2. Generate test data:
   - Create a student profile (Students section)
   - Assign grades to that student (Grades section)
3. Access the Circuit Breaker test panel
4. Click "Test Circuit Breaker" with the service operational
5. Simulate failure by stopping the Grading Service:
   ```powershell
   docker stop grading-service
   ```
6. Click "Test Circuit Breaker" again—fallback behavior activates
7. Resume the service:
   ```powershell
   docker start grading-service
   ```

## Monitoring & Observability

### Service Registry Dashboard
Point your browser to http://localhost:8761 for visibility into:
- All active and registered microservice instances
- Real-time health status of each service
- Service heartbeat information and availability metrics

### Health Indicators
Individual service health can be queried via actuator endpoints:
- `http://localhost:8081/actuator/health` - Student Service
- `http://localhost:8081/actuator/circuitbreakers` - Circuit Breaker Status
- Apply the same pattern to other services using their respective ports (8082-8084)

## Project Architecture

### Directory Organization
```
uni-service/
├── eureka-server/          # Dynamic service registry
├── api-gateway/            # Request routing layer
├── student-service/        # Student domain logic
├── professor-service/      # Faculty management
├── course-service/         # Academic offerings
├── grading-service/        # Assessment tracking
├── frontend/               # User-facing interface
└── docker-compose.yml      # Infrastructure as code
```

### Technology Stack

| Layer | Technologies |
|-------|---------------|
| **Framework** | Spring Boot 3.2.0 |
| **Cloud Services** | Spring Cloud Netflix Eureka, Spring Cloud Gateway |
| **Resilience** | Resilience4j |
| **Persistence** | PostgreSQL, MySQL, MongoDB, H2 |
| **Interface** | HTML5, CSS3, Vanilla JavaScript |
| **Container Orchestration** | Docker, Docker Compose |

### Building Individual Components

Compile a specific microservice:

```powershell
cd student-service
mvn clean package
```

Create its container image:

```powershell
docker build -t student-service .
```

## API Usage Examples

All requests route through the API Gateway at `http://localhost:8080`.

### Student Creation

```http
POST http://localhost:8080/api/students
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@university.edu",
  "phone": "555-1234",
  "address": "123 Main St"
}
```

### Grade Assignment

```http
POST http://localhost:8080/api/grades
Content-Type: application/json

{
  "studentId": 1,
  "courseId": 1,
  "gradeValue": 85.5
}
```

## Maintenance & Cleanup

### Stopping the System

Halt all running containers:

```powershell
docker-compose down
```

### Full Resource Cleanup

Remove all containers, associated networks, and data volumes:

```powershell
docker-compose down -v
```

## Benefits & Tradeoffs

### Key Advantages

- **Horizontal Scalability** - Scale any service independently based on demand
- **Database Flexibility** - Each service selects the optimal database technology
- **Fault Isolation** - Failures remain compartmentalized with circuit breaker protection
- **Team Autonomy** - Services developed and deployed independently
- **Dynamic Registration** - Automatic service discovery eliminates hardcoded endpoints

### Design Considerations

- **Eventual Consistency** - No global transactions across service boundaries
- **Network Overhead** - Communication latency between service calls
- **Consistency Challenges** - Distributed data state requires careful management
- **Operational Complexity** - More components to monitor and manage
- **Observability Requirements** - Distributed tracing essential for production debugging

## Implemented Microservice Patterns

| Pattern | Implementation |
|---------|-----------------|
| **Service Registry** | Netflix Eureka for dynamic discovery |
| **API Gateway** | Spring Cloud Gateway centralized routing |
| **Resilience** | Resilience4j fault tolerance mechanisms |
| **Polyglot Storage** | Multiple database engines per service |
| **Config Externalization** | Environment-driven configuration |
| **Health Monitoring** | Spring Boot Actuator endpoints |