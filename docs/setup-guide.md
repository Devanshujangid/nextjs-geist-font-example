# InternHub Setup Guide

## Project Overview
InternHub is a comprehensive web-based portal for managing internships and job opportunities. It connects students with recruiters and provides admin-level control for moderation and analytics.

## Tech Stack
- **Frontend**: Next.js 14, TypeScript, Tailwind CSS, shadcn/ui
- **Backend**: Java Spring Boot (to be implemented)
- **Database**: MySQL
- **Authentication**: JWT tokens
- **File Storage**: Local/AWS S3

## Prerequisites
- Node.js 18+ and npm
- Java 17+ (for backend)
- MySQL 8.0+
- Git

## Frontend Setup (Current Implementation)

### 1. Clone and Install Dependencies
```bash
git clone <repository-url>
cd internhub
npm install
```

### 2. Environment Configuration
Create a `.env.local` file in the root directory:
```env
# Database
DATABASE_URL="mysql://username:password@localhost:3306/internhub"

# JWT Secret
JWT_SECRET="your-super-secret-jwt-key-here"

# File Upload
UPLOAD_DIR="./uploads"
MAX_FILE_SIZE="5MB"

# Email Configuration (for notifications)
SMTP_HOST="smtp.gmail.com"
SMTP_PORT="587"
SMTP_USER="your-email@gmail.com"
SMTP_PASS="your-app-password"

# Frontend URL
NEXT_PUBLIC_API_URL="http://localhost:8080/api/v1"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

### 3. Run Development Server
```bash
npm run dev
```

The application will be available at `http://localhost:3000`

## Backend Setup (Spring Boot)

### 1. Project Structure
```
backend/
├── src/main/java/com/internhub/
│   ├── InternhubApplication.java
│   ├── config/
│   │   ├── SecurityConfig.java
│   │   ├── JwtConfig.java
│   │   └── CorsConfig.java
│   ├── controller/
│   │   ├── AuthController.java
│   │   ├── StudentController.java
│   │   ├── RecruiterController.java
│   │   ├── JobController.java
│   │   └── AdminController.java
│   ├── service/
│   │   ├── AuthService.java
│   │   ├── StudentService.java
│   │   ├── RecruiterService.java
│   │   ├── JobService.java
│   │   └── EmailService.java
│   ├── repository/
│   │   ├── UserRepository.java
│   │   ├── StudentRepository.java
│   │   ├── RecruiterRepository.java
│   │   ├── JobRepository.java
│   │   └── ApplicationRepository.java
│   ├── model/
│   │   ├── User.java
│   │   ├── Student.java
│   │   ├── Recruiter.java
│   │   ├── Job.java
│   │   └── Application.java
│   ├── dto/
│   │   ├── LoginRequest.java
│   │   ├── RegisterRequest.java
│   │   ├── JobRequest.java
│   │   └── ApiResponse.java
│   └── util/
│       ├── JwtUtil.java
│       └── FileUtil.java
└── src/main/resources/
    ├── application.yml
    └── data.sql
```

### 2. Dependencies (pom.xml)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.0</version>
        <relativePath/>
    </parent>
    
    <groupId>com.internhub</groupId>
    <artifactId>internhub-backend</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-api</artifactId>
            <version>0.11.5</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-impl</artifactId>
            <version>0.11.5</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-jackson</artifactId>
            <version>0.11.5</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### 3. Application Configuration (application.yml)
```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/internhub
    username: ${DB_USERNAME:root}
    password: ${DB_PASSWORD:password}
    driver-class-name: com.mysql.cj.jdbc.Driver
    
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect
        
  servlet:
    multipart:
      max-file-size: 5MB
      max-request-size: 5MB
      
  mail:
    host: smtp.gmail.com
    port: 587
    username: ${SMTP_USERNAME}
    password: ${SMTP_PASSWORD}
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true

jwt:
  secret: ${JWT_SECRET:your-secret-key}
  expiration: 86400000 # 24 hours

file:
  upload-dir: ${UPLOAD_DIR:./uploads}

cors:
  allowed-origins: http://localhost:3000
```

## Database Setup

### 1. Create Database
```sql
CREATE DATABASE internhub;
USE internhub;
```

### 2. Run Schema
Execute the SQL script from `docs/database-schema.sql`

### 3. Insert Sample Data
```sql
-- Insert sample skills
INSERT INTO skills (name, category) VALUES
('JavaScript', 'Programming'),
('Python', 'Programming'),
('Java', 'Programming'),
('React', 'Frontend'),
('Node.js', 'Backend');

-- Insert admin user
INSERT INTO users (email, password, role, is_verified) VALUES
('admin@internhub.com', '$2b$10$encrypted_password', 'ADMIN', TRUE);
```

## Development Workflow

### 1. Frontend Development
```bash
# Start development server
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run linting
npm run lint

# Run type checking
npm run type-check
```

### 2. Backend Development
```bash
# Run Spring Boot application
./mvnw spring-boot:run

# Run tests
./mvnw test

# Build JAR
./mvnw clean package
```

## Testing

### Frontend Testing
```bash
# Install testing dependencies
npm install --save-dev @testing-library/react @testing-library/jest-dom jest

# Run tests
npm test
```

### Backend Testing
```bash
# Run unit tests
./mvnw test

# Run integration tests
./mvnw verify
```

## Deployment

### Frontend Deployment (Vercel)
1. Connect GitHub repository to Vercel
2. Set environment variables in Vercel dashboard
3. Deploy automatically on push to main branch

### Backend Deployment (AWS/Heroku)
1. Build JAR file: `./mvnw clean package`
2. Deploy to cloud platform
3. Configure environment variables
4. Set up database connection

### Database Deployment
1. Set up MySQL instance on cloud provider
2. Run migration scripts
3. Configure connection strings
4. Set up backups

## Security Considerations

### 1. Authentication
- JWT tokens with expiration
- Password hashing with bcrypt
- Role-based access control

### 2. Data Protection
- Input validation and sanitization
- SQL injection prevention
- XSS protection
- CORS configuration

### 3. File Upload Security
- File type validation
- Size limits
- Virus scanning
- Secure storage

## Performance Optimization

### 1. Frontend
- Code splitting
- Image optimization
- Caching strategies
- Bundle analysis

### 2. Backend
- Database indexing
- Query optimization
- Caching (Redis)
- Connection pooling

### 3. Database
- Proper indexing
- Query optimization
- Regular maintenance
- Monitoring

## Monitoring and Logging

### 1. Application Monitoring
- Error tracking (Sentry)
- Performance monitoring
- User analytics
- Uptime monitoring

### 2. Logging
- Structured logging
- Log aggregation
- Error alerting
- Audit trails

## Troubleshooting

### Common Issues

1. **Database Connection Error**
   - Check MySQL service status
   - Verify connection credentials
   - Ensure database exists

2. **JWT Token Issues**
   - Check token expiration
   - Verify secret key
   - Validate token format

3. **File Upload Problems**
   - Check file size limits
   - Verify upload directory permissions
   - Validate file types

4. **CORS Errors**
   - Configure allowed origins
   - Check request headers
   - Verify preflight requests

### Debug Commands
```bash
# Check database connection
mysql -u username -p -h localhost internhub

# View application logs
tail -f logs/application.log

# Check port usage
netstat -tulpn | grep :8080

# Test API endpoints
curl -X GET http://localhost:8080/api/v1/jobs
```

## Contributing

1. Fork the repository
2. Create feature branch
3. Make changes with tests
4. Submit pull request
5. Code review process

## Support

For technical support:
- Email: support@internhub.com
- Documentation: `/docs`
- Issue tracker: GitHub Issues
