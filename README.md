# NovaCommerce - Microservices E-commerce Platform

A modern, cloud-native e-commerce platform built with Spring Boot 3.2.4 microservices architecture, featuring event-driven communication, comprehensive observability, and OAuth2 security.

## 🚀 Architecture Overview

NovaCommerce implements a distributed microservices architecture with the following core services:

- **API Gateway** (Port 9000) - Spring Cloud Gateway MVC with circuit breakers and load balancing
- **Product Service** (Port 8080) - Product catalog management with MongoDB
- **Order Service** (Port 8081) - Order processing and management with MySQL
- **Inventory Service** (Port 8082) - Stock management with MySQL and Flyway migrations
- **Notification Service** (Port 8083) - Event-driven email notifications
- **Frontend** - Angular 18 SPA with OIDC authentication

## 🛠 Tech Stack

### Backend
- **Java 21** with **Spring Boot 3.2.4**
- **Spring Cloud Gateway MVC** for API routing
- **Spring Security** with OAuth2/JWT
- **Apache Kafka** with **Avro** for event streaming
- **Spring Data JPA** and **Spring Data MongoDB**
- **Flyway** for database migrations
- **Resilience4j** for circuit breakers, retries, and timeouts
- **Micrometer** for observability and metrics

### Frontend
- **Angular 18** with TypeScript
- **TailwindCSS** for styling
- **angular-auth-oidc-client** for OIDC authentication
- **Standalone components** architecture

### Infrastructure
- **Keycloak** for identity and access management
- **Apache Kafka** with Schema Registry
- **MongoDB** for product data
- **MySQL** for transactional data
- **Docker Compose** for local development
- **Kubernetes** manifests for cloud deployment

### Observability Stack
- **Prometheus** for metrics collection
- **Grafana** for monitoring dashboards
- **Loki** for log aggregation
- **Tempo** for distributed tracing
- **Zipkin** for trace visualization

## 📋 Prerequisites

- **Java 21+**
- **Node.js 18+** and **npm**
- **Docker** and **Docker Compose**
- **Maven 3.6+**

## 🚦 Quick Start

### 1. Start Infrastructure Services
```bash
# Start all infrastructure components
docker-compose up -d
```

This will start:
- MongoDB (localhost:27017)
- MySQL (localhost:3306)
- Kafka + Zookeeper (localhost:9092)
- Schema Registry (localhost:8085)
- Keycloak (localhost:8181)
- Prometheus (localhost:9090)
- Grafana (localhost:3000)
- Loki (localhost:3100)
- Tempo (localhost:3110)
- Kafka UI (localhost:8086)

### 2. Start Backend Services
Start services in the following order:

```bash
# Product Service
cd product-service
mvn spring-boot:run

# Inventory Service  
cd inventory-service
mvn spring-boot:run

# Order Service
cd order-service
mvn spring-boot:run

# Notification Service
cd notification-service
mvn spring-boot:run

# API Gateway
cd api-gateway
mvn spring-boot:run
```

### 3. Start Frontend
```bash
cd frontend
npm install
npm start
```

Access the application at `http://localhost:4200`

## 🔐 Authentication & Security

### Keycloak Configuration
- **Admin Console**: http://localhost:8181/admin
- **Username/Password**: admin/admin
- **Realm**: spring-microservices-security-realm
- **Client ID**: angular-client

### Security Features
- JWT-based authentication with OAuth2/OIDC
- CORS configuration for cross-origin requests
- Circuit breaker patterns for resilience
- Resource server protection on all APIs

## 🔄 Event-Driven Architecture

### Kafka Topics
- **order-placed**: Order placement events

### Avro Schema
```json
{
  "type": "record",
  "name": "OrderPlacedEvent",
  "namespace": "com.techie.microservices.order.event",
  "fields": [
    { "name": "orderNumber", "type": "string" },
    { "name": "email", "type": "string" },
    { "name": "firstName", "type": "string" },
    { "name": "lastName", "type": "string" }
  ]
}
```

### Event Flow
1. Order Service publishes `OrderPlacedEvent` to Kafka
2. Notification Service consumes events and sends email notifications
3. All services are instrumented for distributed tracing

## 📊 API Documentation

### Swagger UI Endpoints
- **API Gateway**: http://localhost:9000/swagger-ui.html
- **Product Service**: http://localhost:8080/swagger-ui.html
- **Order Service**: http://localhost:8081/swagger-ui.html
- **Inventory Service**: http://localhost:8082/swagger-ui.html

### Key API Endpoints
```
# Product Management
GET    /api/product           # Get all products
POST   /api/product           # Create product

# Order Management  
POST   /api/order             # Place order

# Inventory Management
GET    /api/inventory         # Check stock
```

## 🗄 Database Schema

### Product Service (MongoDB)
```javascript
{
  "_id": "ObjectId",
  "name": "string",
  "description": "string", 
  "skuCode": "string",
  "price": "decimal"
}
```

### Order Service (MySQL)
```sql
CREATE TABLE t_orders (
    id bigint PRIMARY KEY AUTO_INCREMENT,
    order_number varchar(255),
    sku_code varchar(255),
    price decimal(19, 2),
    quantity int
);
```

### Inventory Service (MySQL)
```sql
CREATE TABLE t_inventory (
    id bigint PRIMARY KEY AUTO_INCREMENT,
    sku_code varchar(255),
    quantity int
);
```

## 🔄 Communication Patterns

### Synchronous Communication
- **Spring Cloud OpenFeign** for service-to-service calls
- **Circuit breakers** with Resilience4j
- **Retry mechanisms** with exponential backoff
- **Timeout handling** for resilience

### Asynchronous Communication
- **Apache Kafka** for event streaming
- **Avro schemas** for data serialization
- **Confluent Schema Registry** for schema evolution

## 📈 Observability

### Metrics & Monitoring
- **Micrometer** integration for all services
- **Prometheus** metrics collection
- **Grafana** dashboards for visualization
- **Custom metrics** for business KPIs

### Distributed Tracing
- **OpenTelemetry** instrumentation
- **Tempo** for trace storage
- **Zipkin** for trace visualization
- **Correlation IDs** across service boundaries

### Logging
- **Structured logging** with SLF4J
- **Loki** for log aggregation
- **Centralized log management**

## ☸️ Kubernetes Deployment

### Available Manifests
```
k8s/manifests/
├── applications/          # Microservice deployments
│   ├── api-gateway.yml
│   ├── product-service.yml
│   ├── order-service.yml
│   ├── inventory-service.yml
│   ├── notification-service.yml
│   └── frontend.yml
├── infra/                 # Infrastructure components
│   ├── mongodb.yml
│   ├── mysql.yml
│   ├── kafka.yml
│   ├── keycloak.yml
│   ├── prometheus.yml
│   ├── grafana.yml
│   └── loki.yml
└── infrastructure/        # Additional infrastructure
```

### Kind Cluster Setup
```bash
cd k8s/kind
./create-kind-cluster.sh   # Create local Kubernetes cluster
./kind-load.sh            # Load Docker images
./delete-kind-cluster.sh  # Cleanup cluster
```

## 🧪 Testing

### Integration Testing
- **TestContainers** for database testing
- **WireMock** for service mocking
- **RestAssured** for API testing
- **MySQL TestContainers** for order service tests

## 🔧 Configuration

### Application Properties
Each service includes comprehensive configuration:
- **Database connections** with connection pooling
- **Kafka producer/consumer** settings
- **Security configurations** for OAuth2
- **Observability settings** for metrics and tracing
- **Resilience4j** circuit breaker configuration

### Environment Variables
Key configuration through environment variables:
- `SPRING_PROFILES_ACTIVE` - Active Spring profiles
- `KAFKA_BOOTSTRAP_SERVERS` - Kafka cluster connection
- `SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI` - Keycloak issuer
- `SPRING_DATASOURCE_URL` - Database connections

## 🚀 Development Workflow

### Local Development
1. Start infrastructure with Docker Compose
2. Run services individually in IDE
3. Access frontend at http://localhost:4200
4. Monitor services through Grafana dashboards

### Database Migrations
- **Flyway** manages schema versions
- Migration scripts in `src/main/resources/db/migration/`
- Follows naming convention: `V{version}__{description}.sql`
- Automatic migration on application startup

### Code Generation
- **Avro code generation** from schemas during Maven build
- **OpenAPI/Swagger** documentation generation
- **Spring Boot configuration metadata** generation

## 📝 Business Logic

### Order Processing Flow
1. **Validate inventory** through synchronous call to Inventory Service
2. **Create order** record in Order Service database
3. **Publish event** to Kafka for downstream processing
4. **Send notification** via Notification Service email

### Product Management
- **CRUD operations** for product catalog
- **MongoDB** for flexible product schema
- **REST API** with OpenAPI documentation

### Inventory Management
- **Stock validation** for order processing
- **Database constraints** for data integrity
- **Flyway migrations** for schema evolution

## 🔍 Monitoring & Alerts

### Health Checks
- **Spring Boot Actuator** endpoints
- **Kubernetes readiness/liveness** probes
- **Circuit breaker** health indicators

### Performance Metrics
- **HTTP request** duration and counts
- **Database connection** pool metrics
- **Kafka producer/consumer** metrics
- **JVM** memory and garbage collection metrics

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit changes: `git commit -am 'Add new feature'`
4. Push to branch: `git push origin feature/your-feature`
5. Submit a Pull Request

## 📄 License

This project is licensed under the MIT License.


**Project Status**: ✅ Production Ready | 🔄 Active Development | 📊 Fully Monitored