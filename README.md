# MiniRedSocialMongoDB

## Overview
MiniRedSocialMongoDB is a social networking web application built with Node.js, Express, and MongoDB. The platform allows users to create profiles, share posts, comment on content, and receive real-time notifications.

## Features
- **User Authentication**: Secure registration and login system
- **Profile Management**: Create and edit user profiles
- **Social Posting**: Create, edit, and delete posts
- **Interaction**: Comment on posts, like content
- **Real-time Notifications**: Receive instant updates using Socket.io
- **Responsive Design**: Mobile-friendly user interface

## Technology Stack
- **Backend**: Node.js with Express.js
- **Database**: MongoDB 
- **Authentication**: bcrypt for password hashing
- **Session Management**: express-session with MongoDB store
- **Frontend Templating**: Handlebars (hbs)
- **Real-time Communication**: Socket.io

## MongoDB Integration
The application uses MongoDB as its primary database, leveraging Mongoose as an ODM (Object Data Modeling) library. Key aspects include:

- **Data Models**:
  - User - Stores user profiles and authentication data
  - Post - Manages social media posts with references to creators
  - Comment - Handles comments with relations to posts and users
  - Notification - Manages user notifications for social interactions

- **Session Storage**: Sessions are stored in MongoDB using connect-mongo

- **Database Connection**: Configurable to work with both local MongoDB instances and MongoDB Atlas cloud service

## Getting Started
1. Ensure MongoDB is installed and running on your system
2. Clone the repository
3. Install dependencies:
   ```
   npm install
   ```
4. Start the application:
   ```
   node app.js
   ```
5. Access the application at http://localhost:3000

## Dependencies
- bcrypt: Password hashing
- express: Web framework
- mongoose: MongoDB object modeling
- express-session: Session management
- connect-mongo: MongoDB session store
- hbs: Handlebars templating engine
- socket.io: Real-time communication