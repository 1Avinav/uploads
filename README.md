package com.hsbc.managementStudio.model;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "users", schema = "test")
public class User {

    @Id
    private String userId; // Unique identifier for the user (e.g., sub from JWT)

    private String userName;
    private String email;
    private String role;
    private String profilePicUrl;

    // Getters and setters

    public String getUserId() {
        return userId;
    }

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getRole() {
        return role;
    }

    public void setRole(String role) {
        this.role = role;
    }

    public String getProfilePicUrl() {
        return profilePicUrl;
    }

    public void setProfilePicUrl(String profilePicUrl) {
        this.profilePicUrl = profilePicUrl;
    }
}


package com.hsbc.managementStudio.util;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import org.springframework.stereotype.Component;

@Component
public class JwtUtil {

    private String secretKey = "your-secret-key"; // Ideally, this should be in application.properties or environment variables

    public Claims extractClaims(String token) {
        return Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(token)
                .getBody();
    }

    public String extractUserId(String token) {
        return extractClaims(token).getSubject();
    }

    public boolean isTokenExpired(String token) {
        return extractClaims(token).getExpiration().before(new java.util.Date());
    }

    public boolean validateToken(String token, String userId) {
        return (userId.equals(extractUserId(token)) && !isTokenExpired(token));
    }
}


package com.hsbc.managementStudio.service;

import com.hsbc.managementStudio.model.User;
import com.hsbc.managementStudio.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Optional;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Get user by ID
    public Optional<User> getUserById(String userId) {
        return userRepository.findById(userId);
    }

    // Update user details (admin can update any user, others can only update themselves)
    public User updateUser(User updatedUser, String loggedInUserId) {
        if (updatedUser.getUserId().equals(loggedInUserId) || "admin".equals(loggedInUserId)) {
            return userRepository.save(updatedUser);
        }
        throw new IllegalArgumentException("You are not authorized to update this user");
    }
}


package com.hsbc.managementStudio.repository;

import com.hsbc.managementStudio.model.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, String> {
    Optional<User> findById(String userId);
}




package com.hsbc.managementStudio.controller;

import com.hsbc.managementStudio.service.UserService;
import com.hsbc.managementStudio.util.JwtUtil;
import com.hsbc.managementStudio.model.User;
import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class JwtController {

    @Autowired
    private JwtUtil jwtUtil;

    @Autowired
    private UserService userService;

    // Endpoint to validate JWT and extract user info
    @PostMapping("/sso-login")
    public ResponseEntity<?> ssoLogin(@RequestHeader("Authorization") String token) {
        // Remove Bearer prefix
        String jwtToken = token.substring(7);
        
        if (jwtUtil.isTokenExpired(jwtToken)) {
            return ResponseEntity.status(401).body("Token is expired");
        }

        String userId = jwtUtil.extractUserId(jwtToken);
        // Fetch user details from DB
        User user = userService.getUserById(userId).orElse(null);

        if (user == null) {
            return ResponseEntity.status(404).body("User not found");
        }

        return ResponseEntity.ok(user); // Send back user info including role
    }
}


package com.hsbc.managementStudio.controller;

import com.hsbc.managementStudio.model.Project;
import com.hsbc.managementStudio.service.ProjectService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/projects")
public class ProjectController {

    @Autowired
    private ProjectService projectService;

    // Admin only - Create a project
    @PostMapping
    public ResponseEntity<?> createProject(@RequestBody Project project, @RequestHeader("Authorization") String token) {
        if ("admin".equals(getRoleFromToken(token))) {
            Project createdProject = projectService.createProject(project);
            return ResponseEntity.ok(createdProject);
        } else {
            return ResponseEntity.status(403).body("Access Denied: Admins only");
        }
    }

    // Get project details (no role restriction in this case)
    @GetMapping("/{projectId}")
    public ResponseEntity<?> getProjectDetails(@PathVariable String projectId) {
        Project project = projectService.getProjectDetails(projectId);
        return ResponseEntity.ok(project);
    }

    private String getRoleFromToken(String token) {
        String userId = jwtUtil.extractUserId(token.substring(7));  // Remove Bearer prefix
        User user = userService.getUserById(userId).orElse(null);
        return (user != null) ? user.getRole() : "unknown";
    }
}

package com.hsbc.managementStudio.model;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "projects", schema = "test")
public class Project {

    @Id
    private String projectId;

    private String projectName;
    private String description;
    private String creator;
    private String createdDateTime;
    private String apiVersion;
    private String status;
    private String documentType;
    private String projectCode;
    private String dataSensitivityLevel;
    private String documentSource;

    // Getters and setters for all fields
}


package com.hsbc.managementStudio.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and()
            .csrf().disable()
            .authorizeRequests()
            .antMatchers("/api/sso-login").permitAll() // Allow SSO login without authentication
            .antMatchers("/api/projects/**").hasRole("ADMIN") // Only Admin can access project endpoints
            .anyRequest().authenticated(); // All other requests require authentication
    }
}
# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/test
spring.datasource.username=your-db-username
spring.datasource.password=your-db-password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# CORS Configuration
spring.webflux.cors.allowed-origins=http://frontend1.com,http://frontend2.com
spring.webflux.cors.allowed-methods=GET,POST,PUT,DELETE,OPTIONS
spring.webflux.cors.allowed-headers=*

# JWT Secret (should be in environment or secrets manager)
jwt.secret-key=your-secret-key


 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <!-- JWT (JSON Web Token) for securing APIs -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.11.5</version>
        </dependency>

        <!-- Spring Boot Starter Validation (For Bean Validation) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <!-- Spring Boot Starter for CORS (Cross-Origin Resource Sharing) configuration -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
        </dependency>
