import javax.persistence.*;
import java.util.Arrays;

@Entity
public class User {
    @Id
    private String userId;

    @Column(name = "group_list", columnDefinition = "text[]")
    private String[] groupList;

    // Constructors, Getters, and Setters
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
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.Optional;

public interface UserRepository extends JpaRepository<User, String> {
    Optional<User> findByUserId(String userId);
}
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.Optional;

@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public User getUserOrCreate(String userId, String[] groupList) {
        Optional<User> user = userRepository.findByUserId(userId);
        return user.orElseGet(() -> userRepository.save(new User(userId, groupList)));
    }
}
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/{userId}")
    public User getUserOrCreate(@PathVariable String userId, @RequestParam String groupList) {
        String[] groupArray = groupList.split(",");
        return userService.getUserOrCreate(userId, groupArray);
    }
}
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
