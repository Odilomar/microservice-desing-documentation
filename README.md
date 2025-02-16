# Microservice Design Documentation

## 1. Overview

This document describes the architecture and technical decisions for the proposed microservice, ensuring scalability, maintainability, and efficiency in handling large data volumes.

## 2. Architecture

### 2.1. General Architecture

The microservice follows **Clean Architecture**, ensuring modularity and separation of concerns. It is divided into the following layers:

- **API Layer**: Handles HTTP requests and validation.
- **Service Layer**: Contains business logic.
- **Database Layer**: Responsible for data persistence.

### 2.2. Technologies Used

- **Framework:** NestJS;
    - **Reasons**: robustness and modularity
- **Database:** PostgreSQL;
    - **Reasons**: transaction support, scalability, and integrity
- **Cache:** Redis;
    - **Reasons**: performance improvement for frequent queries
- **Containerization:** Docker and Kubernetes for orchestration;
- **CI/CD:** GitHub Actions for automation;

### 2.3. Structural microservice
![Captura de tela de 2025-02-16 18-10-49](https://github.com/user-attachments/assets/0ec9020a-e829-499a-bd97-2c273abf033e)

```
## user microservice - example
domain
 user.domain.ts
infra
 config
  db.config.ts
 repositories
  user.repository.ts
use-cases
 create-user
  create-user.use-case.ts
  create-user.use-case.spec.ts
controllers
 create-user
  create-user.dto.ts
  create-user.controller.ts
app.module.ts
main.ts
package.json
```

## 3. Endpoints

### 3.1. POST /data

**Description:** Receives and validates JSON data before persisting it to the database.

**Request Body:**

```json
{
  "id": "uuid",
  "name": "string",
  "value": "number"
}
```

**Validations:**

- `id` must be a UUID.
- `name` must be a non-empty string.
- `value` must be a valid number.

### 3.2. GET /data

**Description:** Returns stored data from the database.

**Optional Parameters:**

- `limit`: Number of records to return.
- `offset`: Pagination.

**Response:**

```json
{
   "data":[
      {
         "id":"uuid",
         "name":"string",
         "value":"number"
      }
   ],
   "meta":{
      "limit": "number",
      "total": "number",
      "page": "number"
   }
}
```

## 4. Containerization

- **Dockerfile:** Defines the microservice image.
- **Docker Compose:** Optional configuration for the local environment.

```docker
### DOCKERFILE MULTI-STAGE EXAMPLE ###

### STAGE 01: Install packages
FROM node:lts-alpine AS builder 
WORKDIR /app
RUN apk add --no-cache git make python3 g++
COPY --chown=node:node ./package*.json . 
RUN npm install ci 
COPY . .

### STAGE 02: Build app
FROM alpine AS runner 
RUN apk add --update --no-cache nodejs
USER node
WORKDIR /app
COPY --chown=node:node --from=builder /app/ . 

CMD ["node","app.js"]
```

## 5. Kubernetes Deployment

- **Deployment with Horizontal Pod Autoscaler (HPA)** to automatically scale based on load.
- **YAML Manifests:** Define Deployment, Service, and ConfigMap.

## 6. CI/CD Pipeline

**Stages:**

1. Linting and automated tests.
2. Build and push the image to Docker Hub.
3. Automated deployment to Kubernetes via GitHub Actions.

**Example**: See ci-cd.pipeline.yaml file

## 7. Testing

- **Unit Tests:** Validation and data persistence.
- **Integration Tests:** Simulating real API calls.
- **Load Testing:** Ensuring response time under 500ms.

## 8. Future Enhancements

- Implementing support for Kafka for event-driven processing.
- Introducing GraphQL for more flexible queries.

## 9. Conclusion

This design ensures that the microservice is scalable, performant, and easy to maintain. The use of best practices such as Clean Architecture, caching, and Kubernetes allows handling millions of records without significant performance degradation.
