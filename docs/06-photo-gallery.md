# Module 6: Photo Gallery App (40 minutes)

Build an Instagram-style photo gallery using R2 and D1. This module creates a **new Worker and new R2 bucket** - do not reuse from previous modules.

---

## What You'll Learn

- Create a new Worker for photo gallery
- Create a new R2 bucket for photos
- Connect Worker to R2 bucket
- Connect Worker to D1 database
- Display images from R2
- Display captions from D1
- Build a complete photo app

---

## Step 1: Create a New R2 Bucket for Photos

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. Click **Build** → **Storage & databases** → **R2 object storage**
3. Click **Create bucket**
4. Enter bucket name: `photo-gallery`
5. Keep default region (Asia Pacific)
6. Click **Create bucket**
7. **No need to enable public access** - we'll serve images through the Worker

---

## Step 2: Create a New Worker for Photo Gallery

1. Go to **Build** → **Compute & AI** → **Workers & Pages**
2. Click **Create application** → **Create Worker**
3. Name it: `photo-gallery`
4. Click **Deploy**
5. You'll see a "Start with Hello World!" template - that's fine, we'll update it later
6. Once deployed, proceed to add bindings in the next step

---

## Step 3: Add R2 Binding to New Worker

1. Click **Bindings** tab at the top (next to Overview, Metrics, Deployments)
2. You'll see the Bindings page with "Add binding" button
3. Click **Add binding** button
4. A modal will appear showing all binding types (Analytics engine, D1 database, KV namespace, R2 bucket, etc.)
5. Click **R2 Bucket** option
6. Click **Add binding** button again at bottom right
7. A form titled "R2 bucket" will appear with two fields:
   - **Variable name**: Enter `BUCKET`
   - **R2 bucket**: Click dropdown and select `photo-gallery`
8. Click **Deploy** button to save and apply
9. You'll see the binding appear in "Connected Bindings"

---

## Step 4: Add D1 Binding to New Worker

1. In the same **Bindings** tab, click **Add binding** again
2. A modal will appear showing all binding types
3. Click **D1 database** option
4. Click **Add binding** button again at bottom right
5. A form titled "Add a D1 database binding" will appear with two fields:
   - **Variable name**: Enter `MY_PHOTOS_DB`
   - **D1 database**: Click dropdown and select `workshop-db` (from Module 5)
6. Click **Deploy** button to save and apply
7. You'll see the second binding appear in "Connected Bindings"

Now you have both bindings configured:
- `BUCKET` → `photo-gallery` R2 bucket
- `MY_PHOTOS_DB` → `workshop-db` D1 database

---

## Step 5: Update Your Worker Code

Go to your Worker and replace all code with this:

```javascript
export default {
  async fetch(request, env) {
    const url = new URL(request.url);

    // Auto-initialize database table on every request
    try {
      await env.MY_PHOTOS_DB.prepare(`
        CREATE TABLE IF NOT EXISTS photos (
          id INTEGER PRIMARY KEY AUTOINCREMENT,
          filename TEXT NOT NULL,
          caption TEXT,
          created_at DATETIME DEFAULT CURRENT_TIMESTAMP
        )
      `).run();
    } catch (e) {
      // Table already exists, ignore error
    }

    if (url.pathname === "/") return showFeed(env);
    
    if (url.pathname === "/upload" && request.method === "POST") {
      const formData = await request.formData();
      const file = formData.get("image");
      const caption = formData.get("caption") || "";
      
      if (!file) return new Response("No file", { status: 400 });
      
      const filename = Date.now() + "-" + file.name;
      await env.BUCKET.put(filename, file.stream(), {
        httpMetadata: { contentType: file.type }
      });
      
      await env.MY_PHOTOS_DB.prepare("INSERT INTO photos (filename, caption) VALUES (?, ?)")
        .bind(filename, caption)
        .run();
      
      return Response.redirect(new URL("/", request.url).toString(), 302);
    }
    
    if (url.pathname.startsWith("/image/")) {
      const object = await env.BUCKET.get(url.pathname.replace("/image/", ""));
      if (!object) return new Response("Not found", { status: 404 });
      return new Response(object.body, {
        headers: { "content-type": object.httpMetadata?.contentType || "image/jpeg" }
      });
    }
    
    if (url.pathname.startsWith("/delete/") && request.method === "POST") {
      const id = url.pathname.replace("/delete/", "");
      const photo = await env.MY_PHOTOS_DB.prepare("SELECT filename FROM photos WHERE id = ?").bind(id).first();
      if (photo) {
        await env.BUCKET.delete(photo.filename);
        await env.MY_PHOTOS_DB.prepare("DELETE FROM photos WHERE id = ?").bind(id).run();
      }
      return Response.redirect(new URL("/", request.url).toString(), 302);
    }
    
    if (url.pathname === "/api/photos") {
      const { results } = await env.MY_PHOTOS_DB.prepare("SELECT * FROM photos ORDER BY created_at DESC").all();
      return Response.json(results);
    }
    
    return new Response("Not found", { status: 404 });
  }
};

async function showFeed(env) {
  const { results: photos } = await env.MY_PHOTOS_DB.prepare(
    "SELECT * FROM photos ORDER BY created_at DESC"
  ).all();
  
  let postsHtml = "";
  if (photos.length === 0) {
    postsHtml = '<div class="empty"><p>No posts yet. Share your first photo!</p></div>';
  } else {
    for (const photo of photos) {
      const date = new Date(photo.created_at);
      const timeAgo = getTimeAgo(date);
      postsHtml += `
        <article class="post">
          <div class="post-header">
            <div class="user-info">
              <div class="avatar">CF</div>
              <div class="user-details">
                <div class="username">You</div>
                <div class="timestamp">${timeAgo}</div>
              </div>
            </div>
            <button class="menu-btn" onclick="if(confirm('Delete this post?')) { document.getElementById('delete-${photo.id}').submit(); }">⋯</button>
          </div>
          <img src="/image/${photo.filename}" alt="" class="post-image">
          <div class="post-actions">
            <button class="action-btn like-btn" title="Like">♡</button>
            <button class="action-btn comment-btn" title="Comment">💬</button>
            <button class="action-btn share-btn" title="Share">↗</button>
          </div>
          <div class="post-content">
            <div class="caption">${photo.caption || ''}</div>
            <div class="timestamp-full">${date.toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' })}</div>
          </div>
          <form id="delete-${photo.id}" action="/delete/${photo.id}" method="POST" style="display:none;"></form>
        </article>`;
    }
  }

  return new Response(`<!DOCTYPE html>
<html>
<head>
  <title>CloudflareGram</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    
    body { 
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif; 
      background: linear-gradient(135deg, #f5f5f5 0%, #fafafa 100%);
      min-height: 100vh;
      color: #262626;
    }
    
    header { 
      background: white; 
      border-bottom: 1px solid #e5e5e5; 
      padding: 16px 20px; 
      position: sticky; 
      top: 0; 
      z-index: 100;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }
    
    .header-content { 
      max-width: 600px; 
      margin: 0 auto; 
      display: flex; 
      justify-content: space-between; 
      align-items: center; 
    }
    
    .logo { 
      font-size: 28px; 
      font-weight: 700; 
      background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      letter-spacing: -1px;
    }
    
    .new-post-btn { 
      background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
      color: white; 
      border: none; 
      padding: 10px 20px; 
      border-radius: 6px; 
      cursor: pointer; 
      font-weight: 600;
      font-size: 14px;
      transition: all 0.2s ease;
    }
    
    .new-post-btn:hover { 
      transform: translateY(-2px);
      box-shadow: 0 4px 12px rgba(246, 130, 31, 0.3);
    }
    
    .new-post-btn:active {
      transform: translateY(0);
    }
    
    .feed { 
      max-width: 600px; 
      margin: 20px auto; 
      padding: 0 20px;
    }
    
    .post { 
      background: white; 
      border: 1px solid #e5e5e5; 
      border-radius: 8px; 
      margin-bottom: 24px; 
      overflow: hidden;
      transition: all 0.2s ease;
    }
    
    .post:hover {
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    }
    
    .post-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 12px 16px;
      border-bottom: 1px solid #f0f0f0;
    }
    
    .user-info {
      display: flex;
      align-items: center;
      gap: 12px;
    }
    
    .avatar {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
      color: white;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 600;
      font-size: 14px;
    }
    
    .user-details {
      display: flex;
      flex-direction: column;
    }
    
    .username {
      font-weight: 600;
      font-size: 14px;
      color: #262626;
    }
    
    .timestamp {
      font-size: 12px;
      color: #8e8e8e;
    }
    
    .menu-btn {
      background: none;
      border: none;
      font-size: 18px;
      cursor: pointer;
      color: #262626;
      padding: 4px 8px;
      transition: all 0.2s ease;
    }
    
    .menu-btn:hover {
      color: #F6821F;
    }
    
    .post-image {
      width: 100%;
      aspect-ratio: 1;
      object-fit: cover;
      display: block;
    }
    
    .post-actions {
      display: flex;
      gap: 12px;
      padding: 12px 16px;
      border-bottom: 1px solid #f0f0f0;
    }
    
    .action-btn {
      background: none;
      border: none;
      font-size: 20px;
      cursor: pointer;
      padding: 4px 8px;
      transition: all 0.2s ease;
      color: #262626;
    }
    
    .action-btn:hover {
      color: #F6821F;
      transform: scale(1.1);
    }
    
    .like-btn:hover {
      color: #ed4956;
    }
    
    .post-content {
      padding: 12px 16px;
    }
    
    .caption {
      font-size: 14px;
      line-height: 1.5;
      color: #262626;
      margin-bottom: 8px;
      word-wrap: break-word;
    }
    
    .timestamp-full {
      font-size: 12px;
      color: #8e8e8e;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
    
    .modal { 
      display: none; 
      position: fixed; 
      inset: 0; 
      background: rgba(0,0,0,0.5); 
      z-index: 200; 
      justify-content: center; 
      align-items: center;
      backdrop-filter: blur(4px);
    }
    
    .modal.active { 
      display: flex; 
    }
    
    .modal-box { 
      background: white; 
      border-radius: 12px; 
      padding: 24px; 
      width: 90%; 
      max-width: 400px;
      box-shadow: 0 20px 60px rgba(0,0,0,0.3);
    }
    
    .modal-box h2 { 
      margin-bottom: 20px; 
      font-size: 20px;
      font-weight: 600;
      color: #262626;
    }
    
    .modal-box input[type="file"] { 
      margin-bottom: 16px;
      padding: 8px;
      border: 1px solid #e5e5e5;
      border-radius: 6px;
      width: 100%;
      cursor: pointer;
    }
    
    .modal-box textarea { 
      width: 100%; 
      height: 100px; 
      border: 1px solid #e5e5e5; 
      border-radius: 6px; 
      padding: 12px; 
      resize: none; 
      font-family: inherit;
      margin-bottom: 16px;
      font-size: 14px;
    }
    
    .modal-box textarea:focus {
      outline: none;
      border-color: #F6821F;
      box-shadow: 0 0 0 3px rgba(246, 130, 31, 0.1);
    }
    
    .modal-box button[type="submit"] { 
      width: 100%; 
      background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
      color: white; 
      border: none; 
      padding: 12px; 
      border-radius: 6px; 
      cursor: pointer; 
      font-weight: 600;
      font-size: 14px;
      transition: all 0.2s ease;
    }
    
    .modal-box button[type="submit"]:hover {
      transform: translateY(-2px);
      box-shadow: 0 4px 12px rgba(246, 130, 31, 0.3);
    }
    
    .close-btn { 
      position: absolute; 
      top: 16px; 
      right: 20px; 
      background: none; 
      border: none; 
      color: white; 
      font-size: 28px; 
      cursor: pointer;
      transition: all 0.2s ease;
    }
    
    .close-btn:hover {
      transform: rotate(90deg);
    }
    
    .empty { 
      text-align: center; 
      padding: 80px 20px;
      color: #8e8e8e;
    }
    
    .empty p {
      font-size: 16px;
    }
    
    @media (max-width: 600px) {
      .header-content {
        padding: 0 12px;
      }
      
      .feed {
        padding: 0 12px;
      }
      
      .post {
        border-radius: 4px;
        margin-bottom: 16px;
      }
    }
  </style>
</head>
<body>
  <header>
    <div class="header-content">
      <div class="logo">CloudflareGram</div>
      <button class="new-post-btn" onclick="document.getElementById('modal').classList.add('active')">+ New Post</button>
    </div>
  </header>
  
  <div id="modal" class="modal" onclick="if(event.target===this)this.classList.remove('active')">
    <button class="close-btn" onclick="document.getElementById('modal').classList.remove('active')">&times;</button>
    <div class="modal-box">
      <h2>Create New Post</h2>
      <form action="/upload" method="POST" enctype="multipart/form-data">
        <input type="file" name="image" accept="image/*" required>
        <textarea name="caption" placeholder="Write a caption..."></textarea>
        <button type="submit">Share</button>
      </form>
    </div>
  </div>
  
  <div class="feed">${postsHtml}</div>
  
  <script>
    function getTimeAgo(date) {
      const now = new Date();
      const seconds = Math.floor((now - date) / 1000);
      const minutes = Math.floor(seconds / 60);
      const hours = Math.floor(minutes / 60);
      const days = Math.floor(hours / 24);
      
      if (seconds < 60) return 'just now';
      if (minutes < 60) return minutes + 'm ago';
      if (hours < 24) return hours + 'h ago';
      if (days < 7) return days + 'd ago';
      return date.toLocaleDateString();
    }
  </script>
</body>
</html>`, { headers: { "content-type": "text/html" } });
}

function getTimeAgo(date) {
  const now = new Date();
  const seconds = Math.floor((now - date) / 1000);
  const minutes = Math.floor(seconds / 60);
  const hours = Math.floor(minutes / 60);
  const days = Math.floor(hours / 24);
  
  if (seconds < 60) return 'just now';
  if (minutes < 60) return minutes + 'm ago';
  if (hours < 24) return hours + 'h ago';
  if (days < 7) return days + 'd ago';
  return date.toLocaleDateString();
}
```

---

## Step 6: Save and Deploy

1. Click **Save and Deploy**
2. Wait for deployment
3. Click your Worker URL
4. You should see your photo gallery!

---

## Step 7: Test Your Photo Gallery

1. Visit your Worker URL
2. Click **New Post** button
3. Upload an image and add a caption
4. Click **Share**
5. Your photo should appear in the feed
6. Try the `/api/photos` endpoint to get JSON data

---

## Key Features

 Upload photos directly from Worker  
 Store images in R2 using binding  
 Store captions in D1 database  
 Display photos in Instagram-style feed  
 Delete photos with confirmation  
 JSON API endpoint (`/api/photos`)  
 Auto-initialize database table  
 Serve images directly from Worker (no public URL needed)  

---

## How It Works

**Upload Flow:**
1. User uploads image via form
2. Worker saves image to R2 bucket using `env.BUCKET.put()`
3. Worker saves filename + caption to D1
4. User is redirected to feed

**Display Flow:**
1. Worker queries D1 for all photos
2. For each photo, creates `<img src="/image/{filename}">`
3. `/image/` endpoint retrieves image from R2 using `env.BUCKET.get()`
4. Image is served with correct content-type

**Delete Flow:**
1. User clicks delete button
2. Worker deletes file from R2
3. Worker deletes record from D1
4. User is redirected to feed

---

## Next Steps

Ready to add AI? Go to **[Module 7: Workers AI Integration](./07-workers-ai.md)** to add a chatbot.

---

## Troubleshooting

### Photos not uploading?
- Check R2 binding is configured correctly
- Verify binding variable name is `BUCKET`
- Check file size is reasonable

### Photos not displaying?
- Verify D1 binding is configured
- Check database has photos table
- Run `SELECT * FROM photos;` in D1 Console
- Check `/image/` endpoint is working

### Delete not working?
- Make sure both R2 and D1 bindings are configured
- Verify filename is stored correctly in database
- Check R2 bucket has the file

### Database error?
- Make sure D1 binding is added in Settings
- Check binding variable name is `MY_PHOTOS_DB`
- Verify database is selected correctly

---

## Dashboard Navigation Summary

```
Cloudflare Dashboard
├── Build (left sidebar)
│   ├── Compute & AI
│   │   ├── Workers & Pages
│   │   │   └── photo-gallery (Worker)
│   │   │       ├── Editor (code)
│   │   │       ├── Settings
│   │   │       │   ├── Bindings
│   │   │       │   │   ├── BUCKET (R2 Bucket)
│   │   │       │   │   └── MY_PHOTOS_DB (D1 Database)
│   │   │       │   └── Variables and Secrets
│   │   │       └── Logs
│   │
│   └── Storage & databases
│       ├── R2 object storage
│       │   └── photo-gallery (Bucket)
│       │       ├── Objects (uploaded images)
│       │       └── Settings
│       │
│       └── D1 SQL database
│           └── workshop-db (Database)
│               ├── Console (SQL editor)
│               ├── Overview (Database ID)
│               └── Settings
```

---

## Resources

- [Workers D1 Binding](https://developers.cloudflare.com/workers/runtime-apis/web-crypto/)
- [D1 SQL API](https://developers.cloudflare.com/d1/platform/client-api/)
- [R2 Documentation](https://developers.cloudflare.com/r2/)
