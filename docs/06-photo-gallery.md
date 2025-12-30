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
5. Click **Edit code**

---

## Step 3: Add R2 Binding to New Worker

1. Click **Settings** tab
2. Under **Bindings**, click **Add binding**
3. Fill in:
   - **Variable name**: `BUCKET`
   - **Resource type**: `R2 Bucket`
   - **Bucket name**: `photo-gallery`
4. Click **Save and deploy**

---

## Step 4: Add D1 Binding to New Worker

1. Click **Settings** tab again
2. Under **Bindings**, click **Add binding**
3. Fill in:
   - **Variable name**: `MY_PHOTOS_DB`
   - **Resource type**: `D1 Database`
   - **Database**: Select `workshop-db` (from Module 5)
4. Click **Save and deploy**

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
    postsHtml = '<p class="empty">No posts yet. Share your first photo!</p>';
  } else {
    for (const photo of photos) {
      const date = new Date(photo.created_at).toLocaleDateString();
      postsHtml += `
        <article class="post">
          <img src="/image/${photo.filename}" alt="">
          <div class="post-content">
            <p class="caption">${photo.caption || ""}</p>
            <p class="date">${date}</p>
            <form action="/delete/${photo.id}" method="POST" class="delete-form">
              <button type="submit" onclick="return confirm('Delete this post?')">Delete</button>
            </form>
          </div>
        </article>`;
    }
  }

  return new Response(`<!DOCTYPE html>
<html>
<head>
  <title>PhotoGram</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: system-ui, sans-serif; background: #fafafa; min-height: 100vh; }
    
    header { background: white; border-bottom: 1px solid #dbdbdb; padding: 12px 20px; position: sticky; top: 0; z-index: 100; }
    .header-content { max-width: 600px; margin: 0 auto; display: flex; justify-content: space-between; align-items: center; }
    .logo { font-size: 24px; font-weight: 600; font-style: italic; }
    
    .new-post-btn { background: #0095f6; color: white; border: none; padding: 8px 16px; border-radius: 8px; cursor: pointer; font-weight: 600; }
    .new-post-btn:hover { background: #1877f2; }
    
    .feed { max-width: 600px; margin: 20px auto; padding: 0 20px; }
    
    .post { background: white; border: 1px solid #dbdbdb; border-radius: 8px; margin-bottom: 20px; overflow: hidden; }
    .post img { width: 100%; aspect-ratio: 1; object-fit: cover; }
    .post-content { padding: 15px; }
    .caption { font-size: 14px; line-height: 1.5; margin-bottom: 8px; }
    .date { font-size: 12px; color: #8e8e8e; margin-bottom: 10px; }
    .delete-form button { background: none; border: none; color: #ed4956; cursor: pointer; font-size: 12px; }
    .delete-form button:hover { text-decoration: underline; }
    
    .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 200; justify-content: center; align-items: center; }
    .modal.active { display: flex; }
    .modal-box { background: white; border-radius: 12px; padding: 20px; width: 90%; max-width: 400px; }
    .modal-box h2 { margin-bottom: 15px; font-size: 18px; }
    .modal-box input[type="file"] { margin-bottom: 15px; }
    .modal-box textarea { width: 100%; height: 80px; border: 1px solid #dbdbdb; border-radius: 8px; padding: 10px; resize: none; font-family: inherit; margin-bottom: 15px; }
    .modal-box button[type="submit"] { width: 100%; background: #0095f6; color: white; border: none; padding: 10px; border-radius: 8px; cursor: pointer; font-weight: 600; }
    .close-btn { position: absolute; top: 15px; right: 20px; background: none; border: none; color: white; font-size: 30px; cursor: pointer; }
    
    .empty { text-align: center; padding: 60px 20px; color: #8e8e8e; }
  </style>
</head>
<body>
  <header>
    <div class="header-content">
      <div class="logo">PhotoGram</div>
      <button class="new-post-btn" onclick="document.getElementById('modal').classList.add('active')">New Post</button>
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
</body>
</html>`, { headers: { "content-type": "text/html" } });
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

## Resources

- [Workers D1 Binding](https://developers.cloudflare.com/workers/runtime-apis/web-crypto/)
- [D1 SQL API](https://developers.cloudflare.com/d1/platform/client-api/)
- [R2 Documentation](https://developers.cloudflare.com/r2/)
