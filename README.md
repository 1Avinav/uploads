curl -X POST http://localhost:8080/api/sso-login \
-H "Authorization: Bearer <your-jwt-token>" \
-H "Content-Type: application/json"


curl -X GET http://localhost:8080/api/users \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json"


curl -X POST http://localhost:8080/api/users \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json" \
-d '{
  "userId": "12345678",
  "userName": "New User",
  "email": "new.user@example.com",
  "profilePicUrl": "http://example.com/new-pic.jpg",
  "role": "Model Trainer"
}'


curl -X PUT http://localhost:8080/api/users/12345678 \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json" \
-d '{
  "userName": "Updated User",
  "email": "updated.user@example.com",
  "profilePicUrl": "http://example.com/updated-pic.jpg",
  "role": "Maker"
}'


curl -X PUT http://localhost:8080/api/users/12345678 \
-H "Authorization: Bearer <user-jwt-token>" \
-H "Content-Type: application/json" \
-d '{
  "userName": "Updated Name",
  "profilePicUrl": "http://example.com/updated-pic.jpg"
}'


curl -X DELETE http://localhost:8080/api/users/12345678 \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json"


curl -X GET http://localhost:8080/api/projects \
-H "Authorization: Bearer <user-or-admin-jwt-token>" \
-H "Content-Type: application/json"


curl -X POST http://localhost:8080/api/projects \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json" \
-d '{
  "name": "New Project",
  "description": "Description of New Project",
  "apiVersion": "v2",
  "status": "Active",
  "creator": "Admin"
}'


curl -X PUT http://localhost:8080/api/projects/1 \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json" \
-d '{
  "description": "Updated Description",
  "status": "Inactive"
}'

curl -X DELETE http://localhost:8080/api/projects/1 \
-H "Authorization: Bearer <admin-jwt-token>" \
-H "Content-Type: application/json"

