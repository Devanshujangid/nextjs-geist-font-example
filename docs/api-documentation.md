# InternHub API Documentation

## Overview
This document outlines the REST API endpoints for the InternHub internship and job portal platform.

**Base URL:** `http://localhost:8080/api/v1`

**Authentication:** JWT Bearer Token

## Authentication Endpoints

### POST /auth/register
Register a new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "role": "STUDENT",
  "profile": {
    "firstName": "John",
    "lastName": "Doe",
    "university": "Stanford University",
    "degree": "Computer Science",
    "graduationYear": 2024
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": 1,
      "email": "user@example.com",
      "role": "STUDENT",
      "isVerified": false
    },
    "token": "jwt_token_here"
  }
}
```

### POST /auth/login
Authenticate user and get access token.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

### POST /auth/logout
Logout user and invalidate token.

## Student Endpoints

### GET /students/profile
Get current student's profile.

### PUT /students/profile
Update student profile.

### POST /students/upload-resume
Upload student resume.

### GET /students/applications
Get student's job applications.

### GET /students/saved-jobs
Get student's saved/bookmarked jobs.

### POST /students/save-job
Save/bookmark a job.

### DELETE /students/save-job/:jobId
Remove job from saved list.

## Recruiter Endpoints

### GET /recruiters/profile
Get current recruiter's profile.

### PUT /recruiters/profile
Update recruiter profile.

### GET /recruiters/jobs
Get recruiter's job postings.

### POST /recruiters/jobs
Create new job posting.

### PUT /recruiters/jobs/:id
Update job posting.

### DELETE /recruiters/jobs/:id
Delete job posting.

### GET /recruiters/applications
Get applications for recruiter's jobs.

### PUT /recruiters/applications/:id/status
Update application status.

## Job Endpoints

### GET /jobs
Get all active job listings.

**Query Parameters:**
- `search`: Search term
- `type`: Job type (INTERNSHIP, FULL_TIME, PART_TIME)
- `location`: Location filter
- `skills`: Comma-separated skills
- `page`: Page number
- `limit`: Items per page

### GET /jobs/:id
Get specific job details.

### POST /jobs/:id/apply
Apply for a job (students only).

**Request Body:**
```json
{
  "coverLetter": "Dear Hiring Manager...",
  "resumeUrl": "/uploads/resume.pdf"
}
```

## Application Endpoints

### GET /applications
Get applications (filtered by user role).

### GET /applications/:id
Get specific application details.

### PUT /applications/:id/status
Update application status (recruiters only).

### DELETE /applications/:id
Withdraw application (students only).

## Admin Endpoints

### GET /admin/users
Get all users with pagination.

### PUT /admin/users/:id/status
Update user status (activate/deactivate).

### GET /admin/jobs
Get all jobs for moderation.

### PUT /admin/jobs/:id/status
Approve/reject job postings.

### GET /admin/analytics
Get platform analytics.

### GET /admin/reports
Generate various reports.

## Notification Endpoints

### GET /notifications
Get user notifications.

### PUT /notifications/:id/read
Mark notification as read.

### PUT /notifications/read-all
Mark all notifications as read.

## File Upload Endpoints

### POST /upload/resume
Upload resume file.

### POST /upload/profile-image
Upload profile image.

### POST /upload/company-logo
Upload company logo.

## Search Endpoints

### GET /search/jobs
Advanced job search.

### GET /search/students
Search students (recruiters only).

### GET /search/companies
Search companies.

## Skills Endpoints

### GET /skills
Get all available skills.

### GET /skills/categories
Get skill categories.

## Error Responses

All endpoints return errors in the following format:

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": "Additional error details"
  }
}
```

## Status Codes

- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `409` - Conflict
- `422` - Validation Error
- `500` - Internal Server Error

## Rate Limiting

API requests are limited to:
- 100 requests per minute for authenticated users
- 20 requests per minute for unauthenticated users

## Pagination

List endpoints support pagination:

```json
{
  "success": true,
  "data": {
    "items": [...],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 100,
      "totalPages": 10,
      "hasNext": true,
      "hasPrev": false
    }
  }
}
```

## WebSocket Events

Real-time notifications via WebSocket:

- `notification:new` - New notification received
- `application:status_changed` - Application status updated
- `job:new_match` - New job matches user profile
- `message:received` - New message received

## Spring Boot Implementation Notes

### Dependencies Required

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
        <version>0.9.1</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
</dependencies>
```

### Controller Structure

```java
@RestController
@RequestMapping("/api/v1")
@CrossOrigin(origins = "http://localhost:3000")
public class AuthController {
    
    @PostMapping("/auth/login")
    public ResponseEntity<ApiResponse> login(@RequestBody LoginRequest request) {
        // Implementation
    }
    
    @PostMapping("/auth/register")
    public ResponseEntity<ApiResponse> register(@RequestBody RegisterRequest request) {
        // Implementation
    }
}
```

### Security Configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and().csrf().disable()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeRequests()
            .antMatchers("/api/v1/auth/**").permitAll()
            .antMatchers(HttpMethod.GET, "/api/v1/jobs/**").permitAll()
            .anyRequest().authenticated();
    }
}
