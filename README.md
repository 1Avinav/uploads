spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.datasource.url=jdbc:postgresql://us-db-server:5432/prod_us_db
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
java -Dspring.profiles.active=prod,us -jar your-application.jar


set SPRING_PROFILES_ACTIVE=prod,us
set DB_USERNAME=your-db-username
set DB_PASSWORD=your-db-password
