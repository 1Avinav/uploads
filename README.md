public List<User> getAllUsers() {
        return userRepository.findAll();
    }

  @GetMapping
    public ResponseEntity<?> getAllUsers(@RequestHeader("Authorization") String token) {
        // Extract the role from the JWT token
        String userRole = jwtUtils.getRoleFromJwt(token);

        // Only allow Admin role to access this endpoint
        if ("Admin".equalsIgnoreCase(userRole)) {
            List<User> users = userService.getAllUsers();
            return ResponseEntity.ok(users);
        }

        // If not an Admin, return unauthorized access
        return ResponseEntity.status(403).body("Access Denied: Admins only.");
    }
