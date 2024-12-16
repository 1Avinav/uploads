@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY)
@ActiveProfiles("test")  // To use application-test.properties
public class ProjectControllerIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;  // Inject TestRestTemplate for making HTTP requests

    @Autowired
    private ProjectRepository projectRepository;  // Inject ProjectRepository for interacting with the DB

    @MockBean
    private JwtUtil jwtUtil;  // Mock JwtUtil for JWT-related operations

    @MockBean
    private UserService userService;  // Mock UserService for user-related logic

    @BeforeEach
    void setUp() {
        // Clear the database before each test
        projectRepository.deleteAll();
    }

    // Test for creating a project
    @Test
    public void shouldCreateProject() {
        // Create a new Project instance
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

        // Mock JWT and UserService
        Mockito.when(jwtUtil.extractSub(Mockito.anyString())).thenReturn("admin");
        Mockito.when(userService.isAdmin("admin")).thenReturn(true);

        // Perform the request to create the project
        ResponseEntity<String> response = restTemplate.postForEntity("/projects", project, String.class);

        // Assert the response and check that the project is created
        Assertions.assertEquals(HttpStatus.CREATED, response.getStatusCode());
        List<Project> projects = projectRepository.findAll();
        Assertions.assertEquals(1, projects.size());
        Assertions.assertEquals("Project Test", projects.get(0).getProjectName());
    }

    // Test for fetching all projects
    @Test
    public void shouldFetchAllProjects() {
        // Create and save a project in the DB
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
        projectRepository.save(project);

        // Perform the request to fetch all projects
        ResponseEntity<Project[]> response = restTemplate.getForEntity("/projects", Project[].class);

        // Assert the response and the project count
        Assertions.assertEquals(HttpStatus.OK, response.getStatusCode());
        Assertions.assertTrue(response.getBody().length > 0);
    }

    // Test for updating a project
    @Test
    public void shouldUpdateProject() {
        // Create and save a project
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
        projectRepository.save(project);

        // Modify the project details
        project.setProjectName("Updated Project Test");

        // Mock JWT and UserService
        Mockito.when(jwtUtil.extractSub(Mockito.anyString())).thenReturn("admin");
        Mockito.when(userService.isAdmin("admin")).thenReturn(true);

        // Perform the request to update the project
        restTemplate.put("/projects/{id}", project, project.getProjectId());

        // Assert the updated project name
        Project updatedProject = projectRepository.findById(project.getProjectId()).orElseThrow();
        Assertions.assertEquals("Updated Project Test", updatedProject.getProjectName());
    }

    // Test for deleting a project
    @Test
    public void shouldDeleteProject() {
        // Create and save a project
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
        projectRepository.save(project);

        // Mock JWT and UserService
        Mockito.when(jwtUtil.extractSub(Mockito.anyString())).thenReturn("admin");
        Mockito.when(userService.isAdmin("admin")).thenReturn(true);

        // Perform the request to delete the project
        restTemplate.delete("/projects/{id}", project.getProjectId());

        // Assert that the project is deleted
        Optional<Project> deletedProject = projectRepository.findById(project.getProjectId());
        Assertions.assertFalse(deletedProject.isPresent());
    }

}
@BeforeEach
void setUp() {
    System.out.println("JwtUtil is: " + jwtUtil);
    System.out.println("ProjectRepository is: " + projectRepository);
    projectRepository.deleteAll();
}
