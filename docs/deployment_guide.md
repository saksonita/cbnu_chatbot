# CBNU Chatbot Deployment Guide

## Prerequisites

- Docker & Docker Compose installed
- Linux server (Ubuntu 20.04 LTS or later recommended)
- Domain name
- SSL certificate (Let's Encrypt recommended)
- OpenAI API key
- PostgreSQL database (managed or self-hosted)

## Local Deployment

### 1. Clone Repository
```bash
git clone <repository_url>
cd cbnu_chatbot
```

### 2. Configure Environment
```bash
cp .env.example .env
# Edit .env with your settings
nano .env
```

### 3. Build and Run
```bash
docker-compose up -d
```

### 4. Initialize Database
```bash
docker-compose exec backend alembic upgrade head
```

### 5. Verify Services
```bash
# Check running containers
docker-compose ps

# View logs
docker-compose logs -f backend
```

## Production Deployment

### 1. Server Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Install Nginx (reverse proxy)
sudo apt install -y nginx

# Install Certbot (SSL certificates)
sudo apt install -y certbot python3-certbot-nginx
```

### 2. SSL Certificate Setup
```bash
# Generate SSL certificate
sudo certbot certonly --standalone -d yourdomain.com -d www.yourdomain.com

# Auto-renewal
sudo certbot renew --dry-run
```

### 3. Nginx Configuration

Create `/etc/nginx/sites-available/cbnu_chatbot`:

```nginx
upstream backend {
    server localhost:8000;
}

upstream frontend {
    server localhost:3000;
}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # Frontend
    location / {
        proxy_pass http://frontend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Backend API
    location /api/ {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 60s;
    }

    # WebSocket support
    location /ws {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

Enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/cbnu_chatbot /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### 4. Environment Configuration

Update `.env` for production:
```bash
# API Configuration
API_HOST=0.0.0.0
API_PORT=8000
API_DEBUG=false

# Database (use managed service or secure connection)
DATABASE_URL=postgresql://user:password@db-host:5432/chatbot_db

# LLM
OPENAI_API_KEY=your_production_key

# Security
SECRET_KEY=your_secret_key_here
JWT_EXPIRATION=3600
CORS_ORIGINS=https://yourdomain.com

# Logging
LOG_LEVEL=INFO
```

### 5. Docker Compose Production

Update `docker-compose.yml`:
```yaml
version: '3.8'

services:
  backend:
    image: cbnu-chatbot-backend:latest
    restart: always
    environment:
      DATABASE_URL: ${DATABASE_URL}
      OPENAI_API_KEY: ${OPENAI_API_KEY}
      API_DEBUG: false
    ports:
      - "8000:8000"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  frontend:
    image: cbnu-chatbot-frontend:latest
    restart: always
    ports:
      - "3000:3000"
    environment:
      REACT_APP_API_URL: https://yourdomain.com/api
```

### 6. Start Services

```bash
# Pull latest images
docker-compose pull

# Start services
docker-compose up -d

# Monitor
docker-compose logs -f
```

## Backup & Recovery

### Database Backup
```bash
# Backup
docker-compose exec postgres pg_dump -U chatbot_user chatbot_db > backup.sql

# Restore
docker-compose exec -T postgres psql -U chatbot_user chatbot_db < backup.sql
```

### Automated Backups
```bash
# Create backup script (cron job)
0 2 * * * /home/ubuntu/cbnu_chatbot/backup.sh
```

## Monitoring & Logging

### System Monitoring
```bash
# Install htop
sudo apt install -y htop
htop
```

### Application Logs
```bash
# View logs
docker-compose logs -f backend

# Export logs
docker-compose logs backend > logs.txt
```

### Health Checks
```bash
curl -X GET http://localhost:8000/health
```

## Scaling Considerations

### Horizontal Scaling
1. Use load balancer (AWS ELB, Nginx)
2. Scale backend instances
3. Use managed database service

### Database Optimization
1. Regular vacuum and analyze
2. Index optimization
3. Connection pooling

### Caching
1. Redis for session cache
2. Vector DB optimization
3. CDN for static assets

## Security Hardening

1. **Firewall**: Configure UFW
```bash
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

2. **SSH Hardening**: Disable password auth
3. **API Rate Limiting**: Enable in production
4. **HTTPS Only**: Enforce redirects
5. **Database Security**: Use strong passwords, private networks
6. **Regular Updates**: Automated security patches

## Troubleshooting

### Services not starting
```bash
docker-compose logs -f
docker-compose down
docker-compose up -d
```

### Database connection errors
```bash
docker-compose exec postgres psql -U chatbot_user -c "SELECT version();"
```

### High memory usage
```bash
docker stats
docker system prune -a
```

## Support

For issues, contact: support@cbnu-chatbot.edu
