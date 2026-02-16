# MERN Chat App - Deployment Guide on Render

## Prerequisites
- GitHub account with project pushed
- MongoDB Atlas account (free tier available)
- Render account (https://render.com)

## Step 1: Set Up MongoDB Atlas

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free account or log in
3. Create a new project and cluster
4. Click "Connect" and select "Drivers"
5. Copy the connection string: `mongodb+srv://username:password@cluster.mongodb.net/database?retryWrites=true&w=majority`
6. Replace `username`, `password`, and `database` with your values

## Step 2: Prepare Your Project

### Build Frontend
```bash
cd frontend
npm run build
```

This creates a `dist/` folder that the backend will serve.

### Update Environment Variables for Production

Create a `.env` file for local testing with production values:
```
PORT=5000
MONGO_URI=mongodb+srv://YOUR_USERNAME:YOUR_PASSWORD@cluster.mongodb.net/chatdb?retryWrites=true&w=majority
JWT_SECRET=your_very_secure_secret_key_at_least_32_chars
NODE_ENV=production
CLIENT_URL=https://your-app-name.onrender.com
```

## Step 3: Deploy to Render

### Deploy Backend

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click "New +" → "Web Service"
3. Connect your GitHub repository (Tejass17x/mernapp)
4. Configure:
   - **Name**: `mern-chat-app-backend` (or your choice)
   - **Environment**: Node
   - **Build Command**: `npm install && npm install --prefix frontend && npm run build --prefix frontend`
   - **Start Command**: `node backend/server.js`
   - **Region**: Choose closest to you

5. Click "Advanced" and add Environment Variables:
   ```
   PORT=5000
   MONGO_URI=your_mongodb_atlas_connection_string
   JWT_SECRET=your_secure_secret_key
   NODE_ENV=production
   CLIENT_URL=https://your-app-name.onrender.com
   ```

6. Click "Create Web Service"
7. Wait for deployment (takes 2-5 minutes)
8. Note your backend URL like: `https://mern-chat-app-backend.onrender.com`

### Update Frontend Socket Connection (Optional)

If you want to deploy frontend separately:

1. In `frontend/src/context/SocketContext.jsx`, update the socket URL:
   ```javascript
   const socket = io("https://your-backend-url.onrender.com", {
       query: { userId: authUser._id },
   });
   ```

2. In `frontend/vite.config.js`, update proxy (for development):
   ```javascript
   proxy: {
       "/api": {
           target: "https://your-backend-url.onrender.com",
       },
   }
   ```

## Step 4: Test Your Deployment

1. Go to your Render backend URL: `https://your-app-name.onrender.com`
2. Try signing up: `https://your-app-name.onrender.com/signup`
3. Test login and messaging
4. Check Render logs if issues occur: Dashboard → Your App → Logs

## Current Setup

The application is configured to:
- Serve frontend static files from backend
- Use MongoDB Atlas for database
- Support real-time messaging via Socket.io
- Authenticate users with JWT tokens

## Troubleshooting

### "Cannot find module" errors
```bash
npm install
npm install --prefix frontend
npm run build --prefix frontend
```

### Socket.io connection issues
- Check `CLIENT_URL` environment variable matches your Render URL
- Verify CORS settings in `backend/socket/socket.js`
- Check browser console for connection errors

### MongoDB connection failed
- Verify connection string in MongoDB Atlas
- Check IP whitelist includes `0.0.0.0/0`
- Confirm database name and credentials

### Free Tier Considerations
- Render spins down after 15 minutes of inactivity
- First request may take 30 seconds
- MongoDB Atlas free tier has limitations (512MB)

## Useful Links
- [Render Documentation](https://render.com/docs)
- [MongoDB Atlas Setup](https://docs.atlas.mongodb.com)
- [Socket.io Deployment](https://socket.io/docs/v4/deploying-on-render/)

## Next Steps
1. Add more features (image upload, groups, etc.)
2. Set up custom domain
3. Enable HTTPS (Render does this automatically)
4. Add email notifications
5. Set up CI/CD pipeline
