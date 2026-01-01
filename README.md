# Voucher Management System

A full-stack web application for managing discount vouchers with CRUD operations, CSV import/export, and user authentication.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Running the Application](#running-the-application)
- [Docker Deployment](#docker-deployment)
- [API Documentation](#api-documentation)
- [Environment Variables](#environment-variables)
- [Contributing](#contributing)
- [License](#license)

## Overview

The Voucher Management System is a modern web application designed to streamline the creation, management, and tracking of discount vouchers. It provides an intuitive interface for administrators to manage voucher codes, discount percentages, and expiration dates, with features like bulk CSV uploads and exports.

## Features

### Core Features
- **User Authentication**: Secure JWT-based authentication system
- **CRUD Operations**: Create, read, update, and delete vouchers
- **CSV Management**: Bulk import and export voucher data via CSV files
- **Search & Filter**: Real-time search and advanced filtering options
  - Filter by expiry date
  - Filter by discount percentage
  - Sort in ascending/descending order
- **Pagination**: Efficient data viewing with customizable items per page
- **Form Validation**: Client-side validation using Yup schema
- **Toast Notifications**: User-friendly feedback for all operations
- **Responsive Design**: Mobile-first design with TailwindCSS

### Technical Features
- RESTful API architecture
- PostgreSQL database integration
- Multi-stage Docker builds for optimized images
- CORS configuration for cross-origin requests
- Input sanitization and validation
- Error handling and logging

## Tech Stack

### Frontend
- **Framework**: React 18 with TypeScript
- **Build Tool**: Vite
- **Styling**: TailwindCSS
- **Form Management**: React Hook Form
- **Validation**: Yup
- **Routing**: React Router v6
- **HTTP Client**: Axios
- **Icons**: Lucide React
- **Server**: Nginx (production)

### Backend
- **Language**: Go 1.24
- **Framework**: Gin
- **Database**: PostgreSQL
- **ORM**: GORM
- **Authentication**: JWT (golang-jwt)
- **CORS**: gin-cors
- **Environment**: fiver

### DevOps
- **Containerization**: Docker & Docker Compose
- **Web Server**: Nginx
- **Reverse Proxy**: Configured with security headers

## Project Structure

```
voucher-management-system/
├── voucher-frontend/          # React TypeScript frontend (git submodule)
│   ├── src/
│   │   ├── components/       # Reusable UI components
│   │   ├── contexts/         # React Context providers
│   │   ├── pages/            # Page components
│   │   ├── services/         # API service layer
│   │   └── main.tsx          # Application entry point
│   ├── public/               # Static assets
│   ├── nginx.conf            # Nginx configuration
│   └── Dockerfile            # Multi-stage build
│
├── voucher-backend/           # Go API backend (git submodule)
│   ├── cmd/api/              # Application entry point
│   ├── internal/
│   │   ├── config/           # Configuration management
│   │   ├── database/         # Database connection
│   │   ├── handlers/         # HTTP request handlers
│   │   ├── middleware/       # Custom middleware
│   │   ├── models/           # Data models
│   │   ├── repository/       # Data access layer
│   │   ├── routes/           # Route definitions
│   │   └── utils/            # Utility functions
│   └── Dockerfile            # Multi-stage build
│
├── docs/                      # Documentation files
├── docker-compose.yml         # Docker composition file
├── .env.example              # Environment variables template
└── README.md                 # This file
```

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v20 or higher)
- **Go** (v1.24 or higher)
- **PostgreSQL** (v14 or higher)
- **Docker** and **Docker Compose** (optional, for containerized deployment)
- **Git** (for cloning the repository)

## Installation

### 1. Clone the Repository

```bash
git clone --recurse-submodules https://github.com/shoelfikar/voucher-management-system.git
cd voucher-management-system
```

If you already cloned without submodules:

```bash
git submodule init
git submodule update
```

### 2. Set Up Environment Variables

Copy the example environment file and configure it:

```bash
cp .env.example .env
```

Edit the `.env` file with your configuration:

```env
# Application Ports
PORT=8080
FRONTEND_PORT=80
GIN_MODE=release

# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=your_password
DB_NAME=voucher_db
DB_SSLMODE=disable

# JWT Configuration
JWT_SECRET=your-super-secret-key-change-this
JWT_EXPIRATION=24h

# CORS Configuration
ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000
```

### 3. Set Up the Database

Create a PostgreSQL database:

```sql
CREATE DATABASE voucher_db;
```

The application will automatically create the required tables on first run.

## Running the Application

### Development Mode

#### Backend

```bash
cd voucher-backend
make install
make run
```

The backend API will start on `http://localhost:8080`

#### Frontend

```bash
cd voucher-frontend
npm install
npm run dev
```

The frontend will start on `http://localhost:5173`

### Production Build

#### Backend

```bash
cd voucher-backend
go build -o main cmd/api/main.go
./main
```

#### Frontend

```bash
cd voucher-frontend
npm install
npm run build
npm run preview
```

## Docker Deployment

### Using Docker Compose

1. **Create external network** (optional):

```bash
docker network create internal-network
```

2. **Build and run containers**:

```bash
docker-compose up -d --build
```

This will start:
- Frontend on `http://localhost:80`
- Backend on `http://localhost:8080`

3. **View logs**:

```bash
docker-compose logs -f
```

4. **Stop containers**:

```bash
docker-compose down
```

### Building Individual Images

#### Backend

```bash
cd voucher-backend
docker build -t voucher-backend:latest .
docker run -p 8080:8080 --env-file ../.env voucher-backend:latest
```

#### Frontend

```bash
cd voucher-frontend
docker build -t voucher-frontend:latest .
docker run -p 80:80 voucher-frontend:latest
```

## API Documentation

### Authentication Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register a new user |
| POST | `/api/auth/login` | Login and get JWT token |

### Voucher Endpoints (Protected)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/vouchers` | Get all vouchers (with pagination, search, filter) |
| GET | `/api/vouchers/:id` | Get voucher by ID |
| POST | `/api/vouchers` | Create a new voucher |
| PUT | `/api/vouchers/:id` | Update a voucher |
| DELETE | `/api/vouchers/:id` | Delete a voucher |
| POST | `/api/vouchers/upload` | Upload vouchers via CSV |
| GET | `/api/vouchers/export` | Export vouchers to CSV |

### Query Parameters for GET /api/vouchers

- `page` - Page number (default: 1)
- `limit` - Items per page (default: 10)
- `search` - Search by voucher code
- `sort_by` - Sort field (created_at, expiry_date, discount_percent)
- `sort_order` - Sort direction (asc, desc)

### Example Requests

#### Login

```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@example.com",
    "password": "password123"
  }'
```

#### Create Voucher

```bash
curl -X POST http://localhost:8080/api/vouchers \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "voucher_code": "SAVE20",
    "discount_percent": 20,
    "expiry_date": "2025-12-31"
  }'
```

## Environment Variables

### Backend Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Backend server port | 8080 |
| `GIN_MODE` | Gin framework mode (debug/release) | release |
| `DB_HOST` | Database host | localhost |
| `DB_PORT` | Database port | 5432 |
| `DB_USER` | Database username | postgres |
| `DB_PASSWORD` | Database password | postgres |
| `DB_NAME` | Database name | voucher_db |
| `DB_SSLMODE` | PostgreSQL SSL mode | disable |
| `JWT_SECRET` | Secret key for JWT signing | - |
| `JWT_EXPIRATION` | JWT token expiration time | 24h |
| `ALLOWED_ORIGINS` | CORS allowed origins | http://localhost:5173 |

### Frontend Environment Variables

The frontend uses Vite's environment variable system. Create a `.env` file in the `voucher-frontend` directory:

```env
VITE_API_BASE_URL=http://localhost:8080
```

## Features in Detail

### CSV Upload Format

The CSV file should have the following columns:

```csv
voucher_code,discount_percent,expiry_date
SAVE20,20,2025-12-31
DISCOUNT10,10,2025-06-30
SUMMER50,50,2025-08-31
```

### Validation Rules

- **Voucher Code**: Alphanumeric uppercase only, max 50 characters
- **Discount Percent**: Number between 1 and 100
- **Expiry Date**: Must be today or a future date

### Filter & Sort Options

- **Filter by Expiry Date**: Sort vouchers by expiration date
- **Filter by Discount**: Sort vouchers by discount percentage
- **Descending Order**: Toggle between ascending and descending sort
- **Multiple Filters**: Apply multiple filters simultaneously

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Guidelines

- Follow Go best practices and conventions
- Use TypeScript for all React components
- Write meaningful commit messages
- Add tests for new features
- Update documentation as needed

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For support, please open an issue in the GitHub repository or contact the maintainers.

## Acknowledgments

- Built with [Gin](https://gin-gonic.com/) web framework
- UI components inspired by modern design systems
- Icons by [Lucide](https://lucide.dev/)

---

**Made with ❤️ by [Sulfikardi](https://github.com/shoelfikar)**
