-- Create schema if not exists
CREATE SCHEMA IF NOT EXISTS test;

-- Set the search path to the schema
SET search_path TO test;

-- Drop the users table if it exists
DROP TABLE IF EXISTS users;

-- Create the users table
CREATE TABLE users (
    userId VARCHAR(50) NOT NULL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    name VARCHAR(100) NOT NULL,
    role VARCHAR(50) NOT NULL CHECK (role IN ('Admin', 'Model Trainer', 'Trainer', 'Maker', 'Checker')),
    profilePicUrl TEXT NOT NULL
);
-- Insert example data into the users table
INSERT INTO users (userId, email, name, role, profilePicUrl) VALUES
('45386732', 'avinav.adarsh@hsbc.com', 'Avinav Adarsh', 'Admin', 'https://example.com/profile/avinav-adarsh.jpg'),
('46273892', 'john.doe@hsbc.com', 'John Doe', 'Model Trainer', 'https://example.com/profile/john-doe.jpg'),
('48293747', 'jane.smith@hsbc.com', 'Jane Smith', 'Trainer', 'https://example.com/profile/jane-smith.jpg'),
('49382747', 'mary.jones@hsbc.com', 'Mary Jones', 'Maker', 'https://example.com/profile/mary-jones.jpg'),
('50384756', 'david.brown@hsbc.com', 'David Brown', 'Checker', 'https://example.com/profile/david-brown.jpg');
