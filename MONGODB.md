# MongoDB Configuration and Commands for MiniRedSocialMongoDB

This document provides detailed information about the MongoDB setup, configuration, and common commands used in this project.

## Database Configuration

The application connects to MongoDB using Mongoose in `app.js`:

```javascript
// Connection string format
const MONGODB_URI = 'mongodb://127.0.0.1:27017/socialapp-auth';

// Mongoose connection
mongoose.connect(MONGODB_URI)
  .then(() => {
    console.log('Connected to MongoDB');
  })
  .catch(err => {
    console.error('MongoDB connection error:', err);
  });
```

### Connection String Options

- **Local Development**: `mongodb://127.0.0.1:27017/socialapp-auth`
- **MongoDB Atlas**: `mongodb+srv://<username>:<password>@<cluster>.mongodb.net/socialapp-auth?retryWrites=true&w=majority`
- **With Authentication**: `mongodb://<username>:<password>@127.0.0.1:27017/socialapp-auth`

## Data Models

The application uses Mongoose schemas to define the data structure:

### User Model

```javascript
const userSchema = new mongoose.Schema({
  username: { type: String, required: true, unique: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  fullName: { type: String, default: '' },
  profilePhoto: { type: String, default: '' },
  bio: { type: String, default: '' },
  createdAt: { type: Date, default: Date.now }
});
```

### Post Model

```javascript
const postSchema = new mongoose.Schema({
  content: String,
  createdAt: { type: Date, default: Date.now },
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
});
```

### Session Storage

Sessions are stored in MongoDB using `connect-mongo`:

```javascript
const sessionStore = MongoStore.create({ 
  mongoUrl: MONGODB_URI,
  collectionName: 'sessions'
});

app.use(session({
  secret: 'secretkey',
  resave: false,
  saveUninitialized: false,
  store: sessionStore,
  cookie: { maxAge: 1000 * 60 * 60 * 24 } // 1 day
}));
```

## MongoDB Installation

### Linux (Ubuntu/Debian)

```bash
# Import MongoDB public GPG key
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -

# Create list file for MongoDB
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list

# Update package database
sudo apt update

# Install MongoDB
sudo apt install -y mongodb-org

# Start MongoDB
sudo systemctl start mongod

# Enable MongoDB service on startup
sudo systemctl enable mongod
```

### macOS (using Homebrew)

```bash
# Install MongoDB
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB service
brew services start mongodb-community
```

### Windows

1. Download the MongoDB installer from [MongoDB Download Center](https://www.mongodb.com/try/download/community)
2. Run the installer and follow the instructions
3. MongoDB will be installed as a Windows service that starts automatically

## Common MongoDB Commands

### Basic MongoDB Shell Commands

```bash
# Start MongoDB shell
mongosh

# Show all databases
show dbs

# Use a specific database
use socialapp-auth

# Show all collections in current database
show collections

# Display all documents in a collection
db.users.find()

# Display documents with formatting
db.users.find().pretty()

# Find a specific document
db.users.findOne({username: "exampleuser"})

# Count documents in a collection
db.users.countDocuments()

# Create a new document
db.users.insertOne({
  username: "newuser",
  email: "user@example.com",
  password: "hashedpassword",
  createdAt: new Date()
})

# Update a document
db.users.updateOne(
  { username: "newuser" },
  { $set: { fullName: "New User", bio: "Hello world" } }
)

# Delete a document
db.users.deleteOne({ username: "newuser" })

# Delete all documents in a collection
db.users.deleteMany({})

# Exit the MongoDB shell
exit
```

### Database Backup and Restore

```bash
# Backup a database
mongodump --db socialapp-auth --out ./backup

# Restore a database
mongorestore --db socialapp-auth ./backup/socialapp-auth
```

## Mongoose Methods Used in This Project

```javascript
// Create a new document
const newUser = new User({ username, email, password });
await newUser.save();

// Find documents
const users = await User.find();

// Find with filtering
const user = await User.findOne({ username });

// Find by ID
const user = await User.findById(userId);

// Find with population (joining)
const posts = await Post.find().populate('author');

// Update a document
await User.findByIdAndUpdate(userId, { fullName, bio });

// Delete a document
await Post.findByIdAndDelete(postId);
```

## Security Best Practices

1. **Use Environment Variables**: Store your MongoDB connection string in environment variables rather than hardcoding it
2. **Enable Authentication**: Always enable authentication for production MongoDB instances
3. **Use TLS/SSL**: Enable TLS/SSL for secure connections to MongoDB
4. **IP Whitelisting**: Restrict database access to specific IP addresses
5. **Regular Backups**: Implement regular database backups
6. **Update MongoDB**: Keep MongoDB updated with the latest security patches

## Troubleshooting Common Issues

1. **Connection Issues**: If unable to connect, check if MongoDB is running: `sudo systemctl status mongod`
2. **Authentication Failures**: Verify credentials in the connection string
3. **Performance Issues**: Create appropriate indexes for frequently queried fields
4. **Data Corruption**: Use `mongodump` regularly to backup your data 