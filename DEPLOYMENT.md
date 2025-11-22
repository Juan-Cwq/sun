# Deploying Cors.app (Cadenza) to Netlify

## Prerequisites
- A Netlify account (free tier works)
- Your code pushed to a Git repository (GitHub, GitLab, or Bitbucket)

## Deployment Steps

### Option 1: Deploy via Netlify Dashboard (Recommended)

1. **Push your code to GitHub/GitLab/Bitbucket**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin <your-repo-url>
   git push -u origin main
   ```

2. **Connect to Netlify**
   - Go to [netlify.com](https://netlify.com) and log in
   - Click "Add new site" → "Import an existing project"
   - Choose your Git provider and select your repository
   - Netlify will auto-detect Next.js settings

3. **Configure Build Settings** (should be auto-detected)
   - Build command: `npm run build`
   - Publish directory: `.next`
   - Install command: `npm install`

4. **Set Environment Variables** (Optional)
   - Go to Site settings → Environment variables
   - Add `SUNOAPI_KEY` with value: `5288dff8494437038cc5690afa43777c`
   - Note: The API key is already hardcoded in the client, so this is optional

5. **Deploy**
   - Click "Deploy site"
   - Wait for build to complete (usually 2-5 minutes)
   - Your site will be live at `https://random-name.netlify.app`

6. **Custom Domain** (Optional)
   - Go to Site settings → Domain management
   - Add your custom domain (e.g., `cors.app`)
   - Follow DNS configuration instructions

### Option 2: Deploy via Netlify CLI

1. **Install Netlify CLI**
   ```bash
   npm install -g netlify-cli
   ```

2. **Login to Netlify**
   ```bash
   netlify login
   ```

3. **Initialize and Deploy**
   ```bash
   netlify init
   netlify deploy --prod
   ```

## Important Notes

### ⚠️ **API Route Timeouts**
- Netlify Functions have a **10-second timeout on free tier** (26 seconds on Pro)
- Your music generation polling runs for up to **10 minutes**
- **Solution**: The polling happens on the client side, so each individual API call is quick (<10s)
- The `/api/status` endpoint just checks status, which is fast
- The long wait happens client-side with 30-second intervals between checks

### ✅ **What Works on Netlify:**
- ✅ Music generation requests (quick API call)
- ✅ Status polling (client-side with 30s intervals)
- ✅ Credits checking
- ✅ Lyrics fetching
- ✅ Video generation requests
- ✅ Cover art generation
- ✅ All client-side features (audio player, history, etc.)

### ⚠️ **Callback URLs**
- The callback URLs in your API routes use `process.env.NEXT_PUBLIC_BASE_URL`
- On Netlify, this will automatically be your site URL
- For development, callbacks won't work (localhost isn't accessible to Suno API)
- For production, callbacks will work once deployed

### 🔒 **Security Consideration**
Your API key is currently hardcoded in the client-side code:
```typescript
const apiKey = '5288dff8494437038cc5690afa43777c';
```

**This means:**
- ✅ Easy to deploy and use
- ⚠️ API key is visible in browser DevTools
- ⚠️ Anyone can extract and use your API key

**To improve security** (optional):
1. Remove the hardcoded key from `app/page.tsx`
2. Add it as a Netlify environment variable
3. Access it only in API routes (server-side)
4. Pass it from API routes to Suno API

## Testing Your Deployment

After deployment, test these features:
1. ✅ Generate a song (2-4 minute wait is normal)
2. ✅ Check credits
3. ✅ View song history (stored in browser localStorage)
4. ✅ Download generated songs
5. ✅ Generate cover art
6. ✅ Generate video
7. ✅ View synchronized lyrics

## Troubleshooting

### Build Fails
- Check build logs in Netlify dashboard
- Ensure all dependencies are in `package.json`
- Try building locally first: `npm run build`

### API Routes Don't Work
- Check Netlify Functions logs
- Verify environment variables are set
- Check that API key is valid

### Timeouts
- If you get timeout errors, check if the issue is with Suno API
- Client-side polling should handle long waits automatically

## Continuous Deployment

Once connected to Git:
- Every push to `main` branch triggers automatic deployment
- Pull requests create preview deployments
- Rollback to previous versions anytime in Netlify dashboard

## Cost

**Free Tier Includes:**
- 100GB bandwidth/month
- 300 build minutes/month
- Unlimited sites
- HTTPS included
- Continuous deployment

**This should be more than enough for a personal music generation app!**
