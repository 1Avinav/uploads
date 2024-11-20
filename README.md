package com.hsbc.managementStudio.service;

import com.hsbc.managementStudio.entity.Role;
import com.hsbc.managementStudio.repository.RoleRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class RoleService {

    @Autowired
    private RoleRepository roleRepository;

    public Role getRoleByName(String roleName) {
        return roleRepository.findByRoleName(roleName);
    }

    public Role saveRole(Role role) {
        return roleRepository.save(role);
    }
}


package com.hsbc.managementStudio.service;

import com.hsbc.managementStudio.entity.Project;
import com.hsbc.managementStudio.entity.User;
import com.hsbc.managementStudio.repository.ProjectRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProjectService {

    @Autowired
    private ProjectRepository projectRepository;

    public List<Project> getAllProjects() {
        return projectRepository.findAll();
    }

    public Project getProjectById(Long projectId) {
        return projectRepository.findById(projectId).orElse(null);
    }

    public boolean existsByProjectCode(String projectCode) {
        return projectRepository.existsByProjectCode(projectCode);
    }

    public Project saveProject(Project project) {
        return projectRepository.save(project);
    }

    public void deleteProject(Long projectId) {
        projectRepository.deleteById(projectId);
    }
}


package com.hsbc.managementStudio.service;

import com.hsbc.managementStudio.entity.User;
import com.hsbc.managementStudio.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public User getUserById(Long userId) {
        return userRepository.findById(userId).orElse(null);
    }

    public User getUserByEmail(String email) {
        return userRepository.findByEmail(email);
    }

    public User saveUser(User user) {
        return userRepository.save(user);
    }

    public void deleteUser(Long userId) {
        userRepository.deleteById(userId);
    }
}


package com.hsbc.managementStudio.service;

import com.hsbc.managementStudio.entity.Project;
import com.hsbc.managementStudio.entity.ProjectUserMapping;
import com.hsbc.managementStudio.entity.User;
import com.hsbc.managementStudio.repository.ProjectUserMappingRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProjectUserMappingService {

    @Autowired
    private ProjectUserMappingRepository projectUserMappingRepository;

    public List<ProjectUserMapping> getUsersByProject(Project project) {
        return projectUserMappingRepository.findByProject(project);
    }

    public List<ProjectUserMapping> getProjectsByUser(User user) {
        return projectUserMappingRepository.findByUser(user);
    }

    public boolean isUserMappedToProject(Project project, User user) {
        return projectUserMappingRepository.existsByProjectAndUser(project, user);
    }

    public ProjectUserMapping saveMapping(ProjectUserMapping mapping) {
        return projectUserMappingRepository.save(mapping);
    }

    public void deleteMapping(Long mappingId) {
        projectUserMappingRepository.deleteById(mappingId);
    }
}
