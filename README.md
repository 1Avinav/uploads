package com.example.usermanagement.entity;

import javax.persistence.*;
import java.util.Arrays;

@Entity
@Table(name = "users", schema = "your_schema_name") // Adjust schema name as needed
public class User {

    @Id
    @Column(name = "user_id")
    private String userId;

    @Column(name = "group_list", columnDefinition = "text[]")
    private String[] groupList;

    public User() {}

    public User(String userId, String[] groupList) {
        this.userId = userId;
        this.groupList = groupList;
    }

    public String getUserId() {
        return userId;
    }

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public String[] getGroupList() {
        return groupList;
    }

    public void setGroupList(String[] groupList) {
        this.groupList = groupList;
    }

    @Override
    public String toString() {
        return "User{" +
                "userId='" + userId + '\'' +
                ", groupList=" + Arrays.toString(groupList) +
                '}';
    }
}
package com.example.usermanagement.repository;

import com.example.usermanagement.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, String> {
}
package com.example.usermanagement.service;

import com.example.usermanagement.entity.User;
import com.example.usermanagement.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Optional;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User getUser(String userId) {
        return userRepository.findById(userId).orElse(null);
    }

    public User addUser(User user) {
        return userRepository.save(user);
    }

    public User checkAndAddUser(String userId, String[] groupList) {
        Optional<User> existingUser = userRepository.findById(userId);
        if (existingUser.isPresent()) {
            return existingUser.get();
        } else {
            User newUser = new User(userId, groupList);
            return userRepository.save(newUser);
        }
    }
}
package com.example.usermanagement.controller;

import com.example.usermanagement.entity.User;
import com.example.usermanagement.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    // Get user by ID
    @GetMapping("/{userId}")
    public User getUser(@PathVariable String userId) {
        return userService.getUser(userId);
    }

    // Add new user if not exists
    @PostMapping
    public User checkAndAddUser(@RequestParam String userId, @RequestParam String[] groupList) {
        return userService.checkAndAddUser(userId, groupList);
    }
}
