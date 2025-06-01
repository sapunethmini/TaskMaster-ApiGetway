# TaskMaster API Gateway 🚀

[![Java](https://img.shields.io/badge/Java-22-orange.svg)](https://openjdk.java.net/projects/jdk/22/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.3.3-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring Cloud Gateway](https://img.shields.io/badge/Spring%20Cloud-Gateway-blue.svg)](https://spring.io/projects/spring-cloud-gateway)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A high-performance API Gateway service built with Spring Cloud Gateway that serves as the unified entry point for the TaskMaster microservices ecosystem. This gateway provides intelligent routing, load balancing, security, and monitoring capabilities for seamless microservices communication.

## 🏗️ Architecture Overview

The TaskMaster API Gateway acts as a reverse proxy and service aggregator, routing incoming requests to appropriate microservices while providing cross-cutting concerns such as authentication, logging, and CORS handling.

```
Client Applications → API Gateway → Microservices
                        ↓
                   Service Discovery
                      (Eureka)
```

## ✨ Key Features

- **🔍 Service Discovery**: Seamless integration with Netflix Eureka for dynamic service discovery
- **🔀 Intelligent Routing**: Dynamic routing configuration for multiple microservices
- **⚖️ Load Balancing**: Built-in load balancing across service instances  
- **🌐 CORS Support**: Comprehensive cross-origin resource sharing configuration
- **💓 Health Monitoring**: Advanced health check endpoints for all integrated services
- **🔧 Request Filtering**: Extensible request/response filtering capabilities
- **📊 Observability**: Built-in logging and monitoring support

## 🎯 Microservices Integration

The gateway provides routing for the following microservices:

| Service | Route Pattern | Description |
|---------|---------------|-------------|
| **Backend Service** | `/api/backend/**` | Core backend functionality |
| **Task Management** | `/api/tasks/**` | Task CRUD operations and workflow |
| **Notification Service** | `/api/notifications/**` | Real-time notifications and alerts |
| **User Service** | `/api/users/**` | User management and profiles |
| **Authentication Service** | `/api/auth/**` | Authentication and authorization |

## 🛠️ Technology Stack

- **Runtime**: Java 22
- **Framework**: Spring Boot 3.3.3
- **Gateway**: Spring Cloud Gateway
- **Service Discovery**: Spring Cloud Netflix Eureka Client
- **Build Tool**: Maven
- **Containerization**: Docker

## 📋 Prerequisites

Before running the API Gateway, ensure you have:

- ☑️ **Java 22** or higher installed
- ☑️ **Maven 3.6+** for dependency management
- ☑️ **Docker** (optional, for containerized deployment)
- ☑️ **Eureka Server** running on port 8761

## ⚙️ Configuration

The service configuration is managed through `application.yml`:

### Core Settings
- **Server Port**: `8080`
- **Eureka Server**: `http://eureka-server:8761/eureka`

### CORS Configuration
```yaml
cors:
  allowed-origins: 
    - http://localhost:3000  # React development
    - http://localhost:4200  # Angular development
  allowed-methods: [GET, POST, PUT, DELETE, OPTIONS]
  allowed-headers: "*"
```

### Service Routes
All microservices are automatically discovered and routed through the `/api/**` pattern.

## 🚀 Getting Started

### Local Development

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd taskmaster-api-gateway
   ```

2. **Install dependencies**
   ```bash
   mvn clean install
   ```

3. **Start the application**
   ```bash
   mvn spring-boot:run
   ```

4. **Verify the service**
   ```bash
   curl http://localhost:8080/actuator/health
   ```

### 🐳 Docker Deployment

1. **Build the Docker image**
   ```bash
   docker build -t taskmaster/api-gateway:latest .
   ```

2. **Run the container**
   ```bash
   docker run -p 8080:8080 \
     -e EUREKA_SERVER_URL=http://eureka-server:8762/eureka \
     taskmaster/api-gateway:latest
   ```

3. **Docker Compose (Recommended)**
   ```yaml
   version: '3.8'
   services:
     api-gateway:
       image: taskmaster/api-gateway:latest
       ports:
         - "8080:8080"
       environment:
         - EUREKA_SERVER_URL=http://eureka-server:8761/eureka
       depends_on:
         - eureka-server
   ```

## 🔍 Health Monitoring

The API Gateway provides comprehensive health monitoring endpoints:

| Endpoint | Purpose | Example |
|----------|---------|---------|
| `/actuator/health` | Gateway health status | `GET /actuator/health` |
| `/health/backend/**` | Backend service health | `GET /health/backend/status` |
| `/health/tasks/**` | Task service health | `GET /health/tasks/status` |
| `/health/notifications/**` | Notification service health | `GET /health/notifications/status` |

### Health Check Response
```json
{
  "status": "UP",
  "components": {
    "discoveryComposite": {
      "status": "UP"
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

## 📝 Logging Configuration

The service uses structured logging with the following configuration:

```yaml
logging:
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
  level:
    org.springframework.cloud.gateway: DEBUG
    com.netflix.eureka: DEBUG
    com.netflix.discovery: DEBUG
```

## 🔧 Development

### Adding New Route
To add a new microservice route, update the `application.yml`:

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: new-service
          uri: lb://NEW-SERVICE
          predicates:
            - Path=/api/new-service/**
```

### Custom Filters
Create custom filters by extending `GlobalFilter`:

```java
@Component
public class CustomGatewayFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, 
                            GatewayFilterChain chain) {
        // Custom logic here
        return chain.filter(exchange);
    }
}
```

## 🧪 Testing

Run the test suite:
```bash
mvn test
```

Integration tests:
```bash
mvn verify
```

## 📊 Performance Tuning

For production deployments, consider these optimizations:

```yaml
server:
  netty:
    connection-timeout: 2s
    idle-timeout: 60s
  
spring:
  cloud:
    gateway:
      httpclient:
        connect-timeout: 1000
        response-timeout: 5s
```

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Code Standards
- Follow Google Java Style Guide
- Maintain test coverage above 80%
- Update documentation for new features

## 📚 API Documentation

Once running, access the API documentation at:
- **Swagger UI**: `http://localhost:8080/swagger-ui.html`
- **OpenAPI Spec**: `http://localhost:8080/v3/api-docs`

## 🔒 Security

This gateway implements several security measures:
- CORS protection
- Request rate limiting (configurable)
- Header sanitization
- Service-to-service authentication ready

## 📈 Monitoring & Observability

Integration with monitoring tools:
- **Prometheus**: Metrics endpoint at `/actuator/prometheus`
- **Jaeger**: Distributed tracing support
- **ELK Stack**: Structured logging compatible

## 🚨 Troubleshooting

### Common Issues

**Service Discovery Issues**
```bash
# Check Eureka server connectivity
curl http://localhost:8762/eureka/apps
```

**Routing Problems**
```bash
# Enable debug logging
export LOGGING_LEVEL_ORG_SPRINGFRAMEWORK_CLOUD_GATEWAY=DEBUG
```

**CORS Errors**
Verify allowed origins in `application.yml` match your client application URLs.

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Spring Cloud Gateway team for the excellent framework
- Netflix OSS for Eureka service discovery
- TaskMaster development team

---

**Made with ❤️ by the TaskMaster Team**

For support, please open an issue or contact the development team.
