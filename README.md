# Chirpy - A Twitter-like API Server

A lightweight, performant REST API server built with Go that demonstrates modern backend development practices. Chirpy lets users post short messages (chirps), manage their accounts, and interact through a clean RESTful interface.

## Why Chirpy?

This project showcases:
- **Clean Architecture** - Well-organized code with clear separation of concerns
- **Type-Safe Database Operations** - Using sqlc for compile-time SQL verification
- **Secure Authentication** - JWT-based auth with refresh tokens
- **Modern Go Patterns** - Idiomatic Go code following best practices
- **Production-Ready Features** - Health checks, metrics, webhook support

Perfect for learning Go backend development or as a foundation for your own social platform!

## Features

- **JWT Authentication** - Secure token-based authentication with refresh tokens
- **User Management** - Registration, login, and profile updates
- **Chirps (Posts)** - Create, read, update, and delete short messages
- **Webhook Integration** - Support for external service webhooks (Polka)
- **Metrics & Monitoring** - Built-in metrics and health endpoints
- **PostgreSQL Database** - Robust data persistence with migrations
- **Type-Safe Queries** - sqlc-generated code eliminates SQL injection risks
- **Well-Tested** - Comprehensive test coverage for critical paths

## Tech Stack

- **Go 1.24.4** - Fast, reliable backend language
- **PostgreSQL** - Battle-tested relational database
- **sqlc** - Generate type-safe Go from SQL
- **Goose** - Database migration management
- **JWT** - Industry-standard authentication
- **bcrypt** - Secure password hashing

## Installation

### Prerequisites

- Go 1.24+ installed
- PostgreSQL database running
- sqlc installed (`go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest`)
- Goose installed (`go install github.com/pressly/goose/v3/cmd/goose@latest`)

### Setup

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/basic-http-server.git
cd basic-http-server
```

2. **Install dependencies**
```bash
go mod tidy
```

3. **Set up environment variables**
```bash
# Create a .env file or export these variables
export DB_URL="postgres://username:password@localhost:5432/chirpy?sslmode=disable"
export JWT_SECRET="your-super-secret-jwt-key"
export PLATFORM="dev"
export POLKA_KEY="your-webhook-api-key"
```

4. **Run database migrations**
```bash
goose -dir sql/schema postgres "$DB_URL" up
```

5. **Generate database code** (if you modify SQL queries)
```bash
sqlc generate
```

6. **Build and run**
```bash
go build -o chirpy
./chirpy
```

The server will start on `http://localhost:8080`

## API Endpoints

### Health & Monitoring
- `GET /api/healthz` - Health check endpoint
- `GET /admin/metrics` - Server metrics

### Authentication
- `POST /api/login` - Login with email/password
- `POST /api/refresh` - Refresh access token
- `POST /api/revoke` - Revoke refresh token

### Users
- `POST /api/users` - Create new user account
- `PUT /api/users` - Update user information (requires auth)

### Chirps
- `POST /api/chirps` - Create a new chirp (requires auth)
- `GET /api/chirps` - List all chirps (optional filters)
- `GET /api/chirps/{id}` - Get specific chirp
- `DELETE /api/chirps/{id}` - Delete your chirp (requires auth)

### Webhooks
- `POST /api/polka/webhooks` - Webhook endpoint for Polka integration

### Admin
- `POST /admin/reset` - Reset database (development only)

## Usage Examples

### Register a new user
```bash
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "securepassword"}'
```

### Login
```bash
curl -X POST http://localhost:8080/api/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "securepassword"}'
```

### Create a chirp
```bash
curl -X POST http://localhost:8080/api/chirps \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"body": "This is my first chirp!"}'
```

### Get all chirps
```bash
curl http://localhost:8080/api/chirps
```

## Development

### Running tests
```bash
# Run all tests
go test ./...

# Run with coverage
go test -cover ./...

# Run specific package tests
go test ./internal/auth -v
```

### Code formatting
```bash
go fmt ./...
go vet ./...
```

### Database migrations

```bash
# Create a new migration
goose -dir sql/schema create migration_name sql

# Apply migrations
goose -dir sql/schema postgres "$DB_URL" up

# Rollback last migration
goose -dir sql/schema postgres "$DB_URL" down

# Check migration status
goose -dir sql/schema postgres "$DB_URL" status
```

### Regenerating database code

After modifying SQL queries in `sql/queries/`:
```bash
sqlc generate
```

## Project Structure

```
.
├── main.go                 # Application entry point
├── handler_*.go            # HTTP request handlers
├── json.go                 # JSON response helpers
├── metrics.go              # Metrics middleware
├── readiness.go            # Health check handler
├── reset.go                # Database reset handler
├── internal/
│   ├── auth/               # Authentication logic
│   │   ├── auth.go         # JWT & password functions
│   │   └── auth_test.go    # Auth unit tests
│   └── database/           # Generated database code
│       ├── *.sql.go        # sqlc-generated queries
│       └── models.go       # Database models
├── sql/
│   ├── queries/            # SQL queries for sqlc
│   └── schema/             # Database migrations
└── sqlc.yaml               # sqlc configuration
```

## Security Considerations

- Passwords are hashed using bcrypt before storage
- JWT tokens expire after a configurable duration
- All authenticated endpoints validate tokens before processing
- SQL injection prevention through parameterized queries (sqlc)
- Environment-based configuration keeps secrets out of code

## Contributing

Feel free to open issues or submit pull requests! Make sure to:
1. Run tests before submitting
2. Follow Go formatting standards
3. Update documentation for new features
4. Add tests for new functionality

## License

MIT License - feel free to use this project as a learning resource or foundation for your own applications!

## Acknowledgments

Built as a learning project to explore Go backend development, inspired by the need for a simple yet robust social platform API.