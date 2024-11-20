@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long projectId;

    private String apiVersion;
    private String description;
    private LocalDateTime startDate;
    private LocalDateTime createdDateTime;
    private String status;
    private String documentType;
    private String documentSource;
    private String projectCode;
    private String projectName;

    @OneToMany(mappedBy = "project", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ProjectUserMapping> projectUserMappings;

     @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ProjectUserMapping> projectUserMappings;


@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long mappingId;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "project_id", nullable = false)
    private Project project;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "role_id", nullable = false)
    private Role role;


    -- Create Project Table
CREATE TABLE project (
    project_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    api_version VARCHAR(255),
    description TEXT,
    start_date TIMESTAMP,
    created_date_time TIMESTAMP,
    status VARCHAR(255),
    document_type VARCHAR(255),
    document_source VARCHAR(255),
    project_code VARCHAR(255),
    project_name VARCHAR(255)
);

-- Create User Table
CREATE TABLE user (
    user_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    user_name VARCHAR(255),
    email VARCHAR(255) UNIQUE,
    profile_url VARCHAR(255)
);

-- Create Role Table
CREATE TABLE role (
    role_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    role_name VARCHAR(255) UNIQUE
);

-- Create ProjectUserMapping Table
CREATE TABLE project_user_mapping (
    mapping_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    project_id BIGINT NOT NULL,
    user_id BIGINT NOT NULL,
    role_id BIGINT NOT NULL,
    FOREIGN KEY (project_id) REFERENCES project(project_id),
    FOREIGN KEY (user_id) REFERENCES user(user_id),
    FOREIGN KEY (role_id) REFERENCES role(role_id)
);
curl -X PUT http://localhost:8080/api/users/12345678 \
-H "Content-Type: application/json" \
-d '{
  "userName": "Updated User Name",
  "email": "updateduser@example.com",
  "profileUrl": "http://example.com/profile/updateduser",
  "role": {
    "roleId": 1
  }
}'

fetch('http://localhost:8080/api/users/12345678', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    userName: 'New Full Name',
    email: 'newfull@example.com',
    profileUrl: 'http://newurl.com',
    role: {
      roleId: 2, // Assuming roleId 2 exists
    },
  }),
})
  .then(response => response.json())
  .then(data => console.log('User updated:', data))
  .catch(error => console.error('Error:', error));

