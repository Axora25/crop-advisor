# Docker Build Guide for Crop Suggestion Project

This guide provides commands to build and run the Crop Suggestion project using Docker.

## Prerequisites

- Docker installed on your system
- Docker Compose installed (usually comes with Docker Desktop)

## Quick Start

### 1. Set up Environment Variables

Copy the example environment file and configure it:

```bash
cp .env.example .env
```

Edit `.env` file with your configuration:
- Set `MYSQL_ROOT_PASSWORD` and `MYSQL_PASSWORD` to secure passwords
- Configure `EMAIL_USER` and `EMAIL_PASS` if using email features
- Adjust `PORT` if you want a different port (default: 8080)

### 2. Build and Run with Docker Compose

**Build and start all services:**
```bash
docker-compose up --build
```

**Build and start in detached mode (background):**
```bash
docker-compose up --build -d
```

**Start services (without rebuilding):**
```bash
docker-compose up
```

**Start services in detached mode:**
```bash
docker-compose up -d
```

### 3. Access the Application

Once the containers are running:
- **Application**: http://localhost:8080
- **MySQL**: localhost:3306

### 4. Useful Commands

**View running containers:**
```bash
docker-compose ps
```

**View logs:**
```bash
# All services
docker-compose logs

# Specific service
docker-compose logs app
docker-compose logs db

# Follow logs (real-time)
docker-compose logs -f app
```

**Stop services:**
```bash
docker-compose stop
```

**Stop and remove containers:**
```bash
docker-compose down
```

**Stop and remove containers, volumes, and networks:**
```bash
docker-compose down -v
```

**Rebuild without cache:**
```bash
docker-compose build --no-cache
```

**Execute commands in container:**
```bash
# Access app container shell
docker-compose exec app bash

# Access database
docker-compose exec db mysql -u root -p
```

**Restart a specific service:**
```bash
docker-compose restart app
docker-compose restart db
```

## Building Individual Docker Image

If you want to build just the Docker image without using docker-compose:

```bash
# Build the image
docker build -t crop-suggestion:latest .

# Run the container
docker run -d \
  --name crop_app \
  -p 8080:8080 \
  -e PORT=8080 \
  -e MYSQL_HOST=db \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=your_password \
  -e MYSQL_DATABASE=crop \
  crop-suggestion:latest
```

## Troubleshooting

**Check if ports are already in use:**
```bash
# Windows PowerShell
netstat -ano | findstr :8080
netstat -ano | findstr :3306

# Linux/Mac
lsof -i :8080
lsof -i :3306
```

**Remove all containers and start fresh:**
```bash
docker-compose down -v
docker-compose up --build
```

**Check container health:**
```bash
docker-compose ps
docker inspect crop_db | grep Health
```

**View detailed container information:**
```bash
docker inspect crop_app
docker inspect crop_db
```

## Database Setup

After starting the containers, you may need to:
1. Import your database schema
2. Run any migration scripts

To import a SQL file:
```bash
docker-compose exec -T db mysql -u root -p${MYSQL_ROOT_PASSWORD} ${MYSQL_DATABASE} < your_database.sql
```

Or copy SQL file and import:
```bash
docker cp your_database.sql crop_db:/tmp/
docker-compose exec db mysql -u root -p${MYSQL_ROOT_PASSWORD} ${MYSQL_DATABASE} < /tmp/your_database.sql
```

## Production Considerations

For production deployment:
1. Use strong passwords in `.env` file
2. Don't commit `.env` file to version control
3. Consider using Docker secrets for sensitive data
4. Set up proper backup strategy for database volumes
5. Configure proper firewall rules
6. Use HTTPS with reverse proxy (nginx/traefik)

