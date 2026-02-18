# Render Deployment - Environment Variables Setup

## 🔴 IMPORTANT: Set These Variables on Render Dashboard

Go to your Render Web Service → Environment → Add these variables:

### Required Environment Variables

| Key | Value | Example |
|-----|-------|---------|
| **MONGO_URI** | Your MongoDB Atlas connection string | `mongodb+srv://username:password@cluster.mongodb.net/chatdb?retryWrites=true&w=majority` |
| **JWT_SECRET** | Strong random secret key (32+ characters) | `a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6` |
| **NODE_ENV** | `production` | `production` |
| **PORT** | `5000` | `5000` |
| **CLIENT_URL** | Your frontend URL (if deploying frontend separately) | `https://your-app.vercel.app` |

## 🔑 How to Generate JWT_SECRET

Run this command in your terminal:
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

Copy the output and paste it in Render as `JWT_SECRET`

## 📝 Step-by-Step Setup on Render

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click on your deployed service
3. Go to **Settings** → **Environment**
4. Click **Add Environment Variable**
5. For each variable above:
   - Enter the **Key** (e.g., `MONGO_URI`)
   - Enter the **Value** (e.g., your MongoDB connection string)
   - Click Add
6. Variables are applied immediately (may trigger auto-deploy)

## 📊 Example Values

### For MongoDB Atlas (Recommended for Production)

```
MONGO_URI=mongodb+srv://myusername:mypassword@cluster0.abcde.mongodb.net/chatdb?retryWrites=true&w=majority
```

**Steps to get MongoDB Atlas URI:**
1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create account and cluster (M0 Free tier)
3. Click "Connect" → "Drivers"
4. Copy connection string
5. Replace `<username>`, `<password>`, and `<database_name>`

### JWT Secret Example

```
JWT_SECRET=e7f4c2a1d9b5f3e6a8c2d1b4f7e3a9c5f2d8b1e4a7c3f6d9b2e5a8c1f4d7b0
```

## ✅ Verification

After setting variables:

1. Check Render logs: Dashboard → Logs
2. Look for: `Connected to MongoDB` ✓
3. Visit your deployed URL
4. Test login/signup functionality

## 🚨 Troubleshooting

### "Cannot find module" Error
- Run build command locally: `npm run build`
- Check frontend dist folder exists

### "Connected to MongoDB" not shown
- Verify MONGO_URI is correct
- Check MongoDB Atlas allows Render IP (whitelist)
- Test connection string locally first

### Build fails on Render
- Check logs: Render Dashboard → Logs
- Verify Node version: `node --version`
- Ensure package.json has all dependencies

## 🔗 Build Command Verification

Your Render build command:
```
npm install && npm install --prefix frontend && npm run build --prefix frontend
```

This:
1. ✅ Installs backend dependencies
2. ✅ Installs frontend dependencies
3. ✅ Builds React frontend to `/frontend/dist`

## 🚀 Final Checklist

- [ ] MONGO_URI set correctly
- [ ] JWT_SECRET generated and set
- [ ] NODE_ENV set to `production`
- [ ] PORT set to `5000`
- [ ] Build logs show "Frontend compiled"
- [ ] Can see "Connected to MongoDB"
- [ ] Frontend loads at your Render URL

If issues persist, check the full logs on Render Dashboard.
