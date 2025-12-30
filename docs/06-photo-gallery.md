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
7. Go to **Settings** tab
8. Under **Public access**, click **Allow access**
9. Copy your R2 public URL (e.g., `https://pub-xxxxxxxxxxxxxxxx.r2.dev`)

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
   - **Variable name**: `photos`
   - **Resource type**: `R2 Bucket`
   - **Bucket name**: `photo-gallery`
4. Click **Save and deploy**

---

## Step 4: Add D1 Binding to New Worker

1. Click **Settings** tab again
2. Under **Bindings**, click **Add binding**
3. Fill in:
   - **Variable name**: `DB`
   - **Resource type**: `D1 Database`
   - **Database**: Select `workshop-db` (from Module 5)
4. Click **Save and deploy**

---

## Step 5: Add Environment Variable for R2 URL

1. Click **Settings** tab
2. Under **Variables and Secrets**, click **Add variable**
3. Fill in:
   - **Variable name**: `R2_URL`
   - **Value**: Your R2 public URL (e.g., `https://pub-xxxxxxxxxxxxxxxx.r2.dev`)
4. Click **Deploy**

---

## Step 6: Update Your Worker Code

Go to your Worker and replace all code with this:

```javascript
export default {
  async fetch(request, env) {
    const url = new URL(request.url);
    const path = url.pathname;

    // Home page - Photo gallery
    if (path === '/') {
      try {
        // Query photos from D1
        const db = env.DB;
        const result = await db.prepare(
          'SELECT id, filename, caption FROM photos ORDER BY created_at DESC'
        ).all();

        const photos = result.results || [];
        const r2Url = env.R2_URL;

        let html = `
          <!DOCTYPE html>
          <html lang="en">
          <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Photo Gallery</title>
            <style>
              * {
                margin: 0;
                padding: 0;
                box-sizing: border-box;
              }

              body {
                font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
                background: #f5f5f5;
                padding: 20px;
              }

              .container {
                max-width: 1200px;
                margin: 0 auto;
              }

              h1 {
                text-align: center;
                color: #333;
                margin-bottom: 10px;
                font-size: 2.5em;
              }

              .subtitle {
                text-align: center;
                color: #999;
                margin-bottom: 40px;
                font-size: 1.1em;
              }

              .gallery {
                display: grid;
                grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
                gap: 20px;
              }

              .photo-card {
                background: white;
                border-radius: 12px;
                overflow: hidden;
                box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
                transition: transform 0.3s, box-shadow 0.3s;
              }

              .photo-card:hover {
                transform: translateY(-5px);
                box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
              }

              .photo-image {
                width: 100%;
                height: 250px;
                object-fit: cover;
                background: #e0e0e0;
              }

              .photo-info {
                padding: 15px;
              }

              .photo-caption {
                color: #333;
                font-size: 1em;
                line-height: 1.5;
                margin-bottom: 10px;
              }

              .photo-filename {
                color: #999;
                font-size: 0.85em;
              }

              .empty {
                text-align: center;
                padding: 60px 20px;
                color: #999;
              }

              .empty-icon {
                font-size: 4em;
                margin-bottom: 20px;
              }

              @media (max-width: 600px) {
                .gallery {
                  grid-template-columns: 1fr;
                }

                h1 {
                  font-size: 1.8em;
                }
              }
            </style>
          </head>
          <body>
            <div class="container">
              <h1> Photo Gallery</h1>
              <p class="subtitle">Built with Cloudflare Workers, R2, and D1</p>
        `;

        if (photos.length === 0) {
          html += `
            <div class="empty">
              <div class="empty-icon"></div>
              <p>No photos yet. Upload some images to R2 and add captions to D1!</p>
            </div>
          `;
        } else {
          html += '<div class="gallery">';

          for (const photo of photos) {
            const imageUrl = `${r2Url}/${photo.filename}`;
            html += `
              <div class="photo-card">
                <img src="${imageUrl}" alt="${photo.caption}" class="photo-image">
                <div class="photo-info">
                  <div class="photo-caption">${photo.caption || 'No caption'}</div>
                  <div class="photo-filename">${photo.filename}</div>
                </div>
              </div>
            `;
          }

          html += '</div>';
        }

        html += `
            </div>
          </body>
          </html>
        `;

        return new Response(html, {
          headers: { 'Content-Type': 'text/html' }
        });
      } catch (error) {
        return new Response(`
          <!DOCTYPE html>
          <html>
          <head><title>Error</title></head>
          <body>
            <h1>Error loading photos</h1>
            <p>${error.message}</p>
            <p>Make sure D1 is configured in your Worker settings.</p>
          </body>
          </html>
        `, {
          status: 500,
          headers: { 'Content-Type': 'text/html' }
        });
      }
    }

    // API endpoint to get photos as JSON
    if (path === '/api/photos') {
      try {
        const db = env.DB;
        const result = await db.prepare(
          'SELECT id, filename, caption FROM photos ORDER BY created_at DESC'
        ).all();

        return new Response(JSON.stringify({
          success: true,
          count: result.results.length,
          photos: result.results
        }), {
          headers: { 'Content-Type': 'application/json' }
        });
      } catch (error) {
        return new Response(JSON.stringify({
          success: false,
          error: error.message
        }), {
          status: 500,
          headers: { 'Content-Type': 'application/json' }
        });
      }
    }

    return new Response('Not found', { status: 404 });
  }
};
```

---

## Step 7: Save and Deploy

1. Click **Save and Deploy**
2. Wait for deployment
3. Click your Worker URL
4. You should see your photo gallery!

---

## Step 8: Test the Gallery

1. Visit your Worker URL
2. You should see photos from R2 with captions from D1
3. Hover over photos to see the hover effect
4. Try the `/api/photos` endpoint to get JSON data

---

## Step 9: Add More Photos

To add more photos:

1. **Upload images to R2 bucket** (`photo-gallery`)
2. **Add captions to D1** (`workshop-db`)

In D1 Console, run:
```sql
INSERT INTO photos (filename, caption) VALUES
('photo4.jpg', 'Your caption here');
```

Then refresh your gallery page to see the new photo.

---

## Key Features

 Displays images from R2  
 Shows captions from D1  
 Responsive grid layout  
 Hover effects  
 JSON API endpoint  
 Error handling  

---

## Dashboard Navigation Summary

```
Cloudflare Dashboard
├── Build
│   ├── Compute & AI
│   │   ├── Workers & Pages
│   │   │   ├── Your Worker
│   │   │   │   ├── Editor (code)
│   │   │   │   └── Settings (D1 binding)
│   │
│   ├── Storage & databases
│   │   ├── R2 object storage (images)
│   │   └── D1 SQL database (captions)
```

---

## Next Steps

Ready to add AI? Go to **[Module 7: Workers AI Integration](./07-workers-ai.md)** to add a chatbot.

---

## Troubleshooting

### Photos not showing?
- Check R2 URL is correct
- Verify images are uploaded to R2
- Check public access is enabled on R2

### Captions not showing?
- Verify D1 binding is configured
- Check database has photos table
- Run `SELECT * FROM photos;` in D1 Console

### Database error?
- Make sure D1 binding is added in Settings
- Check binding variable name is `DB`
- Verify database is selected correctly

---

## Resources

- [Workers D1 Binding](https://developers.cloudflare.com/workers/runtime-apis/web-crypto/)
- [D1 SQL API](https://developers.cloudflare.com/d1/platform/client-api/)
- [R2 Documentation](https://developers.cloudflare.com/r2/)
