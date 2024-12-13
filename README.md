package com.hsbc.managementStudio.project.model;

import org.junit.jupiter.api.Test;

import java.time.LocalDateTime;

import static org.junit.jupiter.api.Assertions.assertEquals;

class ProjectTest {

    @Test
    void testProjectFields() {
        // Arrange
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
        project.setCreatedDateTime(LocalDateTime.now());

        // Assert
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

package com.hsbc.managementStudio.project.controller;

import com.hsbc.managementStudio.project.model.Project;
import com.hsbc.managementStudio.project.service.ProjectService;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.http.ResponseEntity;

import java.time.LocalDateTime;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

class ProjectControllerTest {

    @InjectMocks
    private ProjectController controller;

    @Mock
    private ProjectService service;

    @Test
    void testCreateProject() {
        // Arrange
        Project project = new Project();
        project.setProjectId(1L);
        project.setProjectName("Test Project");
        project.setCreatedDateTime(LocalDateTime.now());
        when(service.createProject(any(Project.class))).thenReturn(project);

        // Act
        ResponseEntity<Project> response = controller.createProject(project);

        // Assert
        assertEquals("Test Project", response.getBody().getProjectName());
        verify(service, times(1)).createProject(project);
    }
}
package com.hsbc.managementStudio.project.controller;

import com.hsbc.managementStudio.project.model.ProjectUserMapping;
import com.hsbc.managementStudio.project.service.ProjectService;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

class ProjectUserMappingControllerTest {

    @InjectMocks
    private ProjectUserMappingController controller;

    @Mock
    private ProjectService service;

    @Test
    void testCreateMapping() {
        // Arrange
        ProjectUserMapping mapping = new ProjectUserMapping();
        mapping.setUserId(1L);
        mapping.setProjectId(1L);
        when(service.createMapping(any(ProjectUserMapping.class))).thenReturn(mapping);

        // Act
        ResponseEntity<ProjectUserMapping> response = controller.createMapping(mapping);

        // Assert
        assertEquals(1L, response.getBody().getProjectId());
        verify(service, times(1)).createMapping(mapping);
    }
}
package com.hsbc.managementStudio.project.service;

import com.hsbc.managementStudio.project.model.Project;
import com.hsbc.managementStudio.project.repository.ProjectRepository;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

class ProjectServiceTest {

    @InjectMocks
    private ProjectService service;

    @Mock
    private ProjectRepository repository;

    @Test
    void testCreateProject() {
        // Arrange
        Project project = new Project();
        project.setProjectName("Test Project");
        when(repository.save(any(Project.class))).thenReturn(project);

        // Act
        Project result = service.createProject(project);

        // Assert
        assertEquals("Test Project", result.getProjectName());
        verify(repository, times(1)).save(project);
    }
}
