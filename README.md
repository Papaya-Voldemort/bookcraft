# Pre-Project README for Bookcraft

**Project Name**: Bookcraft  
**Description**: Bookcraft is a web-based writing management tool designed to assist authors in collaboratively planning, organizing, and writing their books. The tool will provide features for outlining chapters, managing character profiles, drafting content, and compiling the final manuscript into various formats, with **real-time collaborative editing** and **user sign-in functionality**.

---

## Project Goals
- **Collaborative Planning**: Enable users to create and manage detailed outlines and character profiles.
- **Live Writing and Collaboration**: Provide a platform for real-time collaborative writing and editing, allowing multiple users to work on the same document simultaneously.
- **User Authentication**: Allow users to sign in, manage their projects, and securely save their progress.
- **Compilation and Export**: Allow users to export their finished book in multiple formats (e.g., PDF, ePub).

---

## Tech Stack
- **Frontend**: React.js  
  (with libraries for routing, state management, and collaborative editing)  
  - Additional Libraries: `react-router-dom`, `react-quill` (for a rich text editor)

- **Backend**: Node.js with Express.js  
  (for handling API requests and WebSocket-based live collaboration)  
  - Additional Libraries: `express`, `cors`, `socket.io` (for real-time collaboration)

- **Database**: MongoDB  
  (to store user data, book outlines, drafts, and live editing state)

- **Authentication**: Firebase Authentication or Auth0  
  (to manage user accounts and secure login)

- **Real-Time Collaboration**: Socket.IO  
  (to enable live editing and real-time updates across users)

- **Deployment**:  
  - Frontend: Vercel  
  - Backend: Heroku or DigitalOcean

---

## Features

### **1. User Authentication**
- Users will be able to sign up, log in, and manage their accounts.
- Authentication will be managed by **Firebase Authentication** or **Auth0**.
- Each user will have access to their own projects, which will be stored securely in the database.

### **2. Real-Time Collaboration**
- Multiple users (you and your brother) can edit the same section of the book simultaneously.
- Changes made by one user will appear instantly for the other user (using WebSockets via **Socket.IO**).
- A user presence indicator will show which user is currently editing (e.g., a small tag showing "User1 is typing" or a cursor with their name).

### **3. Project Management and Writing Tools**
- **Planning Tools**:
  - Chapter outlines, character profiles, and plot timelines.
  - Drag-and-drop functionality for reordering chapters or sections.
- **Writing Tools**:
  - A rich text editor (using `react-quill` or `draft-js`) with formatting options (bold, italics, headers, etc.).
  - Version control for drafts to track edits and revert to previous versions.
- **Export Tools**:
  - Export the book to formats like `.pdf`, `.docx`, or `.epub`.

---

## Building the Project

### **1. Repository Setup**
- Create a new repository on GitHub named `bookcraft`.
- Clone the repository to your local machine:

```bash
git clone https://github.com/yourusername/bookcraft.git
cd bookcraft
```

---

### **2. Frontend Setup**
- Navigate to the `client` directory and initialize a new React app:

```bash
npx create-react-app client
cd client
```

- Install necessary libraries:

```bash
npm install react-router-dom axios react-quill socket.io-client
```

- **Key Features to Implement:**
  - **Routing:** Use `react-router-dom` to handle navigation (e.g., login, dashboard, editor).
  - **Rich Text Editor:** Use `react-quill` or `draft-js` for the writing interface.
  - **Real-Time Updates:** Use `socket.io-client` to establish WebSocket connections for live collaboration.

---

### **3. Backend Setup**
- Navigate to the `server` directory and set up a basic Node.js application:

```bash
mkdir server
cd server
npm init -y
npm install express mongoose cors dotenv socket.io
```

- Create a basic `server.js` file to handle API routes and WebSocket events.

#### **WebSocket Setup for Real-Time Collaboration**
- Add Socket.IO for real-time communication between users:

```javascript
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

const server = http.createServer(app);
const io = new Server(server, {
  cors: {
    origin: 'http://localhost:3000', // Frontend URL
    methods: ['GET', 'POST'],
  },
});

// Socket.IO events
io.on('connection', (socket) => {
  console.log('User connected:', socket.id);

  socket.on('join-document', (documentId) => {
    socket.join(documentId);
    console.log(`User ${socket.id} joined document ${documentId}`);
  });

  socket.on('send-changes', (data) => {
    const { documentId, content } = data;
    socket.to(documentId).emit('receive-changes', content);
  });

  socket.on('disconnect', () => {
    console.log('User disconnected:', socket.id);
  });
});

const PORT = process.env.PORT || 5000;
server.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

- This backend will handle WebSocket connections for live editing.

---

### **4. Database Configuration**
- Use **MongoDB** to store user accounts, book outlines, and drafts:
  - User schema: Stores user credentials and project references.
  - Document schema: Stores project details, live content, and metadata.

Example MongoDB schema for users and documents:

```javascript
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  username: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  projects: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Document' }],
});

const DocumentSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: { type: String, default: '' },
  lastEdited: { type: Date, default: Date.now },
  users: [{ type: mongoose.Schema.Types.ObjectId, ref: 'User' }],
});

const User = mongoose.model('User', UserSchema);
const Document = mongoose.model('Document', DocumentSchema);

module.exports = { User, Document };
```

---

### **5. Authentication**
- Set up Firebase Authentication to allow users to sign up and log in.
- Save user tokens and project references in MongoDB for secure access.

---

### **6. Deployment**
- **Frontend**: Deploy the React app to Vercel.
- **Backend**: Deploy the Node.js app to Heroku.
- Set up environment variables for the database connection, Firebase credentials, and other sensitive information.

---

## Next Steps for Development
1. **Frontend Development**:
   - Build login and sign-up pages.
   - Create a dashboard for users to see and select their projects.
   - Implement the live editor with real-time updates using `react-quill` and `socket.io-client`.

2. **Backend Development**:
   - Create REST API endpoints for user authentication and project CRUD operations.
   - Add WebSocket handlers for real-time collaboration.

3. **Testing**:
   - Test real-time collaboration with multiple users.
   - Ensure authentication and project security.

4. **UI/UX Design**:
   - Build a clean and intuitive user interface for planning and writing.

---

## Notes for LLM
- Assist with generating code for user authentication, real-time collaboration, and rich text editing as well as the rest of the application.
- Provide troubleshooting help for WebSocket implementation and React state management.
- Suggest best practices for secure and scalable deployment.
- Make sure to guild the user with the entire set up.

---
