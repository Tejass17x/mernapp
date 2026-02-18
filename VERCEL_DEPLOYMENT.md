# MERN Chat App - Vercel Deployment Guide

## Important Note
Vercel works best for serverless functions and static sites. For a full-stack MERN app with WebSockets (Socket.io), here are your options:

### Option 1: Frontend on Vercel + Backend on Render (RECOMMENDED)
- **Frontend**: Deploy on Vercel (free, fast CDN)
- **Backend**: Deploy on Render (free tier available)

### Option 2: Full Stack on Vercel (Limited)
- Uses Vercel's serverless functions
- Socket.io may have limitations
- More complex setup

This guide covers **Option 1** (Recommended approach).

---

## Prerequisites
1. GitHub account with project pushed
2. MongoDB Atlas account (free tier: https://www.mongodb.com/cloud/atlas)
3. Vercel account (https://vercel.com)
4. Render account (https://render.com) - for backend

---

## Step 1: Set Up MongoDB Atlas

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free account or log in
3. Create a new project and cluster (M0 Free tier)
4. Click "Connect" → "Drivers"
5. Copy connection string: `mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority`

---

## Step 2: Deploy Backend on Render

### 2.1 Prepare Backend

Update [backend/.env](backend/.env):
```
PORT=5000
MONGO_DB_URI=mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/chatdb?retryWrites=true&w=majority
JWT_SECRET=your_very_secure_secret_key_here_at_least_32_chars
NODE_ENV=production
CLIENT_URL=https://your-frontend-app.vercel.app
```

### 2.2 Deploy to Render

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click "New +" → "Web Service"
3. Connect your GitHub repository
4. Configure:
   - **Name**: `mern-chat-app-backend`
   - **Environment**: Node
   - **Region**: Select closest region
   - **Plan**: Free or Paid (free has limits)
   - **Build Command**: `npm install`
   - **Start Command**: `node backend/server.js`

5. Click "Advanced" → Add Environment Variables:
   ```
   PORT=5000
   MONGO_DB_URI=your_mongodb_atlas_connection_string
   JWT_SECRET=your_secure_secret_key
   NODE_ENV=production
   CLIENT_URL=https://your-frontend-app.vercel.app
   ```

6. Create Web Service
7. **Note your backend URL**: `https://your-app-name.onrender.com`

---

## Step 3: Deploy Frontend on Vercel

### 3.1 Update Socket.io Connection

Edit [frontend/src/context/SocketContext.jsx](frontend/src/context/SocketContext.jsx):

```javascript
// Change this line:
const socket = io("", {
    query: { userId: authUser._id },
});

// To:
const socket = io(import.meta.env.VITE_BACKEND_URL || "http://localhost:5000", {
    query: { userId: authUser._id },
});
```

### 3.2 Create Frontend Environment File

Create [frontend/.env.local](frontend/.env.local):
```
VITE_BACKEND_URL=https://your-backend-app.onrender.com
```

Create [frontend/.env.production](frontend/.env.production):
```
VITE_BACKEND_URL=https://your-backend-app.onrender.com
```

### 3.3 Update Vite Config

Edit [frontend/vite.config.js](frontend/vite.config.js):
```javascript
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      "/api": {
        target: "http://localhost:5000",
        changeOrigin: true,
      },
    },
  },
})
```

### 3.4 Deploy to Vercel

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Click "Add New..." → "Project"
3. Import your GitHub repository
4. Configure:
   - **Root Directory**: `frontend`
   - **Framework**: `Vite`
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`

5. Add Environment Variables:
   ```
   VITE_BACKEND_URL=https://your-backend-app.onrender.com
   ```

6. Click "Deploy"
7. Wait for deployment (usually 1-2 minutes)
8. Your app will be available at: `https://your-app-name.vercel.app`

---

## Step 4: Update Backend CORS Settings (Optional)

If you get CORS errors, edit [backend/server.js](backend/server.js):

```javascript
import cors from "cors";

const allowedOrigins = [
    "http://localhost:3000",
    "https://your-frontend-app.vercel.app"
];

app.use(cors({
    origin: allowedOrigins,
    credentials: true
}));
```

Install cors:
```bash
npm install cors --save
```

---

## Step 5: Test Your Deployment

1. Visit: `https://your-app-name.vercel.app`
2. Test Signup/Login
3. Test messaging and real-time updates
4. Check browser console for errors
5. Check Render logs: Dashboard → Your App → Logs
6. Check Vercel logs: Deployments → Logs

---

## Troubleshooting

### Socket.io Connection Issues
- Ensure backend URL in frontend is correct
- Check browser console for connection errors
- Verify CORS settings in backend
- Check that backend is running on Render

### MongoDB Connection Issues
- Verify connection string in .env
- Add your IP to MongoDB Atlas whitelist
- Check database name matches

### Frontend Build Fails
- Run `npm run build` locally to test
- Check build logs in Vercel dashboard
- Ensure all dependencies are listed in package.json

### Real-time Messages Not Working
- Socket.io needs persistent connections
- Verify both apps are running
- Check network tab in browser DevTools

---

## Environment Variables Checklist

### Backend (.env on Render)
- [ ] MONGO_DB_URI
- [ ] JWT_SECRET
- [ ] NODE_ENV=production
- [ ] CLIENT_URL (frontend URL)

### Frontend (.env on Vercel)
- [ ] VITE_BACKEND_URL (backend URL)

---

## Next Steps

1. Push code to GitHub
2. Deploy backend on Render
3. Deploy frontend on Vercel
4. Test all features
5. Monitor logs for any issues
6. Scale as needed

---

## Free Tier Limitations

**Vercel Frontend**: 
- 100GB bandwidth/month
- Unlimited deployments
- Free SSL

**Render Backend**:
- Stops after 15 min of inactivity (free tier)
- 750 hours/month
- 100GB egress/month

Consider upgrading if you need production reliability.
