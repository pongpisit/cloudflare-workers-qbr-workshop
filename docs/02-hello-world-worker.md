# Module 2: Hello World Worker (25 minutes)

Create a Worker that returns HTML, JSON, and handles different routes.

---

## What You'll Learn

- Return HTML from a Worker
- Return JSON responses
- Handle different URL routes
- Use the Worker editor

---

## Step 1: Create Your Worker

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. Click **Build** → **Compute & AI** → **Workers & Pages**
3. Click the blue **Create application** button (top right)
4. In the "Create a Worker" dialog, click **Start with Hello World!**
5. You'll see the code editor with a default "Hello World" example

---

## Step 2: Return HTML

Replace the default code with this HTML-returning Worker:

```javascript
export default {
  fetch(request) {
    const url = new URL(request.url);
    const path = url.pathname;

    if (path === '/') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>My Profile</title>
          <style>
            body {
              font-family: Arial, sans-serif;
              max-width: 600px;
              margin: 50px auto;
              padding: 20px;
              background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
              color: white;
            }
            h1 { font-size: 2.5em; margin: 0; }
            p { font-size: 1.1em; margin: 10px 0; }
            .links { margin-top: 30px; }
            a {
              display: inline-block;
              margin: 10px 10px 10px 0;
              padding: 10px 20px;
              background: white;
              color: #667eea;
              text-decoration: none;
              border-radius: 5px;
              font-weight: bold;
            }
            a:hover { background: #f0f0f0; }
          </style>
        </head>
        <body>
          <h1>👋 Hello World!</h1>
          <p>Welcome to my Cloudflare Worker</p>
          <p>This page is running on the edge!</p>
          <div class="links">
            <a href="/api/hello">Get JSON</a>
            <a href="/about">About Page</a>
          </div>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    return new Response('Not found', { status: 404 });
  }
};
```

---

## Step 3: Save and Deploy

1. Click **Save and Deploy** button
2. You'll see the deployment dialog showing:
   - Worker name (auto-generated)
   - Code preview
3. Click the blue **Deploy** button
4. Wait for deployment to complete (usually 10-30 seconds)
5. You'll see a success message with your Worker URL
6. Click the Worker URL to test
7. You should see your styled profile page

---

## Step 4: Add JSON Endpoint

Now let's add a `/api/hello` endpoint that returns JSON. Replace the code with:

```javascript
export default {
  fetch(request) {
    const url = new URL(request.url);
    const path = url.pathname;

    // Home page - return HTML
    if (path === '/') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>My Profile</title>
          <style>
            body {
              font-family: Arial, sans-serif;
              max-width: 600px;
              margin: 50px auto;
              padding: 20px;
              background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
              color: white;
            }
            h1 { font-size: 2.5em; margin: 0; }
            p { font-size: 1.1em; margin: 10px 0; }
            .links { margin-top: 30px; }
            a {
              display: inline-block;
              margin: 10px 10px 10px 0;
              padding: 10px 20px;
              background: white;
              color: #667eea;
              text-decoration: none;
              border-radius: 5px;
              font-weight: bold;
            }
            a:hover { background: #f0f0f0; }
          </style>
        </head>
        <body>
          <h1>👋 Hello World!</h1>
          <p>Welcome to my Cloudflare Worker</p>
          <p>This page is running on the edge!</p>
          <div class="links">
            <a href="/api/hello">Get JSON</a>
            <a href="/about">About Page</a>
          </div>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    // JSON API endpoint
    if (path === '/api/hello') {
      return new Response(JSON.stringify({
        message: 'Hello from Cloudflare Workers!',
        timestamp: new Date().toISOString(),
        location: 'Edge Network'
      }), {
        headers: { 'Content-Type': 'application/json' }
      });
    }

    // About page
    if (path === '/about') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>About</title>
          <style>
            body {
              font-family: Arial, sans-serif;
              max-width: 600px;
              margin: 50px auto;
              padding: 20px;
              background: #f5f5f5;
            }
            h1 { color: #667eea; }
            a { color: #667eea; text-decoration: none; }
            a:hover { text-decoration: underline; }
          </style>
        </head>
        <body>
          <h1>About This Worker</h1>
          <p>This is a simple Cloudflare Worker built in the Dashboard.</p>
          <p>It demonstrates:</p>
          <ul>
            <li>Returning HTML</li>
            <li>Returning JSON</li>
            <li>Handling different routes</li>
            <li>Styling with CSS</li>
          </ul>
          <p><a href="/">← Back to Home</a></p>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    // 404 Not Found
    return new Response('Page not found', { status: 404 });
  }
};
```

---

## Step 5: Save and Test

1. Click **Save and Deploy**
2. Wait for deployment
3. Test the different routes:
   - `https://[your-worker].workers.dev/` - Home page

---

## Step 6: Understand the Code

### URL Routing
```javascript
const url = new URL(request.url);
const path = url.pathname;

if (path === '/') { /* home */ }
if (path === '/api/hello') { /* JSON */ }
if (path === '/about') { /* about */ }
```

### Returning HTML
```javascript
return new Response(`<html>...</html>`, {
  headers: { 'Content-Type': 'text/html' }
});
```

### Returning JSON
```javascript
return new Response(JSON.stringify({
  message: 'Hello'
}), {
  headers: { 'Content-Type': 'application/json' }
});
```

---

## Step 7: Customize Your Profile

Edit the HTML to add your own information:

```javascript
<h1>👋 Your Name Here</h1>
<p>Your bio or description</p>
<p>Your location or role</p>
```

---

## Key Takeaways

✅ Created a Worker with multiple routes  
✅ Returned HTML with styling  
✅ Returned JSON responses  
✅ Handled 404 errors  
✅ Customized the profile page  

---

## Next Steps

Ready to store files and data? Go to **[Module 3: Profile Page](./03-profile-page.md)** to add more styling and features.

---

## Troubleshooting

### Changes not showing?
- Click **Save and Deploy** again
- Wait 30 seconds
- Refresh your browser (Ctrl+F5 or Cmd+Shift+R)

### JSON not displaying?
- Check the URL is exactly `/api/hello`
- Make sure the header is `'Content-Type': 'application/json'`

### Styling not working?
- Make sure CSS is inside `<style>` tags
- Check for typos in color codes
- Try a different browser

---

## Resources

- [Workers Documentation](https://developers.cloudflare.com/workers/)
- [Request/Response API](https://developers.cloudflare.com/workers/runtime-apis/web-crypto/)
- [URL API](https://developer.mozilla.org/en-US/docs/Web/API/URL)
