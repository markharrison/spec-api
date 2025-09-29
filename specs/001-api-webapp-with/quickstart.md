# Quickstart Guide: Color Management API

## Overview
This guide provides step-by-step instructions to run and test the Color Management API. The API provides endpoints for managing a collection of colors with full CRUD operations and interactive documentation.

## Prerequisites
- .NET 9.0 SDK installed
- Git for version control
- Web browser for Swagger UI
- HTTP client (curl, Postman, or browser) for API testing

## Quick Start Steps

### 1. Clone and Setup
```bash
# Clone the repository
git clone <repository-url>
cd color-management-api

# Restore dependencies
dotnet restore

# Build the solution
dotnet build
```

### 2. Run the Application
```bash
# Start the development server
dotnet run --project src/ColorApi

# Expected output:
# Building...
# info: Microsoft.Hosting.Lifetime[14]
#       Now listening on: https://localhost:5001
#       Now listening on: http://localhost:5000
# info: Microsoft.Hosting.Lifetime[0]
#       Application started. Press Ctrl+C to shut down.
```

### 3. Access Swagger UI
Open your web browser and navigate to:
- **HTTPS**: https://localhost:5001/swagger
- **HTTP**: http://localhost:5000/swagger

You should see the interactive API documentation with all available endpoints.

### 4. Test the API Endpoints

#### 4.1 Health Check
First, verify the API is running:
```bash
curl -X GET "https://localhost:5001/health" -k
```

Expected response:
```json
{
  "status": "Healthy",
  "timestamp": "2025-09-29T10:30:00Z"
}
```

#### 4.2 Get All Colors (Initially Empty)
```bash
curl -X GET "https://localhost:5001/api/colors" -k
```

Expected response:
```json
[]
```

#### 4.3 Add Your First Color
```bash
curl -X PUT "https://localhost:5001/api/colors/primary-red" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Primary Red",
    "hexValue": "#FF0000"
  }' -k
```

Expected response (201 Created):
```json
{
  "id": "primary-red",
  "name": "Primary Red",
  "hexValue": "#FF0000",
  "createdAt": "2025-09-29T10:30:00Z",
  "updatedAt": "2025-09-29T10:30:00Z"
}
```

#### 4.4 Add More Colors
```bash
# Add Ocean Blue
curl -X PUT "https://localhost:5001/api/colors/ocean-blue" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Ocean Blue", 
    "hexValue": "#3498DB"
  }' -k

# Add Forest Green
curl -X PUT "https://localhost:5001/api/colors/forest-green" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Forest Green",
    "hexValue": "#228B22"
  }' -k
```

#### 4.5 Get All Colors (Now Populated)
```bash
curl -X GET "https://localhost:5001/api/colors" -k
```

Expected response:
```json
[
  {
    "id": "primary-red",
    "name": "Primary Red",
    "hexValue": "#FF0000", 
    "createdAt": "2025-09-29T10:30:00Z",
    "updatedAt": "2025-09-29T10:30:00Z"
  },
  {
    "id": "ocean-blue",
    "name": "Ocean Blue",
    "hexValue": "#3498DB",
    "createdAt": "2025-09-29T10:31:00Z", 
    "updatedAt": "2025-09-29T10:31:00Z"
  },
  {
    "id": "forest-green",
    "name": "Forest Green",
    "hexValue": "#228B22",
    "createdAt": "2025-09-29T10:32:00Z",
    "updatedAt": "2025-09-29T10:32:00Z"
  }
]
```

#### 4.6 Get Specific Color
```bash
curl -X GET "https://localhost:5001/api/colors/ocean-blue" -k
```

Expected response:
```json
{
  "id": "ocean-blue",
  "name": "Ocean Blue", 
  "hexValue": "#3498DB",
  "createdAt": "2025-09-29T10:31:00Z",
  "updatedAt": "2025-09-29T10:31:00Z"
}
```

#### 4.7 Get Random Color
```bash
curl -X GET "https://localhost:5001/api/colors/random" -k
```

Expected response (one of the colors randomly selected):
```json
{
  "id": "forest-green",
  "name": "Forest Green",
  "hexValue": "#228B22", 
  "createdAt": "2025-09-29T10:32:00Z",
  "updatedAt": "2025-09-29T10:32:00Z"
}
```

#### 4.8 Update Existing Color
```bash
curl -X PUT "https://localhost:5001/api/colors/primary-red" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Bright Red",
    "hexValue": "#FF0000"
  }' -k
```

Expected response (200 OK):
```json
{
  "id": "primary-red",
  "name": "Bright Red",
  "hexValue": "#FF0000",
  "createdAt": "2025-09-29T10:30:00Z",
  "updatedAt": "2025-09-29T10:35:00Z"
}
```

### 5. Error Handling Tests

#### 5.1 Test Invalid Color ID
```bash
curl -X GET "https://localhost:5001/api/colors/nonexistent-color" -k
```

Expected response (404 Not Found):
```json
{
  "error": "NotFound",
  "message": "Color with ID 'nonexistent-color' not found",
  "correlationId": "12345678-1234-1234-1234-123456789012"
}
```

#### 5.2 Test Invalid Hex Value
```bash
curl -X PUT "https://localhost:5001/api/colors/invalid-color" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Invalid Color",
    "hexValue": "not-a-hex-value"
  }' -k
```

Expected response (400 Bad Request):
```json
{
  "error": "ValidationError",
  "message": "Invalid hex color format. Expected format: #RRGGBB or #RGB",
  "correlationId": "12345678-1234-1234-1234-123456789012"
}
```

#### 5.3 Test Random Color with Empty Collection
To test this scenario, you would need to restart the application (which clears in-memory data):
```bash
curl -X GET "https://localhost:5001/api/colors/random" -k
```

Expected response (404 Not Found):
```json
{
  "error": "NotFound", 
  "message": "No colors available in the collection",
  "correlationId": "12345678-1234-1234-1234-123456789012"
}
```

## Performance Validation

### Response Time Testing
Use curl with timing to verify performance requirements:
```bash
# Test response time (should be < 200ms)
curl -w "@curl-format.txt" -X GET "https://localhost:5001/api/colors" -k -s -o /dev/null
```

Create `curl-format.txt`:
```
     time_namelookup:  %{time_namelookup}\n
        time_connect:  %{time_connect}\n
     time_appconnect:  %{time_appconnect}\n
    time_pretransfer:  %{time_pretransfer}\n
       time_redirect:  %{time_redirect}\n
  time_starttransfer:  %{time_starttransfer}\n
                     ----------\n
          time_total:  %{time_total}\n
```

### Load Testing (Optional)
For more comprehensive performance testing:
```bash
# Install Apache Bench (if not already installed)
# Ubuntu: sudo apt-get install apache2-utils
# macOS: brew install httpie

# Run load test
ab -n 1000 -c 10 https://localhost:5001/api/colors
```

## Development Workflow

### Running Tests
```bash
# Run all tests
dotnet test

# Run with coverage
dotnet test --collect:"XPlat Code Coverage"

# Run specific test category
dotnet test --filter Category=Integration
```

### Development Mode
```bash
# Run with hot reload for development
dotnet watch run --project src/ColorApi
```

## Troubleshooting

### Common Issues

**Issue**: SSL Certificate errors
**Solution**: Use `-k` flag with curl or install development certificate:
```bash
dotnet dev-certs https --trust
```

**Issue**: Port already in use
**Solution**: Check for existing processes or change ports in appsettings.json

**Issue**: Application won't start
**Solution**: Verify .NET 9.0 SDK is installed:
```bash
dotnet --version
```

**Issue**: API returns 500 errors
**Solution**: Check application logs and ensure all dependencies are properly injected

### Logs and Monitoring
- Application logs are output to console in development
- Structured logging with correlation IDs for request tracing
- Health check endpoint at `/health` for monitoring

## Next Steps
1. Explore the interactive Swagger UI documentation
2. Try different color formats and edge cases
3. Test concurrent requests to verify thread safety
4. Review the generated OpenAPI specification
5. Set up automated testing with the provided test suites

## API Documentation
Complete API documentation is available at the Swagger UI endpoint when the application is running. The OpenAPI specification can also be downloaded from the Swagger UI interface.