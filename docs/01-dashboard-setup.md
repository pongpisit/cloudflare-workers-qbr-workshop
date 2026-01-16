# Module 1: Dashboard Setup (15 minutes)

🎯 **Goal:** Create your Cloudflare account and deploy your first Worker to the internet!

**What you'll do:**
1. Sign up for free Cloudflare account (no credit card needed)
2. Learn where everything is in the Dashboard
3. Create and deploy your first Worker
4. See it live on the internet!

**What you'll learn:**
- How to navigate the Cloudflare Dashboard
- What Workers, R2, and D1 are
- How to deploy code to the internet

---

## Step 1: Create a Cloudflare Account

**📋 Copy this URL and paste it in your browser:**
```
https://dash.cloudflare.com/sign-up
```

**Then follow these steps:**

1. **Enter your email address** (use a real email - you'll need to verify it)
2. **Create a password** (make it strong - at least 8 characters)
3. **Click the blue "Create Account" button**
4. **Check your email inbox** for a message from Cloudflare
5. **Click the "Verify Email" link** in the email

✅ **Success!** You'll be redirected to the Cloudflare Dashboard.

> 💡 **Tip:** You don't need a domain name or credit card for this workshop. Everything is free!

---

## Step 2: First Look at the Dashboard

**What you're seeing:**

After verifying your email, you'll see the **Cloudflare Dashboard**. This is your control center for everything!

**Don't panic!** There are lots of buttons and menus, but we'll only use a few of them. Let me show you what's important.

---

## Step 3: Understanding the Dashboard Layout

**The Dashboard has 3 main areas:**

### 1. Top Bar (across the top)
- **Cloudflare logo** (top left) - Click to go home
- **Search box** (center) - Find things quickly
- **Your account menu** (top right) - Settings and logout

### 2. Left Sidebar (the menu on the left)

This is where you'll find everything. Look for these sections:

**📦 Build** (click to expand)
- **Compute & AI** (click to expand)
  - **Workers & Pages** ← We'll use this!
  - Workers AI ← We'll use this later!
- **Storage & databases** (click to expand)
  - **R2 object storage** ← We'll use this!
  - **D1 SQL database** ← We'll use this!

### 3. Main Content Area (the big space in the middle)
- Shows what you're currently working on
- Has buttons to create new things

---

## Step 4: The Only 4 Things You Need to Find

**For this workshop, you only need to know how to find these 4 things:**

| What | Where to Click | What It Does |
|------|----------------|-------------|
| **Workers & Pages** | Left sidebar → Build → Compute & AI → Workers & Pages | Create your code |
| **R2 Storage** | Left sidebar → Build → Storage & databases → R2 object storage | Store images |
| **D1 Database** | Left sidebar → Build → Storage & databases → D1 SQL database | Store data |
| **Workers AI** | Left sidebar → Build → Compute & AI → Workers AI | Use AI models |

**💡 Tip:** If you can't see these sections, make sure you clicked "Build" in the left sidebar to expand it!

---

## Step 5: Let's Create Your First Worker!

**A Worker is code that runs on Cloudflare's servers.** Think of it like a mini-program that can respond to web requests.

**Follow these exact steps:**

---

### Step 5.1: Navigate to Workers

**Click these in order:**
1. **Build** (in left sidebar)
2. **Compute & AI** (expands to show more options)
3. **Workers & Pages** (click this)

✅ You should now see a page that says "Workers & Pages" at the top.

---

### Step 5.2: Start Creating a Worker

**Look for the blue button that says "Create application"** (top right corner)

**Click it!**

You'll see a dialog box with several options:
- Continue with GitHub
- Connect GitLab  
- **Start with Hello World!** ← Click this one!
- Select a template
- Upload your static files

**Click "Start with Hello World!"**

---

### Step 5.3: Deploy Your Worker

**You'll see a dialog called "Deploy Hello World"**

This shows:
- **Worker name** - A random name like `broad-frost-227d` (you can change it if you want)
- **Code preview** - Shows some JavaScript code (don't worry about understanding it yet!)

**Click the blue "Deploy" button** (bottom right)

⏳ **Wait 10-30 seconds...** You'll see a loading animation.

✅ **Success!** You'll see:
- A green checkmark
- A message saying "Your Worker is now deployed"
- **A URL** that looks like: `https://your-worker-name.your-account.workers.dev`

🎉 **Congratulations!** Your first Worker is now live on the internet!

---

## Step 6: Understanding Your Worker Dashboard

**After deployment, you'll see your Worker's control panel.**

There are several tabs at the top:

| Tab | What It's For |
|-----|---------------|
| **Overview** | Main page - see status and quick actions |
| **Metrics** | See how many people visited your Worker |
| **Deployments** | See history of changes you made |
| **Bindings** | Connect to R2, D1, or AI (we'll do this later!) |
| **Observability** | See errors and logs (for debugging) |
| **Settings** | Advanced settings |

**Important buttons you'll use:**

- **Edit code** - Change your Worker's code
- **Visit** - See your Worker in a browser
- **Save and Deploy** - Save changes and make them live

**💡 You don't need to understand all these tabs yet!** We'll use them as needed.

---

## Step 7: See Your Worker Live!

**Let's see your Worker in action:**

1. **Click the blue "Visit" button** (top right corner)
2. **A new browser tab will open**
3. **You should see "Hello World"** displayed on the page

🎉 **Amazing!** You just deployed code to the internet!

**What just happened?**
- Your Worker is running on Cloudflare's servers (not your computer!)
- It's accessible from anywhere in the world
- It responds to web requests with "Hello World"
- It's running on 300+ servers globally for fast access

---

## Step 8: Understanding Your Worker URL

**Your Worker has a unique web address (URL) that looks like this:**

```
https://[worker-name].[your-account].workers.dev
```

**For example:**
```
https://broad-frost-227d.john-smith.workers.dev
```

**What this means:**
- `broad-frost-227d` = Your Worker's name (randomly generated)
- `john-smith` = Your Cloudflare account name
- `workers.dev` = Cloudflare's domain for Workers

**Cool things about this URL:**
- ✅ It works immediately (no waiting!)
- ✅ Anyone can visit it (it's public)
- ✅ It works from any device (phone, tablet, computer)
- ✅ It's fast everywhere (runs on 300+ servers worldwide)
- ✅ It's free (no hosting costs!)

**💡 Tip:** Copy this URL and send it to a friend - they can see your Worker too!

---

## Key Takeaways

- [x] Created a Cloudflare account  
- [x] Navigated the Dashboard  
- [x] Created your first Worker  
- [x] Deployed to the internet  
- [x] Tested your Worker  

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
