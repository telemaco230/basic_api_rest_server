# Basic API REST Server

A Laravel-based REST API server implementing JWT authentication with Swagger/OpenAPI documentation.

## Architecture Overview

### Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Runtime | PHP | 8.3+ |
| Framework | Laravel | 13.x |
| Authentication | tymon/jwt-auth | 2.x |
| API Documentation | darkaonline/l5-swagger | 11.x |
| Database | SQLite (dev) / MySQL (prod) | - |

### Authentication Flow

#### Login Flow
```
Client → POST /api/auth/login {email, password}
         → Validate credentials
         → Auth::attempt()
         → Generate JWT token
         → Return {access_token, token_type, expires_in}
```

#### Protected Request Flow
```
Client → GET /api/auth/user
       → Authorization: Bearer <token>
       → JWT Middleware validates token
       → Auth::user() returns user data
       → Return user JSON
```

## API Endpoints

| Method | Endpoint | Auth Required | Description |
|--------|----------|---------------|-------------|
| POST | `/api/auth/login` | No | Authenticate and get JWT token |
| POST | `/api/auth/logout` | Yes (Bearer) | Invalidate current token |
| GET | `/api/auth/user` | Yes (Bearer) | Get current user info |
| POST | `/api/auth/refresh` | Yes (Bearer) | Refresh JWT token |

## Setup and Installation

### Prerequisites

- PHP >= 8.3
- Composer
- A supported database (MySQL, PostgreSQL, SQLite)

### Installation Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd basic_api_rest_server
   ```

2. **Install dependencies**
   ```bash
   composer install
   ```

3. **Environment setup**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Configure the database** in `.env`:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=basic_api
   DB_USERNAME=root
   DB_PASSWORD=secret
   ```

5. **Generate JWT secret**
   ```bash
   php artisan jwt:secret
   ```

6. **Run migrations**
   ```bash
   php artisan migrate
   ```

7. **Generate Swagger documentation**
   ```bash
   php artisan l5-swagger:generate
   ```

8. **Start the server**
   ```bash
   php artisan serve
   ```

The API will be available at `http://localhost:8000/api` and Swagger UI at `http://localhost:8000/api/documentation`.

## JWT Configuration

Key JWT settings in `config/jwt.php`:

| Setting | Default | Description |
|---------|---------|-------------|
| `ttl` | 60 | Token TTL in minutes |
| `refresh_ttl` | 20160 | Refresh window in minutes (14 days) |
| `algo` | HS256 | Signing algorithm |
| `blacklist_enabled` | true | Token blacklist (required for logout) |

## Security Considerations

1. **Token Storage**: Store JWT tokens in `httpOnly` cookies or secure `localStorage` (client responsibility).
2. **HTTPS**: Always use HTTPS in production to prevent token interception.
3. **Token Expiry**: Default TTL is 60 minutes; refresh tokens are valid for 14 days.
4. **Blacklist**: Token blacklist is enabled to support logout/invalidation.
5. **Secret Key**: The `JWT_SECRET` must be kept secure and never committed to version control.

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `APP_KEY` | Laravel application key | Generated |
| `JWT_SECRET` | JWT signing secret | Generated |
| `JWT_TTL` | Token TTL in minutes | 60 |
| `DB_CONNECTION` | Database driver | sqlite |
| `DB_DATABASE` | Database path/name | database/database.sqlite |

## API Documentation

Interactive API documentation is available via Swagger UI at:
```
http://localhost:8000/api/documentation
```

## Testing

Run the test suite:
```bash
php artisan test
```
