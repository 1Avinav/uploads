@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY)
@ActiveProfiles("test")
public class ProjectControllerIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProjectRepository projectRepository;

    @MockBean
    private JwtUtil jwtUtil;

    @MockBean
    private UserService userService;

    private static final String ADMIN_TOKEN = "Bearer adminToken";

    @BeforeEach
    void setUp() {
        projectRepository.deleteAll();  // Clean up the DB before each test
    }

    // Test for creating a project
    @Test
    public void shouldCreateProject() {
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

        Mockito.when(jwtUtil.extractSub(Mockito.anyString())).thenReturn("admin");
        Mockito.when(userService.isAdmin("admin")).thenReturn(true);

        ResponseEntity<String> response = restTemplate.postForEntity("/projects", project, String.class);

        Assertions.assertEquals(HttpStatus.CREATED, response.getStatusCode());
        List<Project> projects = projectRepository.findAll();
        Assertions.assertEquals(1, projects.size());
        Assertions.assertEquals("Project Test", projects.get(0).getProjectName());
    }

    // Test for getting a project by ID
    @Test
    public void shouldGetProjectById() {
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

        ResponseEntity<Project> response = restTemplate.getForEntity("/projects/" + project.getProjectId(), Project.class);

        Assertions.assertEquals(HttpStatus.OK, response.getStatusCode());
        Assertions.assertNotNull(response.getBody());
        Assertions.assertEquals("Project Test", response.getBody().getProjectName());
    }

    // Test for getting all projects
    @Test
    public void shouldGetAllProjects() {
        Project project1 = new Project();
        project1.setProjectId(1L);
        project1.setApiVersion("v1");
        project1.setCreator("John Doe");
        project1.setDataSensitivityLevel("High");
        project1.setDescription("Test project description");
        project1.setDocumentSource("Internal");
        project1.setDocumentType("PDF");
        project1.setProjectCode("PRJ001");
        project1.setStatus("Active");
        project1.setProjectName("Project Test 1");
        projectRepository.save(project1);

        Project project2 = new Project();
        project2.setProjectId(2L);
        project2.setApiVersion("v1");
        project2.setCreator("Jane Doe");
        project2.setDataSensitivityLevel("Low");
        project2.setDescription("Another project description");
        project2.setDocumentSource("External");
        project2.setDocumentType("Excel");
        project2.setProjectCode("PRJ002");
        project2.setStatus("Inactive");
        project2.setProjectName("Project Test 2");
        projectRepository.save(project2);

        ResponseEntity<List<Project>> response = restTemplate.exchange(
                "/projects", HttpMethod.GET, null,
                new ParameterizedTypeReference<List<Project>>() {}
        );

        Assertions.assertEquals(HttpStatus.OK, response.getStatusCode());
        Assertions.assertEquals(2, response.getBody().size());
    }

    // Test for updating a project
    @Test
    public void shouldUpdateProject() {
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
        project.setProjectName("Old Project Name");
        projectRepository.save(project);

        project.setProjectName("Updated Project Name");

        HttpEntity<Project> entity = new HttpEntity<>(project);
        ResponseEntity<String> response = restTemplate.exchange(
                "/projects/" + project.getProjectId(),
                HttpMethod.PUT, entity, String.class
        );

        Assertions.assertEquals(HttpStatus.OK, response.getStatusCode());
        Project updatedProject = projectRepository.findById(project.getProjectId()).orElse(null);
        Assertions.assertNotNull(updatedProject);
        Assertions.assertEquals("Updated Project Name", updatedProject.getProjectName());
    }

    // Test for deleting a project
    @Test
    public void shouldDeleteProject() {
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

        ResponseEntity<String> response = restTemplate.exchange(
                "/projects/" + project.getProjectId(),
                HttpMethod.DELETE, null, String.class
        );

        Assertions.assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());
        Optional<Project> deletedProject = projectRepository.findById(project.getProjectId());
        Assertions.assertTrue(deletedProject.isEmpty());
    }
}


# H2 Database Config (For Testing)
spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;MODE=PostgreSQL
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password

# JPA Config for Testing
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect

# H2 Console (Optional)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console


