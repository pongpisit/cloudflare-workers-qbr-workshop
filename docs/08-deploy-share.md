# Module 8: Deploy & Share (15 minutes)

Deploy your complete application and share it with the world.

---

## What You'll Learn

- Understand your Worker deployment
- Share your Worker URL
- Monitor your Worker
- Add a custom domain (optional)

---

## Step 1: Your Worker is Already Deployed!

Great news: Your Worker is **already deployed to the internet** and live!

Every time you click **Save and Deploy**, your code goes live immediately.

### Access Your Worker Dashboard

1. Go to **Build** → **Compute & AI** → **Workers & Pages**
2. Click on your Worker name
3. You'll see the Worker project dashboard with tabs:
   - **Overview** - Status and metrics
   - **Metrics** - Requests, errors, CPU time
   - **Deployments** - Version history
   - **Bindings** - Connected resources (D1, R2, KV)
   - **Observability** - Logs and monitoring
   - **Settings** - Configuration and routes

---

## Step 2: Share Your Worker URL

Your Worker has a public URL:

```
https://[worker-name].[account-name].workers.dev
```

You can:
- Share it with anyone
- Access it from any device
- No additional deployment needed
- It's live 24/7

### How to Find Your URL

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. Click **Workers & Pages** → **Workers**
3. Click your Worker
4. The URL is displayed at the top of the page

---

## Step 3: Test Your Deployment

1. Open your Worker URL in a browser
2. Test all features:
   - Profile page loads
   - Photos display from R2
   - Captions show from D1
   - AI chatbot responds
3. Try on mobile device
4. Share the URL with friends

---

## Step 4: Monitor Your Worker

### View Logs

1. Click your Worker
2. Click **Logs** tab
3. You'll see real-time logs of requests
4. Useful for debugging issues

### View Analytics

1. Click your Worker
2. Click **Analytics** tab
3. See:
   - Number of requests
   - Error rates
   - Response times
   - Geographic distribution

---

## Step 5: Add a Custom Domain (Optional)

If you have a domain, you can use it instead of the `.workers.dev` URL:

### Prerequisites

- Own a domain (e.g., `myapp.com`)
- Domain registered with Cloudflare

### Steps

1. Go to your Worker
2. Click **Settings** button
3. Look for **Routes** section
4. Click **Add route**
5. Enter your domain (e.g., `myapp.com/*`)
6. Select your Cloudflare zone
7. Click **Save and deploy**

Now your app is available at your custom domain!

---

## Step 6: Update Your Worker Name

To rename your Worker:

1. Click your Worker
2. Click **Settings** button
3. Look for **Worker name**
4. Enter a new name
5. Click **Save**

The URL will update automatically.

---

## Step 7: Environment Variables (Optional)

To add secrets like API keys:

1. Click your Worker
2. Click **Settings** button
3. Look for **Environment variables**
4. Click **Add variable**
5. Enter name and value
6. Click **Save and deploy**

Access in code:
```javascript
const apiKey = env.MY_API_KEY;
```

---

## Step 8: Understand Deployment Status

### Green Status ✅
- Worker is deployed and live
- All requests are being handled
- No errors

### Red Status ❌
- Deployment failed
- Check error message
- Review your code for syntax errors

---

## Step 9: Rollback to Previous Version

If something breaks:

1. Click your Worker
2. Click **Deployments** tab
3. You'll see previous versions
4. Click **Rollback** on a previous version
5. Your Worker reverts instantly

---

## Step 10: Share Your App

### Share the URL

```
https://[your-worker].workers.dev
```

### Share on Social Media

```
Just built an AI chatbot with Cloudflare Workers! 🚀
Check it out: https://[your-worker].workers.dev
Built with Workers, R2, D1, and Workers AI
```

### Add to Portfolio

Include in your portfolio or resume:
- Live URL
- GitHub repo (if you have one)
- Features built
- Technologies used

---

## Key Takeaways

✅ Worker is deployed and live  
✅ Shared your Worker URL  
✅ Monitored your Worker  
✅ Understood deployment options  
✅ Ready to share with the world  

---

## Dashboard Navigation Summary

```
Cloudflare Dashboard
├── Build
│   ├── Compute & AI
│   │   ├── Workers & Pages
│   │   │   ├── Your Worker
│   │   │   │   ├── Editor (code)
│   │   │   │   ├── Settings (config)
│   │   │   │   ├── Logs (debugging)
│   │   │   │   ├── Analytics (metrics)
│   │   │   │   └── Deployments (versions)
```

---

## What's Next?

### Enhance Your App

- Add more AI models
- Create more photo galleries
- Add user authentication
- Build an API

### Learn More

- [Workers Documentation](https://developers.cloudflare.com/workers/)
- [Advanced Features](https://developers.cloudflare.com/workers/platform/limits/)
- [Best Practices](https://developers.cloudflare.com/workers/platform/best-practices/)

### Build More

- Create multiple Workers
- Use Cloudflare Pages for frontend
- Add Cloudflare Analytics Engine
- Integrate with external APIs

---

## Congratulations! 🎉

You've successfully:

✅ Created a Cloudflare account  
✅ Built multiple Workers  
✅ Stored images with R2  
✅ Created a database with D1  
✅ Built a photo gallery  
✅ Added AI chatbot  
✅ Deployed to the internet  

**You're now a Cloudflare Workers developer!**

---

## Resources

- [Cloudflare Dashboard](https://dash.cloudflare.com)
- [Workers Documentation](https://developers.cloudflare.com/workers/)
- [Community Discord](https://discord.gg/cloudflaredev)
- [Workers Pricing](https://developers.cloudflare.com/workers/platform/pricing/)

---

## Feedback

Have questions or feedback about this workshop?

- [Cloudflare Community](https://community.cloudflare.com/)
- [GitHub Issues](https://github.com/)
- [Twitter @Cloudflare](https://twitter.com/cloudflare)

---

**Thank you for completing the Cloudflare Workers Dashboard Workshop!** 🚀
