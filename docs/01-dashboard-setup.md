# Module 1: Dashboard Setup (15 minutes)

Learn to navigate the Cloudflare Dashboard and create your first Worker.

---

## What You'll Learn

- Create a free Cloudflare account
- Navigate the Cloudflare Dashboard
- Understand the main sections
- Create your first Worker

---

## Step 1: Create a Cloudflare Account

1. Go to [https://dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up)
2. Enter your email address
3. Create a strong password
4. Click **Create Account**
5. Verify your email (check your inbox)

> **Note:** You don't need a domain name or credit card for this workshop.

---

## Step 2: Verify Your Email

1. Check your email inbox for a message from Cloudflare
2. Click the **Verify Email** link
3. You'll be redirected to the Dashboard

---

## Step 3: Navigate the Cloudflare Dashboard

Once logged in, you'll see the main Dashboard. Here's what you need to know:

### Main Navigation (Left Sidebar)

```
Build
├── Compute & AI
│   ├── Workers & Pages
│   ├── Observability
│   ├── Workers for Platforms
│   ├── Containers (Beta)
│   ├── Durable Objects
│   ├── Queues
│   ├── Workflows
│   ├── Browser Rendering
│   ├── AI Search (Beta)
│   ├── Workers AI
│   ├── AI Gateway
│   ├── VPC (Beta)
│   └── Email Service
│
├── Storage & databases
│   ├── R2 object storage
│   ├── Hyperdrive
│   ├── Workers KV
│   ├── D1 SQL database
│   ├── Analytics Engine
│   ├── Pipelines (Beta)
│   ├── Vectorize
│   └── Secrets Store (New)
│
└── [Other sections...]
```

### Key Sections for This Workshop

| Section | Path | What It Does |
|---------|------|-------------|
| **Workers & Pages** | Build → Compute & AI → Workers & Pages | Create and manage serverless functions |
| **R2** | Build → Storage & databases → R2 object storage | Store images and files |
| **D1** | Build → Storage & databases → D1 SQL database | Create and manage databases |
| **Workers AI** | Build → Compute & AI → Workers AI | Use AI models |

---

## Step 4: Navigate to Workers

1. In the left sidebar, click **Workers & Pages**
2. Click **Workers** (or **Overview** if it shows)
3. You should see a button that says **Create application** or **Create a Service**

---

## Step 5: Understand the Dashboard Layout

The Dashboard has these main areas:

### Top Navigation Bar
- **Cloudflare logo** - Click to go home
- **Search** - Find resources quickly
- **Account menu** - Profile, settings, logout

### Left Sidebar
- **All sections** - Navigate to different products
- **Collapse/Expand** - Click the menu icon to toggle

### Main Content Area
- **Current page** - Shows Workers, R2, D1, etc.
- **Action buttons** - Create, edit, delete resources

---

## Step 6: Create Your First Worker

1. Click **Workers & Pages** in the left sidebar
2. Click **Workers** tab
3. Click **Create application** button
4. Click **Create a Worker** (not Pages)
5. You'll see a code editor with a default "Hello World" example

---

## Step 7: Explore the Worker Editor

The Worker editor has:

```
┌─────────────────────────────────────────┐
│ File Name: index.js                     │
├─────────────────────────────────────────┤
│                                         │
│  export default {                       │
│    fetch(request) {                     │
│      return new Response('Hello World') │
│    }                                    │
│  }                                      │
│                                         │
├─────────────────────────────────────────┤
│ [Save and Deploy]  [Test]  [Settings]   │
└─────────────────────────────────────────┘
```

### Key Buttons

| Button | What It Does |
|--------|-------------|
| **Save and Deploy** | Save your code and deploy to the internet |
| **Test** | Test your Worker locally |
| **Settings** | Configure routes, environment variables, etc. |

---

## Step 8: Deploy Your First Worker

1. The default code is already there (Hello World)
2. Click **Save and Deploy** button
3. Wait for the deployment to complete (usually 10-30 seconds)
4. You'll see a success message with your Worker URL

---

## Step 9: Test Your Worker

1. Click the URL that appears after deployment
2. You should see "Hello World" in your browser
3. Your first Worker is live! 🎉

---

## Step 10: Understand Worker URLs

Your Worker gets a unique URL:

```
https://[worker-name].[account-name].workers.dev
```

Example:
```
https://my-first-worker.pongpisit.workers.dev
```

You can:
- Share this URL with anyone
- Access it from any device
- It's live on the internet immediately

---

## Key Takeaways

✅ Created a Cloudflare account  
✅ Navigated the Dashboard  
✅ Created your first Worker  
✅ Deployed to the internet  
✅ Tested your Worker  

---

## Next Steps

Ready to build something more interesting? Go to **[Module 2: Hello World Worker](./02-hello-world-worker.md)** to return HTML and JSON.

---

## Troubleshooting

### Can't see Workers in the sidebar?
- Make sure you're logged in
- Refresh the page
- Try a different browser

### Deployment failed?
- Check your internet connection
- Try clicking "Save and Deploy" again
- Check the error message in red text

### Worker URL not working?
- Wait 30 seconds after deployment
- Try refreshing the page
- Check if the URL is correct

---

## Resources

- [Cloudflare Dashboard](https://dash.cloudflare.com)
- [Workers Documentation](https://developers.cloudflare.com/workers/)
- [Getting Started with Workers](https://developers.cloudflare.com/workers/get-started/)
