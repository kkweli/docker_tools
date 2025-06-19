# Docker Compose Setup for Trivy and Local Registry

This project provides a Docker Compose setup for deploying Trivy and a local Docker registry on your local Docker Desktop environment.

## Prerequisites

- Docker Desktop installed on your machine.
- Basic knowledge of Docker and Docker Compose.

## Architecture

The setup consists of three main components:

1. **Local Registry (Port 5000)**
   - Private Docker registry for storing container images
   - Configured with persistent storage
   - Supports image deletion and health checks

2. **Trivy Scanner (Port 8080)**
   - Vulnerability scanner for container images
   - Integrated with Redis for caching
   - Supports REST API for automated scanning

3. **Redis Cache (Port 6379)**
   - Caching layer for Trivy scanner
   - Improves scanning performance
   - Persistent storage for scan results

## Project Structure

- `docker-compose.yml`: Defines the services for Trivy and the local registry.
- `config/registry-config.yml`: Configuration settings for the local Docker registry.
- `config/trivy.yaml`: Configuration for Trivy vulnerability scanning.
- `.env`: Environment variables for configuration management.
- `README.md`: Documentation for the project.

## Storage Locations

- Registry images: `C:\projects\local_registry\registry`
- Trivy cache: `C:\projects\local_registry\trivy-cache`
- Scan reports: `C:\projects\local_registry\reports`

Before starting the services, create these directories:

```powershell
New-Item -Path "C:\projects\local_registry" -ItemType Directory -Force
New-Item -Path "C:\projects\local_registry\registry" -ItemType Directory -Force
New-Item -Path "C:\projects\local_registry\trivy-cache" -ItemType Directory -Force
New-Item -Path "C:\projects\local_registry\reports" -ItemType Directory -Force
```

## Getting Started

1. Clone this repository to your local machine.
2. Navigate to the project directory:
   ```bash
   cd docker-compose-trivy-registry
   ```
3. Build and start the services using Docker Compose:
   ```bash
   docker-compose up -d
   ```
4. Access the local registry at `http://localhost:5000`.
5. Use Trivy to scan images in your local registry.

## Application Examples

### Pushing Images to Local Registry
```bash
docker tag your-image:tag localhost:5000/your-image:tag
docker push localhost:5000/your-image:tag
```

### Scanning Images with Trivy
```bash
# Scan image directly
curl -X POST "http://localhost:8080/scanner/v1/scan" \
     -H "Content-Type: application/json" \
     -d '{"image": "localhost:5000/your-image:tag"}'

# Get vulnerability report
curl "http://localhost:8080/scanner/v1/results/localhost:5000/your-image:tag"
```

## Usage

### Registry Operations
- List images: `curl http://localhost:5000/v2/_catalog`
- List tags: `curl http://localhost:5000/v2/{image-name}/tags/list`
- Delete image: Use registry API with DELETE method

### Trivy Configuration
- Severity levels: HIGH, CRITICAL
- Scan timeout: 5 minutes
- Supports both OS and library vulnerability scanning
- Auto-refresh vulnerability database enabled

### Environment Variables
Configure the following in `.env` file:
- Registry storage settings
- Trivy scanning parameters
- Redis cache configuration

## Maintenance

- Registry data is persisted in `C:\projects\local_registry\registry`
- Trivy cache is stored in `C:\projects\local_registry\trivy-cache`
- Scan reports are saved in `C:\projects\local_registry\reports`
- Redis data is preserved in redis-data volume
- Use `docker-compose down` to stop services
- Use `docker-compose down -v` to remove volumes

## License

This project is licensed under the MIT License.