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

## Step 4: Understand the Dashboard Layout

The Dashboard has these main areas:

### Top Navigation Bar
- **Cloudflare logo** – Go home
- **Search** – Find resources quickly
- **Account menu** – Profile, settings, logout

### Left Sidebar
- **Build** → **Compute & AI** – Workers, AI, etc.
- **Storage & databases** – R2, D1, KV, Vectorize
- Use the collapse icon to hide/show sections

### Main Content Area
- Shows the current product (Workers, R2, etc.)
- Includes primary action buttons (Create application, Add binding, etc.)

---

## Step 5: Create Your First Worker

1. Click **Build** → **Compute & AI** → **Workers & Pages**
2. Click the blue **Create application** button (top right)
3. You'll see the "Create a Worker" dialog with options:
   - Continue with GitHub
   - Connect GitLab
   - **Start with Hello World!** (Click this one)
   - Select a template
   - Upload your static files
4. Click **Start with Hello World!**

---

## Step 7: Deploy Your First Worker

1. After clicking "Start with Hello World!", you'll see the "Deploy Hello World" dialog
2. The dialog shows:
   - **Worker name** - Auto-generated name (e.g., `broad-frost-227d`)
   - **Worker preview** - Shows the default Hello World code
3. Click the blue **Deploy** button (bottom right)
4. Wait for the deployment to complete (usually 10-30 seconds)
5. You'll see a success message with your Worker URL

---

## Step 8: Explore the Worker Project Dashboard

After deployment, you'll see the Worker project dashboard with several tabs:

### Dashboard Tabs

| Tab | What It Shows |
|-----|---------------|
| **Overview** | Worker status, metrics, and quick actions |
| **Metrics** | Requests, errors, and CPU time (Last 26 hours) |
| **Deployments** | Deployment history and versions |
| **Bindings** | Connected resources (D1, R2, KV, etc.) |
| **Observability** | Logs and monitoring data |
| **Settings** | Worker configuration and routes |

### Key Buttons

- **Edit code** - Modify your Worker code
- **Visit** - Open your Worker in a browser
- **Domains & Routes** - Configure custom domains

---

## Step 9: Test Your Worker

1. Click the blue **Visit** button (top right)
2. You should see "Hello World" in your browser
3. Your first Worker is live!

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

[x] Created a Cloudflare account  
[x] Navigated the Dashboard  
[x] Created your first Worker  
[x] Deployed to the internet  
[x] Tested your Worker  

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
