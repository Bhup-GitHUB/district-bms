# 🎬 District - Movie Ticket Booking Platform

A scalable, production-ready movie ticket booking system built with Go, similar to BookMyShow and Zomato's District app. This backend supports real-time seat booking, payment processing, and multi-city theater management.

[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?style=flat&logo=go)](https://golang.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-316192?style=flat&logo=postgresql)](https://www.postgresql.org)
[![Redis](https://img.shields.io/badge/Redis-7.0+-DC382D?style=flat&logo=redis)](https://redis.io)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📋 Table of Contents
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Getting Started](#-getting-started)
- [API Documentation](#-api-documentation)
- [Database Schema](#-database-schema)
- [Project Structure](#-project-structure)
- [Configuration](#-configuration)
- [Development](#-development)
- [Testing](#-testing)
- [Deployment](#-deployment)
- [Contributing](#-contributing)

## ✨ Features

### Core Features
- 🔐 **User Authentication** - JWT-based auth with email/phone verification
- 🎥 **Movie Management** - Browse movies, view details, trailers, and ratings
- 🏢 **Theater Management** - Multi-city theaters with multiple screens
- 🪑 **Real-time Seat Booking** - Live seat availability with distributed locking
- 💳 **Payment Integration** - Stripe/Razorpay integration with retry mechanism
- 📧 **Notifications** - Email/SMS/Push notifications for bookings
- 📍 **Location-based Search** - Find theaters and movies near you
- ⭐ **Reviews & Ratings** - User reviews and movie ratings

### Technical Features
- 🚀 **High Performance** - Built with Gin framework for speed
- 🔒 **Distributed Locking** - Redis-based locking for concurrent bookings
- 📊 **Event-driven Architecture** - Kafka for async processing
- 💾 **Caching Strategy** - Redis caching for frequently accessed data
- 📈 **Monitoring** - Prometheus metrics and structured logging
- 🔄 **Database Replication** - Master-slave setup for read scalability
- 🛡️ **Rate Limiting** - API rate limiting to prevent abuse
- 🧪 **Comprehensive Testing** - Unit and integration tests

## 🛠 Tech Stack

### Backend
- **Language**: Go 1.21+
- **Framework**: Gin Web Framework
- **ORM**: GORM
- **Database**: PostgreSQL 15+
- **Cache**: Redis 7.0+
- **Message Queue**: Apache Kafka
- **Search**: (Optional) Elasticsearch

### Infrastructure
- **Containerization**: Docker & Docker Compose
- **Monitoring**: Prometheus + Grafana
- **Logging**: Zap (structured logging)
- **CI/CD**: GitHub Actions
- **Cloud**: AWS / GCP / DigitalOcean

### Third-party Services
- **Payment**: Stripe / Razorpay
- **Email**: SendGrid / AWS SES
- **SMS**: Twilio
- **Storage**: AWS S3 / MinIO
- **Push Notifications**: Firebase Cloud Messaging

## 🏗 Architecture

### High-Level Architecture
```
Client → Load Balancer → API Gateway → Microservices
                                      ↓
                        ┌─────────────┴─────────────┐
                        ↓                           ↓
                   PostgreSQL                    Redis
                (Master + Replicas)         (Cache + Lock)
                        ↑                           ↑
                        └───────── Kafka ──────────┘
```

### Booking Flow (Critical Path)
1. User selects seats → Temporary hold (10 min TTL)
2. Distributed lock acquired via Redis
3. Payment initiated → Event published to Kafka
4. Payment success → Booking confirmed
5. Notification sent via Kafka consumer
6. Background job releases expired holds

## 🚀 Getting Started

### Prerequisites
- Go 1.21 or higher
- Docker & Docker Compose
- PostgreSQL 15+
- Redis 7.0+
- Kafka (optional for local dev)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/district-backend.git
cd district-backend
```

2. **Copy environment variables**
```bash
cp .env.example .env
# Edit .env with your configuration
```

3. **Install dependencies**
```bash
go mod download
```

4. **Start infrastructure with Docker**
```bash
docker-compose up -d postgres redis kafka
```

5. **Run database migrations**
```bash
make migrate-up
```

6. **Seed the database (optional)**
```bash
make seed
```

7. **Run the application**
```bash
# Development mode with hot reload
make dev

# Or standard run
go run cmd/api/main.go
```

The API will be available at `http://localhost:8080`

## 📚 API Documentation

### Authentication Endpoints
```http
POST   /api/v1/auth/signup          # User registration
POST   /api/v1/auth/login           # User login
POST   /api/v1/auth/verify-email    # Email verification
POST   /api/v1/auth/refresh-token   # Refresh JWT token
POST   /api/v1/auth/forgot-password # Password reset
```

### User Endpoints
```http
GET    /api/v1/users/profile        # Get user profile
PUT    /api/v1/users/profile        # Update profile
GET    /api/v1/users/bookings       # Get user bookings
```

### Movie Endpoints
```http
GET    /api/v1/movies               # List all movies
GET    /api/v1/movies/:id           # Get movie details
GET    /api/v1/movies/trending      # Trending movies
GET    /api/v1/movies/upcoming      # Upcoming releases
GET    /api/v1/movies/search        # Search movies
POST   /api/v1/movies/:id/reviews   # Add review
```

### Theater & Show Endpoints
```http
GET    /api/v1/cities               # List cities
GET    /api/v1/theaters             # List theaters by city
GET    /api/v1/theaters/:id         # Get theater details
GET    /api/v1/shows                # Get shows by movie/date
GET    /api/v1/shows/:id/seats      # Get seat availability
```

### Booking Endpoints
```http
POST   /api/v1/bookings/hold        # Hold seats (10 min)
POST   /api/v1/bookings/confirm     # Confirm booking
GET    /api/v1/bookings/:id         # Get booking details
DELETE /api/v1/bookings/:id         # Cancel booking
```

### Payment Endpoints
```http
POST   /api/v1/payments/initiate    # Initiate payment
POST   /api/v1/payments/webhook     # Payment gateway webhook
GET    /api/v1/payments/:id         # Get payment status
```

**Swagger Documentation**: Available at `/swagger/index.html` when running the server

## 🗄 Database Schema

### Core Tables
- **users** - User accounts and profiles
- **movies** - Movie information and metadata
- **cities** - Cities where service is available
- **theaters** - Theater locations and details
- **screens** - Movie screens within theaters
- **seats** - Seat configuration per screen
- **shows** - Movie showtimes
- **bookings** - Booking records
- **booking_seats** - Seat-booking relationships
- **payments** - Payment transactions
- **reviews** - User reviews and ratings

See [Database Schema Diagram](#) for detailed ER diagram.

## 📁 Project Structure

```
movie-booking-backend/
├── cmd/
│   └── api/
│       └── main.go              # Application entry point
├── internal/
│   ├── config/                  # Configuration management
│   ├── models/                  # Database models (GORM)
│   ├── repositories/            # Data access layer
│   ├── services/                # Business logic layer
│   ├── handlers/                # HTTP handlers (controllers)
│   ├── middleware/              # HTTP middleware
│   ├── dto/                     # Request/Response DTOs
│   ├── utils/                   # Utility functions
│   ├── database/                # Database connection
│   ├── cache/                   # Redis connection
│   └── queue/                   # Kafka producer/consumer
├── pkg/                         # Public packages
├── migrations/                  # Database migrations
├── docs/                        # Documentation
├── tests/                       # Tests
├── scripts/                     # Utility scripts
├── .env.example
├── docker-compose.yml
├── Dockerfile
├── Makefile
└── README.md
```

## ⚙️ Configuration

Key environment variables:

```env
# Application
APP_ENV=development
APP_PORT=8080
API_VERSION=v1

# Database
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=password
DB_NAME=district_db

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=

# Kafka
KAFKA_BROKERS=localhost:9092

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRY=24h

# Payment Gateway
STRIPE_SECRET_KEY=sk_test_xxx
RAZORPAY_KEY_ID=rzp_test_xxx
RAZORPAY_KEY_SECRET=xxx

# Notifications
SENDGRID_API_KEY=SG.xxx
TWILIO_ACCOUNT_SID=ACxxx
TWILIO_AUTH_TOKEN=xxx
```

## 💻 Development

### Makefile Commands
```bash
make run          # Run the application
make dev          # Run with hot reload (air)
make build        # Build binary
make test         # Run tests
make test-cover   # Run tests with coverage
make lint         # Run linter
make migrate-up   # Run migrations
make migrate-down # Rollback migrations
make seed         # Seed database
make swagger      # Generate Swagger docs
make docker-up    # Start Docker services
make docker-down  # Stop Docker services
```

### Code Style
- Follow standard Go conventions
- Use `gofmt` for formatting
- Run `golangci-lint` before committing
- Write tests for new features
- Document public functions and types

## 🧪 Testing

```bash
# Run all tests
make test

# Run with coverage
make test-cover

# Run specific package
go test ./internal/services/... -v

# Run integration tests
make test-integration
```

## 🚢 Deployment

### Docker Deployment
```bash
# Build image
docker build -t district-backend:latest .

# Run container
docker run -p 8080:8080 --env-file .env district-backend:latest
```

### Kubernetes Deployment
```bash
kubectl apply -f k8s/
```

### Environment-specific Configs
- **Development**: `.env.development`
- **Staging**: `.env.staging`
- **Production**: `.env.production`

## 📊 Monitoring & Logging

### Metrics
- Prometheus metrics available at `/metrics`
- Grafana dashboards for visualization
- Key metrics: Request rate, latency, error rate, booking success rate

### Logging
- Structured logging with Zap
- Log levels: Debug, Info, Warn, Error
- Centralized logging with ELK stack (optional)



⭐ **Star this repo if you find it helpful!**
