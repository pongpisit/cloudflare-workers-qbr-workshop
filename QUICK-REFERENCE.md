# Quick Reference Guide

**Quick answers to common questions** - Bookmark this page!

---

## 🔍 How to Find Things in the Dashboard

### Workers & Pages
```
Left Sidebar → Build → Compute & AI → Workers & Pages
```

### R2 Storage (for images)
```
Left Sidebar → Build → Storage & databases → R2 object storage
```

### D1 Database (for data)
```
Left Sidebar → Build → Storage & databases → D1 SQL database
```

### Workers AI (for AI models)
```
Left Sidebar → Build → Compute & AI → Workers AI
```

---

## 📋 Copy-Paste Checklist

### Creating a New Worker
- [ ] Go to Workers & Pages
- [ ] Click "Create application"
- [ ] Click "Start with Hello World!"
- [ ] Click "Deploy"
- [ ] Click "Visit" to test

### Editing Your Worker
- [ ] Go to Workers & Pages
- [ ] Click your Worker name
- [ ] Click "Edit code"
- [ ] Copy and paste new code
- [ ] Click "Save and Deploy"
- [ ] Click "Visit" to test

### Creating an R2 Bucket
- [ ] Go to R2 object storage
- [ ] Click "Create bucket"
- [ ] Enter bucket name (lowercase, no spaces)
- [ ] Click "Create bucket"

### Binding R2 to Your Worker
- [ ] Go to your Worker
- [ ] Click "Bindings" tab
- [ ] Click "Add binding"
- [ ] Select "R2 Bucket"
- [ ] Enter variable name (e.g., `BUCKET`)
- [ ] Select your bucket
- [ ] Click "Deploy"

### Creating a D1 Database
- [ ] Go to D1 SQL database
- [ ] Click "Create database"
- [ ] Enter database name
- [ ] Click "Create"

### Binding D1 to Your Worker
- [ ] Go to your Worker
- [ ] Click "Bindings" tab
- [ ] Click "Add binding"
- [ ] Select "D1 database"
- [ ] Enter variable name (e.g., `DB`)
- [ ] Select your database
- [ ] Click "Deploy"

### Adding AI to Your Worker
- [ ] Go to your Worker
- [ ] Click "Bindings" tab
- [ ] Click "Add binding"
- [ ] Select "Workers AI"
- [ ] Enter variable name: `AI`
- [ ] Click "Deploy"

---

## 🐛 Common Problems & Solutions

### "I can't find Workers & Pages"
**Solution:** Click "Build" in the left sidebar to expand it, then look for "Compute & AI"

### "My changes aren't showing"
**Solution:** 
1. Click "Save and Deploy" (not just Save)
2. Wait 30 seconds
3. Refresh your browser (Ctrl+F5 or Cmd+Shift+R)

### "I see a blank page"
**Solution:** Check the browser console (F12) for errors. Most likely you have a typo in your code.

### "Deployment failed"
**Solution:**
1. Check for error messages in red text
2. Make sure you copied ALL the code (including the first and last lines)
3. Try clicking "Save and Deploy" again

### "My Worker URL doesn't work"
**Solution:**
1. Wait 30 seconds after deployment
2. Make sure you're using the correct URL from the Dashboard
3. Try opening it in a private/incognito browser window

### "I can't upload to R2"
**Solution:**
1. Make sure you created an R2 binding in your Worker
2. Check the variable name matches your code (e.g., `env.BUCKET`)
3. Verify the bucket exists in R2 storage

### "Database query failed"
**Solution:**
1. Make sure you created a D1 binding in your Worker
2. Check the table exists (use Console tab in D1)
3. Verify your SQL syntax is correct

### "AI not responding"
**Solution:**
1. Make sure you added the AI binding to your Worker
2. Check the variable name is exactly `AI`
3. AI responses can take 5-10 seconds (be patient!)

---

## 💡 Pro Tips

### Tip 1: Always Test After Changes
After every code change:
1. Click "Save and Deploy"
2. Click "Visit" to test
3. Check if it works as expected

### Tip 2: Use the Browser Console
Press F12 to open developer tools and see errors. This helps you debug issues!

### Tip 3: Copy Code Exactly
When copying code from the workshop:
- Copy the ENTIRE code block (including first and last lines)
- Don't modify variable names unless instructed
- Keep the same indentation

### Tip 4: One Thing at a Time
Don't try to do multiple modules at once. Complete each module fully before moving to the next.

### Tip 5: Save Your Worker URLs
Keep a text file with your Worker URLs so you can easily find them later.

---

## 📝 Code Snippets

### Basic Worker Structure
```javascript
export default {
  async fetch(request, env) {
    // Your code here
    return new Response('Hello World');
  }
};
```

### Return HTML
```javascript
return new Response(`
  <!DOCTYPE html>
  <html>
  <body>
    <h1>Hello World</h1>
  </body>
  </html>
`, {
  headers: { 'Content-Type': 'text/html' }
});
```

### Return JSON
```javascript
return new Response(JSON.stringify({
  message: 'Hello World',
  timestamp: new Date().toISOString()
}), {
  headers: { 'Content-Type': 'application/json' }
});
```

### Get URL Path
```javascript
const url = new URL(request.url);
const path = url.pathname;

if (path === '/') {
  // Home page
}
if (path === '/about') {
  // About page
}
```

### Upload to R2
```javascript
await env.BUCKET.put('filename.jpg', fileData);
```

### Get from R2
```javascript
const file = await env.BUCKET.get('filename.jpg');
```

### Query D1 Database
```javascript
const { results } = await env.DB.prepare(
  'SELECT * FROM table_name'
).all();
```

### Insert into D1
```javascript
await env.DB.prepare(
  'INSERT INTO table_name (column1, column2) VALUES (?, ?)'
).bind(value1, value2).run();
```

### Use Workers AI (Chat)
```javascript
const response = await env.AI.run('@cf/meta/llama-3.1-8b-instruct', {
  messages: [
    { role: 'user', content: 'Hello!' }
  ]
});
```

### Use Workers AI (Image)
```javascript
const image = await env.AI.run(
  '@cf/stabilityai/stable-diffusion-xl-base-1.0',
  { prompt: 'A beautiful sunset' }
);
```

---

## 🔗 Useful Links

### Official Documentation
- [Cloudflare Dashboard](https://dash.cloudflare.com)
- [Workers Docs](https://developers.cloudflare.com/workers/)
- [R2 Docs](https://developers.cloudflare.com/r2/)
- [D1 Docs](https://developers.cloudflare.com/d1/)
- [Workers AI Docs](https://developers.cloudflare.com/workers-ai/)

### Learning Resources
- [Workers Examples](https://developers.cloudflare.com/workers/examples/)
- [AI Models List](https://developers.cloudflare.com/workers-ai/models/)
- [Community Discord](https://discord.cloudflare.com)

---

## 🆘 Need More Help?

### Workshop Modules
- [Module 1: Dashboard Setup](./docs/01-dashboard-setup.md)
- [Module 2: Hello World Worker](./docs/02-hello-world-worker.md)
- [Module 3: Profile Page](./docs/03-profile-page.md)
- [Module 4: R2 Storage](./docs/04-r2-storage.md)
- [Module 5: D1 Database](./docs/05-d1-database.md)
- [Module 6: Photo Gallery](./docs/06-photo-gallery.md)
- [Module 7: Workers AI](./docs/07-workers-ai.md)

### Get Help
- Check the troubleshooting section in each module
- Search [Cloudflare Community](https://community.cloudflare.com)
- Ask in [Discord](https://discord.cloudflare.com)

---

## 📊 Workshop Progress Tracker

Use this to track your progress:

- [ ] Module 1: Created Cloudflare account
- [ ] Module 1: Deployed first Worker
- [ ] Module 2: Created HTML page
- [ ] Module 2: Added JSON endpoint
- [ ] Module 3: Built profile page
- [ ] Module 3: Customized with my info
- [ ] Module 4: Created R2 bucket
- [ ] Module 4: Uploaded profile picture
- [ ] Module 5: Created D1 database
- [ ] Module 5: Ran SQL queries
- [ ] Module 6: Built photo gallery
- [ ] Module 6: Uploaded test photos
- [ ] Module 7: Added AI chatbot
- [ ] Module 7: Generated AI images

**🎉 Completed all modules? Congratulations! You're now a Cloudflare developer!**
