package com.example.myapp.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;

@Service
public class DatabaseConnectionTestService {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public boolean testConnection() {
        try {
            Integer result = jdbcTemplate.queryForObject("SELECT 1", Integer.class);
            return result != null && result == 1;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
}
package com.example.myapp.controller;

import com.example.myapp.service.DatabaseConnectionTestService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DatabaseConnectionController {

    @Autowired
    private DatabaseConnectionTestService databaseConnectionTestService;

    @GetMapping("/test-connection")
    public String testDatabaseConnection() {
        boolean isConnected = databaseConnectionTestService.testConnection();
        return isConnected ? "Database connection successful!" : "Failed to connect to the database!";
    }
}
<dependencies>
    <!-- Spring Boot Starter for Core Application -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>

    <!-- Spring Boot Starter JDBC for Database Connectivity -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>

    <!-- PostgreSQL Driver for PostgreSQL Database -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.6.0</version>
    </dependency>

    <!-- Optional: Spring Boot Starter Data JPA for Repository Support -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Optional: Spring Boot Starter Web for REST Controller -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
