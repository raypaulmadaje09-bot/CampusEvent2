# Campus Pulse | Event Management System

A high-fidelity campus event scheduling and administrative platform built with React, Vite, Tailwind CSS, and Node.js.

**Repository:** [https://github.com/raypaulmadaje09-bot/CampusEvent1](https://github.com/raypaulmadaje09-bot/CampusEvent1)

## 🚀 Quick Start Guide

### 1. Prerequisites
- **Node.js** (v18 or higher)
- **MySQL Server**
- **MySQL Workbench** (recommended for database management)

### 2. Database Synchronization (Aiven Cloud)
Connect to your Aiven MySQL instance and prepare the environment:

1. Open **MySQL Workbench**.
2. Create a new connection using the **Aiven Service URI** or the parameters below:
   - **Host**: `mysql-32635dc5-mysql-lab-project-madaje.e.aivencloud.com`
   - **Port**: `15645`
   - **User**: `avnadmin`
   - **Password**: `AVNS_Bec0I9laP4vj1JUb4OH`
3. Once connected, open a new SQL tab and run the following script in the `defaultdb` database:
   ```sql
   USE defaultdb;

   -- Clean start (Optional)
   DROP TABLE IF EXISTS audit_logs;
   DROP TABLE IF EXISTS replies;
   DROP TABLE IF EXISTS feedback;
   DROP TABLE IF EXISTS events;
   DROP TABLE IF EXISTS users;

   CREATE TABLE users (
     id VARCHAR(255) PRIMARY KEY,
     name VARCHAR(255) NOT NULL,
     email VARCHAR(255) UNIQUE NOT NULL,
     password VARCHAR(255) NOT NULL,
     role ENUM('MasterAdmin', 'Admin', 'Student') NOT NULL,
     avatar TEXT
   );

   CREATE TABLE events (
     id VARCHAR(255) PRIMARY KEY,
     title VARCHAR(255) NOT NULL,
     description TEXT,
     date DATE NOT NULL,
     startTime VARCHAR(50),
     endTime VARCHAR(50),
     location VARCHAR(255),
     category VARCHAR(100),
     organizer VARCHAR(255),
     attendees INT DEFAULT 0,
     image LONGTEXT,
     isPopular BOOLEAN DEFAULT FALSE,
     isLive BOOLEAN DEFAULT FALSE,
     status ENUM('Approved', 'Pending') DEFAULT 'Pending'
   );

   CREATE TABLE feedback (
     id VARCHAR(255) PRIMARY KEY,
     senderName VARCHAR(255),
     senderEmail VARCHAR(255),
     subject VARCHAR(255),
     message TEXT,
     timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
     status ENUM('new', 'read', 'replied') DEFAULT 'new'
   );

   CREATE TABLE replies (
     id INT AUTO_INCREMENT PRIMARY KEY,
     feedback_id VARCHAR(255),
     sender ENUM('Admin', 'Student'),
     text TEXT,
     timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
     FOREIGN KEY (feedback_id) REFERENCES feedback(id) ON DELETE CASCADE
   );

   CREATE TABLE audit_logs (
     id VARCHAR(255) PRIMARY KEY,
     timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
     action VARCHAR(255),
     actor VARCHAR(255),
     type VARCHAR(50),
     details TEXT
   );

   -- INITIAL AUTHORIZED MASTER NODE
   INSERT INTO users (id, name, email, password, role) 
   VALUES ('u1', 'Master Admin', 'master@campus.edu', 'master', 'MasterAdmin');
   ```

### 3. Environment Configuration
1. In the project root, create a file named `.env`.
2. Configure your credentials based on `.env.example`:
   ```env
   DB_HOST=localhost
   DB_USER=your_mysql_username
   DB_PASSWORD=your_mysql_password
   DB_NAME=campus_event_scheduller
   PORT=5000
   ```

### 4. Initialize Protocols
Run the backend and frontend in separate terminals:

**Terminal 1 (Backend):**
```bash
node server.js
```

**Terminal 2 (Frontend):**
```bash
npm install
npm run dev
```

## 🔑 Authorized Access Nodes

| Identity | Email Node | Access Key | Authorization |
| :--- | :--- | :--- | :--- |
| **Master Admin** | `master@campus.edu` | `master` | Level 10 (Full Control) |
| **Staff Admin** | `admin@campus.edu` | `admin` | Level 5 (Operations) |
| **Student** | `alex@student.edu` | `student` | Level 1 (Engagement) |

---
**Note:** If the server is offline, the frontend will automatically switch to a high-fidelity "Mock Node" for demonstration purposes. Syncing with MySQL requires an active `server.js` instance.
