# Strapi Deployment Guide - Step by Step

## Option 1: Railway (Recommended - Easiest)

### Step 1: Push Strapi to GitHub

1. Open Terminal
2. Navigate to your Strapi folder:
   ```
   cd /Users/rrezonistrefaj/Documents/dev/OtherWork/treplus-strapi
   ```
3. Check what files need to be committed:
   ```
   git status
   ```
4. Add all files:
   ```
   git add .
   ```
5. Commit the changes:
   ```
   git commit -m "Prepare for deployment"
   ```
6. Push to GitHub:
   ```
   git push origin main
   ```
   (If you don't have a remote yet, create a new repository on GitHub first, then add it)

### Step 2: Sign Up for Railway

1. Go to https://railway.app in your browser
2. Click "Start a New Project"
3. Sign up with your GitHub account (it's free)
4. Authorize Railway to access your GitHub repositories

### Step 3: Create New Project on Railway

1. After logging in, click "New Project"
2. Select "Deploy from GitHub repo"
3. You'll see a list of your GitHub repositories
4. Find and select `treplus-strapi` (or whatever you named it)
5. Click "Deploy Now"

### Step 4: Add PostgreSQL Database

1. In your Railway project dashboard, click the "+ New" button
2. Select "Database"
3. Click "Add PostgreSQL"
4. Railway will automatically create a PostgreSQL database
5. Wait for it to finish provisioning (takes about 30 seconds)

### Step 5: Generate Secret Keys

You need to generate 5 random secret strings. Here's how:

1. Open Terminal
2. Run this command 5 times (each time will give you a different random string):
   ```
   openssl rand -base64 32
   ```
3. Copy each result - you'll need them in the next step

### Step 6: Configure Environment Variables

1. In Railway, click on your Strapi service (not the database)
2. Click on the "Variables" tab
3. Click "New Variable" and add each of these one by one:

   **Variable 1:**
   - Name: `DATABASE_CLIENT`
   - Value: `postgres`
   - Click "Add"

   **Variable 2:**
   - Name: `DATABASE_URL`
   - Value: Click "Reference Variable" → Select your PostgreSQL service → Select `DATABASE_URL`
   - This will automatically use `${{Postgres.DATABASE_URL}}`
   - Click "Add"

   **Variable 3:**
   - Name: `NODE_ENV`
   - Value: `production`
   - Click "Add"

   **Variable 4:**
   - Name: `APP_KEYS`
   - Value: Paste 4 random strings separated by commas (from Step 5)
   - Example: `abc123,def456,ghi789,jkl012`
   - Click "Add"

   **Variable 5:**
   - Name: `API_TOKEN_SALT`
   - Value: Paste one random string from Step 5
   - Click "Add"

   **Variable 6:**
   - Name: `ADMIN_JWT_SECRET`
   - Value: Paste one random string from Step 5
   - Click "Add"

   **Variable 7:**
   - Name: `TRANSFER_TOKEN_SALT`
   - Value: Paste one random string from Step 5
   - Click "Add"

   **Variable 8:**
   - Name: `JWT_SECRET`
   - Value: Paste one random string from Step 5
   - Click "Add"

   **Variable 9:**
   - Name: `HOST`
   - Value: `0.0.0.0`
   - Click "Add"

   **Variable 10:**
   - Name: `PORT`
   - Value: `1337`
   - Click "Add"

### Step 7: Configure Build Settings

1. Still in your Strapi service, click on the "Settings" tab
2. Scroll down to "Build & Deploy"
3. Set "Build Command" to: `npm run build`
4. Set "Start Command" to: `npm start`
5. Railway will automatically save and redeploy

### Step 8: Wait for Deployment

1. Go to the "Deployments" tab
2. You'll see Railway building and deploying your app
3. Wait for it to complete (usually 2-5 minutes)
4. Once it says "Active", click on your service
5. You'll see a URL like `https://your-app-name.railway.app`
6. Copy this URL - this is your Strapi URL

### Step 9: Access Strapi Admin Panel

1. Open the URL you copied in a new browser tab
2. Add `/admin` to the end: `https://your-app-name.railway.app/admin`
3. You'll see the Strapi admin setup page
4. Create your admin account:
   - Enter your email
   - Enter a password (make it strong!)
   - Enter your first name and last name
   - Click "Let's start"

### Step 10: Configure CORS for Your Next.js App

1. In Strapi admin panel, go to "Settings" (gear icon in left sidebar)
2. Click "Users & Permissions" → "Advanced Settings"
3. Find "CORS" section
4. In "Authorized URLs", add your Next.js domain:
   - If deploying to Vercel: `https://your-nextjs-app.vercel.app`
   - For local development: `http://localhost:3000`
   - Add both if you want to test locally too
5. Click "Save"

### Step 11: Create API Token in Strapi

1. In Strapi admin, go to "Settings" → "API Tokens"
2. Click "Create new API Token"
3. Fill in:
   - Token name: `Next.js Production`
   - Token duration: `Unlimited` (or set a date)
   - Token type: `Full access` (or customize as needed)
4. Click "Save"
5. **IMPORTANT:** Copy the token immediately (you won't see it again!)
6. Save it somewhere safe

### Step 12: Update Your Next.js App

1. If deploying to Vercel:
   - Go to your Vercel project dashboard
   - Go to "Settings" → "Environment Variables"
   - Add these variables:

   **Variable 1:**
   - Name: `NEXT_PUBLIC_STRAPI_URL`
   - Value: `https://your-strapi-app.railway.app` (your Railway URL)
   - Environment: Production, Preview, Development (check all)
   - Click "Save"

   **Variable 2:**
   - Name: `STRAPI_URL`
   - Value: `https://your-strapi-app.railway.app` (same URL)
   - Environment: Production, Preview, Development (check all)
   - Click "Save"

   **Variable 3:**
   - Name: `STRAPI_API_TOKEN`
   - Value: Paste the API token from Step 11
   - Environment: Production, Preview, Development (check all)
   - Click "Save"

2. Update `next.config.ts` in your Next.js project:
   - Open `/Users/rrezonistrefaj/Documents/dev/OtherWork/treplus/next.config.ts`
   - Add your Strapi domain to `remotePatterns`:
   ```typescript
   remotePatterns: [
     // ... existing patterns ...
     {
       protocol: 'https',
       hostname: 'your-strapi-app.railway.app',
       pathname: '/uploads/**',
     },
   ],
   ```

3. Redeploy your Next.js app (Vercel will auto-deploy if connected to GitHub)

### Step 13: Test Everything

1. Visit your Next.js app
2. Check if content from Strapi is loading
3. Check browser console for any errors
4. If images aren't loading, verify the `next.config.ts` update

---

## Option 2: Render (Alternative Platform)

### Step 1: Push to GitHub
(Same as Railway Step 1)

### Step 2: Sign Up for Render
1. Go to https://render.com
2. Sign up with GitHub (free tier available)

### Step 3: Create PostgreSQL Database
1. In Render dashboard, click "New +"
2. Select "PostgreSQL"
3. Name it (e.g., "treplus-strapi-db")
4. Select free tier
5. Click "Create Database"
6. Wait for it to provision
7. Copy the "Internal Database URL" (you'll need this)

### Step 4: Create Web Service
1. Click "New +" → "Web Service"
2. Connect your GitHub repository
3. Select `treplus-strapi`
4. Configure:
   - Name: `treplus-strapi`
   - Region: Choose closest to you
   - Branch: `main`
   - Root Directory: (leave empty)
   - Runtime: `Node`
   - Build Command: `npm run build`
   - Start Command: `npm start`
5. Click "Advanced"
6. Add Environment Variables (same as Railway Step 6, but use the PostgreSQL URL you copied)

### Step 5-13: Follow Railway Steps 5-13
(Same process, just using Render's interface)

---

## Important Notes

1. **First Time Setup:**
   - After deployment, you must create an admin account in Strapi
   - This is a one-time setup

2. **Database:**
   - Railway/Render will handle database backups automatically
   - Your data is safe

3. **File Uploads:**
   - Files uploaded to Strapi will be stored on the server
   - For production, consider using cloud storage (AWS S3, Cloudinary) later
   - For now, Railway/Render storage is fine

4. **Environment Variables:**
   - Never commit `.env` files to GitHub
   - Always use the platform's environment variable settings

5. **Troubleshooting:**
   - If deployment fails, check the logs in Railway/Render
   - Common issues: missing environment variables, build errors
   - Make sure all secrets are set correctly

---

## What You'll Have After This

✅ Strapi running on Railway/Render  
✅ PostgreSQL database connected  
✅ Admin panel accessible  
✅ API token for Next.js  
✅ Next.js app connected to Strapi  
✅ Everything ready for production

