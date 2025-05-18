# EduPlatform Deployment

Docker Compose configuration for deploying the educational platform.

## Components

- **eduapi** - Backend API (.NET Core)
- **frontend** - Frontend (React)
- **sqlserver** - Database
- **elasticsearch** - Log storage
- **kibana** - Interface for log analysis
- **minio** - Object storage

## Running the Application

1. Clone all repositories:
```bash
git clone <eduapi-repository-url>
git clone <eduportal-repository-url>
git clone <edu-deployment-repository-url>
```

2. Start all services:
```bash
cd edu-deployment
docker-compose up -d
```

3. Access the services:
- Frontend: http://localhost:3001
- API: http://localhost:8081
- Kibana: http://localhost:5601
- MinIO Console: http://localhost:9001 (login: minioadmin, password: minioadmin)
- SQL Server: localhost:1433 (login: sa, password: YourStrong@Password123)

## Management Commands

- View logs:
```bash
docker-compose logs -f [service_name]
```

- Stop all services:
```bash
docker-compose down
```

- Restart a specific service:
```bash
docker-compose restart [service_name]
```

- Rebuild and restart services:
```bash
docker-compose up -d --build
```

## Configuration

The `docker-compose.yml` file contains all necessary environment variables for development. Key configurations:

```yaml
# API Service
eduapi:
  build:
    context: ../EduApi
  environment:
    - ASPNETCORE_ENVIRONMENT=Development
    - ConnectionStrings__DefaultConnection=Server=sqlserver;Database=EduPortal;User Id=sa;Password=YourStrong@Password123;TrustServerCertificate=True
    - ElasticConfiguration__Uri=http://elasticsearch:9200
    - Minio__Endpoint=minio:9000
    - Minio__AccessKey=minioadmin
    - Minio__SecretKey=minioadmin
    - Minio__BucketName=edu-files
    - Jwt__Key=YourSuperSecretKey12345!@#$%ThisShouldBeAtLeast32CharactersLong
    - Jwt__Issuer=eduportal-api
    - Jwt__Audience=eduportal-client

# Frontend Service
frontend:
  build:
    context: ../eduportal
    args:
      REACT_APP_API_URL: http://localhost:8081/api
```

For production, consider:
1. Creating a separate `.env` file
2. Using Docker secrets for sensitive data
3. Setting up proper SSL certificates
4. Implementing container health checks

## Volumes and Persistence

The configuration includes persistent volumes for:
- SQL Server data
- Elasticsearch data
- MinIO data

This ensures your data is preserved between container restarts.

## Network Configuration

Two networks are configured:
- `edu-network` - Main application network
- `elk` - Logging infrastructure network

This separation provides better security and organization of services.
