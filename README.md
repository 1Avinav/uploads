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

package com.example.myapp.model;

import java.util.List;

public class User {

    private Long userId;
    private List<String> groupList;

    // Constructors, getters, and setters
    public User() {}

    public User(Long userId, List<String> groupList) {
        this.userId = userId;
        this.groupList = groupList;
    }

    public Long getUserId() {
        return userId;
    }

    public void setUserId(Long userId) {
        this.userId = userId;
    }

    public List<String> getGroupList() {
        return groupList;
    }

    public void setGroupList(List<String> groupList) {
        this.groupList = groupList;
    }
}
package com.example.myapp.service;

import com.example.myapp.model.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Service;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Arrays;
import java.util.List;

@Service
public class UserService {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public List<User> getAllUsers() {
        String sql = "SELECT user_id, group_list FROM x.user"; // Schema 'x' and table 'user'
        
        return jdbcTemplate.query(sql, new RowMapper<User>() {
            @Override
            public User mapRow(ResultSet rs, int rowNum) throws SQLException {
                Long userId = rs.getLong("user_id");
                String groupListStr = rs.getString("group_list");
                
                // Convert group_list from a comma-separated string to a List<String>
                List<String> groupList = Arrays.asList(groupListStr.split(","));
                
                return new User(userId, groupList);
            }
        });
    }
}
package com.example.myapp.controller;

import com.example.myapp.model.User;
import com.example.myapp.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/users")
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }
}

