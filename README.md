package com.hsbc.managementStudio.project.controller;

import com.hsbc.managementStudio.project.model.Project;
import com.hsbc.managementStudio.project.service.ProjectService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class ProjectControllerTest {

    @Mock
    private ProjectService projectService;

    @InjectMocks
    private ProjectController projectController;

    private Project project;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Initialize project object with sample data
        project = new Project();
        project.setProjectId(1L);
        project.setApiVersion("v1");
        project.setCreator("John Doe");
        project.setDataSensitivityLevel("High");
        project.setDescription("Test project description");
        project.setDocumentSource("Internal");
        project.setDocumentType("PDF");
        project.setProjectCode("PRJ001");
        project.setStatus("Active");
        project.setProjectName("Project Test");
        project.setCreatedDateTime(LocalDateTime.now());
    }

    @Test
    void testCreateProject() {
        String authHeader = "Bearer token"; // Mocked authorization header

        // Mocking the service layer
        when(projectService.createProject(any(Project.class), eq(authHeader)))
                .thenReturn(project);

        // Test the controller method
        ResponseEntity<Project> response = projectController.createProject(authHeader, project);

        // Assert the response status and body
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(project, response.getBody());

        // Verify if the service method was called once
        verify(projectService, times(1)).createProject(any(Project.class), eq(authHeader));
    }

    // Additional test cases can be added for other CRUD operations
}


package com.hsbc.managementStudio.project.controller;

import com.hsbc.managementStudio.project.model.ProjectUserMapping;
import com.hsbc.managementStudio.project.service.ProjectService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class ProjectUserMappingControllerTest {

    @Mock
    private ProjectService projectService;

    @InjectMocks
    private ProjectUserMappingController projectUserMappingController;

    private ProjectUserMapping projectUserMapping;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Initialize project-user mapping object with sample data
        projectUserMapping = new ProjectUserMapping();
        projectUserMapping.setMappingId(1L);
        projectUserMapping.setProjectId(1L);
        projectUserMapping.setUserId(101L);
        projectUserMapping.setRole("Admin");
    }

    @Test
    void testCreateProjectUserMapping() {
        String authHeader = "Bearer token"; // Mocked authorization header

        // Mocking the service layer
        when(projectService.createProjectUserMapping(any(ProjectUserMapping.class), eq(authHeader)))
                .thenReturn(projectUserMapping);

        // Test the controller method
        ResponseEntity<ProjectUserMapping> response = projectUserMappingController.createProjectUserMapping(authHeader, projectUserMapping);

        // Assert the response status and body
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(projectUserMapping, response.getBody());

        // Verify if the service method was called once
        verify(projectService, times(1)).createProjectUserMapping(any(ProjectUserMapping.class), eq(authHeader));
    }

    // Additional test cases can be added for other CRUD operations
}


package com.hsbc.managementStudio.project.service;

import com.hsbc.managementStudio.project.model.Project;
import com.hsbc.managementStudio.project.model.ProjectUserMapping;
import com.hsbc.managementStudio.project.repository.ProjectRepository;
import com.hsbc.managementStudio.project.repository.ProjectUserMappingRepository;
import com.hsbc.managementStudio.user.service.UserService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.time.LocalDateTime;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class ProjectServiceTest {

    @Mock
    private ProjectRepository projectRepository;

    @Mock
    private ProjectUserMappingRepository projectUserMappingRepository;

    @Mock
    private UserService userService;

    @InjectMocks
    private ProjectService projectService;

    private Project project;
    private ProjectUserMapping projectUserMapping;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Initialize project and project user mapping
        project = new Project();
        project.setProjectId(1L);
        project.setApiVersion("v1");
        project.setCreator("John Doe");
        project.setDataSensitivityLevel("High");
        project.setDescription("Test project description");
        project.setDocumentSource("Internal");
        project.setDocumentType("PDF");
        project.setProjectCode("PRJ001");
        project.setStatus("Active");
        project.setProjectName("Project Test");
        project.setCreatedDateTime(LocalDateTime.now());

        projectUserMapping = new ProjectUserMapping();
        projectUserMapping.setMappingId(1L);
        projectUserMapping.setProjectId(1L);
        projectUserMapping.setUserId(101L);
        projectUserMapping.setRole("Admin");
    }

    @Test
    void testCreateProject() {
        // Mock the repository save operation
        when(projectRepository.save(any(Project.class))).thenReturn(project);

        // Call the service method
        Project createdProject = projectService.createProject(project, "Bearer token");

        // Assert the returned project
        assertEquals(project, createdProject);

        // Verify repository method was called once
        verify(projectRepository, times(1)).save(any(Project.class));
    }

    @Test
    void testCreateProjectUserMapping() {
        // Mock the repository save operation
        when(projectUserMappingRepository.save(any(ProjectUserMapping.class))).thenReturn(projectUserMapping);

        // Call the service method
        ProjectUserMapping createdMapping = projectService.createProjectUserMapping(projectUserMapping, "Bearer token");

        // Assert the returned project-user mapping
        assertEquals(projectUserMapping, createdMapping);

        // Verify repository method was called once
        verify(projectUserMappingRepository, times(1)).save(any(ProjectUserMapping.class));
    }

    // More tests for other methods can be added here
}


package com.hsbc.managementStudio.project.model;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class ProjectTest {

    @Test
    void testProjectModel() {
        Project project = new Project();
        project.setProjectId(1L);
        project.setApiVersion("v1");
        project.setCreator("John Doe");
        project.setDataSensitivityLevel("High");
        project.setDescription("Test project description");
        project.setDocumentSource("Internal");
        project.setDocumentType("PDF");
        project.setProjectCode("PRJ001");
        project.setStatus("Active");
        project.setProjectName("Project Test");

        // Test getters and setters
        assertEquals(1L, project.getProjectId());
        assertEquals("v1", project.getApiVersion());
        assertEquals("John Doe", project.getCreator());
        assertEquals("High", project.getDataSensitivityLevel());
        assertEquals("Test project description", project.getDescription());
        assertEquals("Internal", project.getDocumentSource());
        assertEquals("PDF", project.getDocumentType());
        assertEquals("PRJ001", project.getProjectCode());
        assertEquals("Active", project.getStatus());
        assertEquals("Project Test", project.getProjectName());
    }
}


package com.hsbc.managementStudio.util;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class JwtUtilTest {

    @Test
    void testExtractSubFromToken() {
        String token = "Bearer sample.jwt.token";  // Mocked JWT token

        JwtUtil jwtUtil = new JwtUtil();
        String sub = jwtUtil.extractSub(token);

        // Assert that the sub is extracted properly from the JWT token
        assertEquals("sample", sub);
    }

    // More JWT utility methods can be tested here
}


package com.hsbc.managementStudio.project.controller;

import com.hsbc.managementStudio.project.model.ProjectUserMapping;
import com.hsbc.managementStudio.project.service.ProjectUserMappingService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class ProjectUserMappingControllerTest {

    @Mock
    private ProjectUserMappingService projectUserMappingService;

    @InjectMocks
    private ProjectUserMappingController projectUserMappingController;

    private ProjectUserMapping projectUserMapping;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Initialize project-user mapping object with sample data
        projectUserMapping = new ProjectUserMapping();
        projectUserMapping.setMappingId(1L);
        projectUserMapping.setProjectId(1L);
        projectUserMapping.setUserId(101L);
        projectUserMapping.setRole("Admin");
    }

    @Test
    void testCreateProjectUserMapping() {
        String authHeader = "Bearer token"; // Mocked authorization header

        // Mocking the service layer
        when(projectUserMappingService.createProjectUserMapping(any(ProjectUserMapping.class), eq(authHeader)))
                .thenReturn(projectUserMapping);

        // Test the controller method
        ResponseEntity<ProjectUserMapping> response = projectUserMappingController.createProjectUserMapping(authHeader, projectUserMapping);

        // Assert the response status and body
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(projectUserMapping, response.getBody());

        // Verify if the service method was called once
        verify(projectUserMappingService, times(1)).createProjectUserMapping(any(ProjectUserMapping.class), eq(authHeader));
    }

    @Test
    void testGetProjectUserMappingById() {
        // Mocking the service layer
        when(projectUserMappingService.getProjectUserMappingById(1L))
                .thenReturn(projectUserMapping);

        // Test the controller method
        ResponseEntity<ProjectUserMapping> response = projectUserMappingController.getProjectUserMappingById(1L);

        // Assert the response status and body
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(projectUserMapping, response.getBody());

        // Verify if the service method was called once
        verify(projectUserMappingService, times(1)).getProjectUserMappingById(1L);
    }

    @Test
    void testUpdateProjectUserMapping() {
        String authHeader = "Bearer token"; // Mocked authorization header

        // Modify the project-user mapping for update
        projectUserMapping.setRole("Manager");

        // Mocking the service layer
        when(projectUserMappingService.updateProjectUserMapping(any(ProjectUserMapping.class), eq(authHeader)))
                .thenReturn(projectUserMapping);

        // Test the controller method
        ResponseEntity<ProjectUserMapping> response = projectUserMappingController.updateProjectUserMapping(authHeader, projectUserMapping);

        // Assert the response status and body
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(projectUserMapping, response.getBody());

        // Verify if the service method was called once
        verify(projectUserMappingService, times(1)).updateProjectUserMapping(any(ProjectUserMapping.class), eq(authHeader));
    }

    @Test
    void testDeleteProjectUserMapping() {
        // Mocking the service layer
        doNothing().when(projectUserMappingService).deleteProjectUserMapping(1L);

        // Test the controller method
        ResponseEntity<Void> response = projectUserMappingController.deleteProjectUserMapping(1L);

        // Assert the response status
        assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());

        // Verify if the service method was called once
        verify(projectUserMappingService, times(1)).deleteProjectUserMapping(1L);
    }
}

package com.hsbc.managementStudio.project.service;

import com.hsbc.managementStudio.project.model.ProjectUserMapping;
import com.hsbc.managementStudio.project.repository.ProjectUserMappingRepository;
import com.hsbc.managementStudio.project.model.Project;
import com.hsbc.managementStudio.user.service.UserService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class ProjectUserMappingServiceTest {

    @Mock
    private ProjectUserMappingRepository projectUserMappingRepository;

    @Mock
    private UserService userService;

    @InjectMocks
    private ProjectUserMappingService projectUserMappingService;

    private ProjectUserMapping projectUserMapping;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Initialize project-user mapping object with sample data
        projectUserMapping = new ProjectUserMapping();
        projectUserMapping.setMappingId(1L);
        projectUserMapping.setProjectId(1L);
        projectUserMapping.setUserId(101L);
        projectUserMapping.setRole("Admin");
    }

    @Test
    void testCreateProjectUserMapping() {
        // Mocking the repository save operation
        when(projectUserMappingRepository.save(any(ProjectUserMapping.class))).thenReturn(projectUserMapping);

        // Call the service method
        ProjectUserMapping createdMapping = projectUserMappingService.createProjectUserMapping(projectUserMapping, "Bearer token");

        // Assert the returned project-user mapping
        assertEquals(projectUserMapping, createdMapping);

        // Verify repository method was called once
        verify(projectUserMappingRepository, times(1)).save(any(ProjectUserMapping.class));
    }

    @Test
    void testGetProjectUserMappingById() {
        // Mocking the repository findById operation
        when(projectUserMappingRepository.findById(1L)).thenReturn(java.util.Optional.of(projectUserMapping));

        // Call the service method
        ProjectUserMapping mapping = projectUserMappingService.getProjectUserMappingById(1L);

        // Assert the returned project-user mapping
        assertEquals(projectUserMapping, mapping);

        // Verify repository method was called once
        verify(projectUserMappingRepository, times(1)).findById(1L);
    }

    @Test
    void testUpdateProjectUserMapping() {
        // Mocking the repository save operation for update
        when(projectUserMappingRepository.save(any(ProjectUserMapping.class))).thenReturn(projectUserMapping);

        // Modify the project-user mapping for update
        projectUserMapping.setRole("Manager");

        // Call the service method
        ProjectUserMapping updatedMapping = projectUserMappingService.updateProjectUserMapping(projectUserMapping, "Bearer token");

        // Assert the returned updated project-user mapping
        assertEquals(projectUserMapping, updatedMapping);

        // Verify repository method was called once
        verify(projectUserMappingRepository, times(1)).save(any(ProjectUserMapping.class));
    }

    @Test
    void testDeleteProjectUserMapping() {
        // Mocking the repository delete operation
        doNothing().when(projectUserMappingRepository).deleteById(1L);

        // Call the service method
        projectUserMappingService.deleteProjectUserMapping(1L);

        // Verify repository method was called once
        verify(projectUserMappingRepository, times(1)).deleteById(1L);
    }
}

package com.hsbc.managementStudio.project.model;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class ProjectUserMappingTest {

    @Test
    void testProjectUserMappingModel() {
        ProjectUserMapping mapping = new ProjectUserMapping();
        mapping.setMappingId(1L);
        mapping.setProjectId(1L);
        mapping.setUserId(101L);
        mapping.setRole("Admin");

        // Test getters and setters
        assertEquals(1L, mapping.getMappingId());
        assertEquals(1L, mapping.getProjectId());
        assertEquals(101L, mapping.getUserId());
        assertEquals("Admin", mapping.getRole());
    }
}
