 package com.hsbc.managementStudio.project;

import com.hsbc.managementStudio.project.model.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockitoAnnotations;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class ModelTest {

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    // Test for User Model
    @Test
    void testUserModel() {
        User mockUser = mock(User.class);

        when(mockUser.getUserId()).thenReturn(1L);
        when(mockUser.getUsername()).thenReturn("testUser");

        assertEquals(1L, mockUser.getUserId());
        assertEquals("testUser", mockUser.getUsername());

        mockUser.setUserId(2L);
        mockUser.setUsername("updatedUser");

        verify(mockUser).setUserId(2L);
        verify(mockUser).setUsername("updatedUser");
    }

    // Test for Role Model
    @Test
    void testRoleModel() {
        Role mockRole = mock(Role.class);

        when(mockRole.getRoleId()).thenReturn(1L);
        when(mockRole.getRoleName()).thenReturn("Admin");

        assertEquals(1L, mockRole.getRoleId());
        assertEquals("Admin", mockRole.getRoleName());

        mockRole.setRoleId(2L);
        mockRole.setRoleName("User");

        verify(mockRole).setRoleId(2L);
        verify(mockRole).setRoleName("User");
    }

    // Test for Project Model
    @Test
    void testProjectModel() {
        Project mockProject = mock(Project.class);

        when(mockProject.getProjectId()).thenReturn(1L);
        when(mockProject.getProjectName()).thenReturn("ProjectName");

        assertEquals(1L, mockProject.getProjectId());
        assertEquals("ProjectName", mockProject.getProjectName());

        mockProject.setProjectId(2L);
        mockProject.setProjectName("UpdatedProject");

        verify(mockProject).setProjectId(2L);
        verify(mockProject).setProjectName("UpdatedProject");
    }

    // Test for ProjectUserMapping Model
    @Test
    void testProjectUserMappingModel() {
        Project mockProject = mock(Project.class);
        User mockUser = mock(User.class);
        ProjectUserMapping mockMapping = mock(ProjectUserMapping.class);

        when(mockMapping.getMappingId()).thenReturn(1L);
        when(mockMapping.getProject()).thenReturn(mockProject);
        when(mockMapping.getUser()).thenReturn(mockUser);
        when(mockMapping.getRoleName()).thenReturn("Admin");

        assertEquals(1L, mockMapping.getMappingId());
        assertEquals(mockProject, mockMapping.getProject());
        assertEquals(mockUser, mockMapping.getUser());
        assertEquals("Admin", mockMapping.getRoleName());

        mockMapping.setMappingId(2L);
        mockMapping.setRoleName("User");

        verify(mockMapping).setMappingId(2L);
        verify(mockMapping).setRoleName("User");
    }
}
