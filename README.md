import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.yourpackage.controller.ProjectController;
import com.yourpackage.model.Project;
import com.yourpackage.service.UserService;
import com.yourpackage.util.JwtUtil;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import java.time.LocalDateTime;

@WebMvcTest(ProjectController.class)
public class ProjectControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @MockBean
    private JwtUtil jwtUtil;

    private ObjectMapper objectMapper;

    private static final String VALID_TOKEN = "Bearer valid.jwt.token";
    private static final String INVALID_TOKEN = "Bearer invalid.jwt.token";

    @BeforeEach
    public void setUp() {
        objectMapper = new ObjectMapper();
    }

    @Test
    public void testCreateProjectWithValidTokenAndAdminRole() throws Exception {
        // Mock JWT extraction and user role validation
        when(jwtUtil.extractSub("valid.jwt.token")).thenReturn("adminUser");
        when(userService.isAdmin("adminUser")).thenReturn(true);

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

        mockMvc.perform(post("/projects")
                .header("Authorization", VALID_TOKEN)
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(project)))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.projectName").value("Project Test"))
                .andExpect(jsonPath("$.status").value("Active"));

        verify(jwtUtil, times(1)).extractSub("valid.jwt.token");
        verify(userService, times(1)).isAdmin("adminUser");
    }

    @Test
    public void testCreateProjectWithInvalidToken() throws Exception {
        // Mock JWT extraction failure
        when(jwtUtil.extractSub("invalid.jwt.token")).thenThrow(new RuntimeException("Invalid Token"));

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

        mockMvc.perform(post("/projects")
                .header("Authorization", INVALID_TOKEN)
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(project)))
                .andExpect(status().isUnauthorized());

        verify(jwtUtil, times(1)).extractSub("invalid.jwt.token");
        verify(userService, never()).isAdmin(anyString());
    }

    @Test
    public void testCreateProjectWithNonAdminRole() throws Exception {
        // Mock JWT extraction and non-admin role
        when(jwtUtil.extractSub("valid.jwt.token")).thenReturn("regularUser");
        when(userService.isAdmin("regularUser")).thenReturn(false);

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

        mockMvc.perform(post("/projects")
                .header("Authorization", VALID_TOKEN)
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(project)))
                .andExpect(status().isForbidden());

        verify(jwtUtil, times(1)).extractSub("valid.jwt.token");
        verify(userService, times(1)).isAdmin("regularUser");
    }
}
