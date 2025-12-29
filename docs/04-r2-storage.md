# Module 4: R2 Storage Setup (30 minutes)

Create an R2 bucket, upload your profile picture, and display it on your profile page.

---

## What You'll Learn

- Create an R2 bucket
- Upload your profile picture from your device
- Configure public access
- Display your profile picture on your profile page
- Get image URLs

---

## Step 1: Navigate to R2

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. In the left sidebar, click **Build** → **Storage & databases** → **R2 object storage**
3. You'll see the R2 overview page

---

## Step 2: Create an R2 Bucket

1. Click **Create bucket** button
2. Fill in the bucket creation form:

### Bucket Name
- Enter a name (e.g., `my-photos`, `workshop-images`, `mybucket`)
- Must be lowercase
- Can contain hyphens
- Must be globally unique
- Bucket name is permanent

### Location
- **Automatic** (recommended) - Cloudflare chooses the best location
  - Provides a location hint (e.g., Asia-Pacific for users in Asia)
- **Specify jurisdiction** - Choose a specific region for data residency

### Default Storage Class
- **Standard** (recommended) - For objects accessed at least once a month
- **Infrequent Access** - For objects accessed less than once a month

### Public Access
- By default, buckets are **private**
- You can make them public later in Settings if needed

3. Click **Create bucket**
4. Wait for the bucket to be created (usually 10-30 seconds)

---

## Step 3: Navigate Your Bucket

After creating your bucket, you'll see the bucket details page with three main tabs:

### Objects Tab
- Shows all files in your bucket
- Empty when first created
- Has an **Add directory** button to organize files
- Shows upload area with "Drag and drop or select from computer"
- Displays file list with Type, Storage Class, Size, and Modified date

### Metrics Tab
- Shows usage statistics
- Class A Operations (uploads, deletes)
- Class B Operations (downloads, list requests)
- Bucket size information

### Settings Tab
- **General** section shows:
  - Bucket name
  - Created date
  - Location (e.g., Asia-Pacific APAC)
  - S3 API endpoint
- **Custom Domains** - Expose bucket contents via custom domain
- **Public Development URL** - Enable public access for testing
- **R2 Data Catalog** - Add Apache Iceberg REST catalog
- **CORS Policy** - Configure cross-origin requests
- **Object Lifecycle Rules** - Auto-delete old files
- **Bucket Lock Rules** - Prevent deletion
- **Event Notifications** - Trigger actions on uploads
- **On Demand Migration** - Migrate from other providers
- **Default Storage Class** - Change storage tier
- **Delete Bucket** - Remove the bucket

---

## Step 4: Configure Public Access

By default, R2 buckets are private. To make your profile picture publicly accessible:

1. Click on your bucket name
2. Click **Settings** tab
3. Find **Public Development URL** section
4. Click **Enable** button
5. Your bucket is now publicly accessible!

The public URL will be displayed (e.g., `https://pub-xxxxxxxxxxxxxxxx.r2.dev`)

---

## Step 5: Bind Your Worker with R2 (Important!)

To access your R2 bucket from your Worker code, you need to create a binding. This allows your Worker to read and write files to R2.

### Prerequisites
- You must have created an R2 bucket (Step 2)
- Your Worker must be deployed (Module 3)

### Steps to Bind R2 to Your Worker

1. Go to your Worker in the Dashboard:
   - Click **Build** → **Compute & AI** → **Workers & Pages**
   - Click on your Worker name

2. Click the **Bindings** tab

3. Click **Add binding** button

4. In the "Add a binding" dialog:
   - On the left, select **R2 Bucket** from the list
   - On the right side, fill in:
     - **Variable name**: `mybucket` (or your preferred name)
     - **R2 bucket**: Select your bucket from the dropdown
   - Click **Add Binding**

5. Your R2 bucket is now bound to your Worker!

### Using R2 in Your Worker Code

Once bound, you can access your R2 bucket in your Worker code:

```javascript
export default {
  async fetch(request, env) {
    // Access your R2 bucket
    const bucket = env.mybucket;
    
    // Example: Get a file
    const file = await bucket.get('profile-picture.jpg');
    
    // Example: List files
    const list = await bucket.list();
    
    return new Response('R2 is connected!');
  }
};
```

The variable name you chose (`mybucket`) becomes `env.mybucket` in your Worker code.

---

## Step 6: Add Upload Button to Your Profile Page

Now that R2 is bound to your Worker, copy and paste this complete code into your Worker. Replace `https://pub-xxxxxxxxxxxxxxxx.r2.dev` with your actual R2 public URL from Step 4.

```javascript
export default {
  async fetch(request, env) {
    const url = new URL(request.url);
    const path = url.pathname;

    if (path === '/') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>My Profile</title>
          <style>
            * {
              margin: 0;
              padding: 0;
              box-sizing: border-box;
            }

            body {
              font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
              background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
              min-height: 100vh;
              display: flex;
              align-items: center;
              justify-content: center;
              padding: 20px;
            }

            .container {
              background: white;
              border-radius: 20px;
              box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
              max-width: 500px;
              width: 100%;
              overflow: hidden;
            }

            .header {
              background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
              padding: 40px 20px;
              text-align: center;
              color: white;
            }

            .avatar {
              width: 120px;
              height: 120px;
              border-radius: 50%;
              background: white;
              margin: 0 auto 20px;
              display: flex;
              align-items: center;
              justify-content: center;
              font-size: 60px;
              box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
              overflow: hidden;
              object-fit: cover;
            }

            .avatar img {
              width: 100%;
              height: 100%;
              object-fit: cover;
              border-radius: 50%;
            }

            .name {
              font-size: 2em;
              font-weight: bold;
              margin-bottom: 10px;
            }

            .title {
              font-size: 1.1em;
              opacity: 0.9;
              margin-bottom: 5px;
            }

            .location {
              font-size: 0.95em;
              opacity: 0.8;
            }

            .content {
              padding: 40px 20px;
            }

            .bio {
              color: #333;
              line-height: 1.6;
              margin-bottom: 30px;
              text-align: center;
            }

            .links {
              display: grid;
              gap: 12px;
            }

            .link-btn {
              display: block;
              padding: 15px 20px;
              background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
              color: white;
              text-decoration: none;
              border-radius: 10px;
              text-align: center;
              font-weight: 600;
              transition: transform 0.2s, box-shadow 0.2s;
              border: none;
              cursor: pointer;
              font-size: 1em;
            }

            .link-btn:hover {
              transform: translateY(-2px);
              box-shadow: 0 10px 20px rgba(246, 130, 31, 0.3);
            }

            .link-btn:active {
              transform: translateY(0);
            }

            .social {
              display: flex;
              justify-content: center;
              gap: 15px;
              margin-top: 30px;
              padding-top: 30px;
              border-top: 1px solid #eee;
            }

            .social-icon {
              width: 40px;
              height: 40px;
              border-radius: 50%;
              background: #f0f0f0;
              display: flex;
              align-items: center;
              justify-content: center;
              text-decoration: none;
              color: #667eea;
              font-size: 1.2em;
              transition: all 0.2s;
            }

            .social-icon:hover {
              background: #667eea;
              color: white;
              transform: scale(1.1);
            }

            .upload-section {
              margin-top: 30px;
              padding: 20px;
              background: #f9f9f9;
              border-radius: 10px;
              text-align: center;
            }

            .upload-section h3 {
              margin-bottom: 15px;
              color: #333;
              font-size: 1.1em;
            }

            .upload-input {
              display: none;
            }

            .upload-btn {
              display: inline-block;
              padding: 10px 20px;
              background: linear-gradient(135deg, #F6821F 0%, #FF6633 100%);
              color: white;
              border: none;
              border-radius: 5px;
              cursor: pointer;
              font-weight: 600;
              transition: transform 0.2s, box-shadow 0.2s;
            }

            .upload-btn:hover {
              transform: translateY(-2px);
              box-shadow: 0 5px 15px rgba(255, 102, 51, 0.3);
            }

            .upload-status {
              margin-top: 10px;
              font-size: 0.9em;
              color: #666;
            }

            .footer {
              text-align: center;
              padding: 20px;
              background: #f9f9f9;
              color: #999;
              font-size: 0.9em;
            }

            @media (max-width: 600px) {
              .header {
                padding: 30px 15px;
              }
              .content {
                padding: 30px 15px;
              }
            }
          </style>
        </head>
        <body>
          <div class="container">
            <div class="header">
              <div class="avatar">[Profile]</div>
              <div class="name">Your Name</div>
              <div class="title">Full Stack Developer</div>
              <div class="location">Your City, Country</div>
            </div>

            <div class="content">
              <div class="bio">
                <p>Welcome to my profile! I'm passionate about building amazing web applications with Cloudflare Workers.</p>
              </div>

              <div class="links">
                <a href="/portfolio" class="link-btn">Portfolio</a>
                <a href="/projects" class="link-btn">Projects</a>
                <a href="/blog" class="link-btn">Blog</a>
                <a href="/contact" class="link-btn">Contact Me</a>
              </div>

              <div class="social">
                <a href="#" class="social-icon" title="GitHub">GH</a>
                <a href="#" class="social-icon" title="Twitter">TW</a>
                <a href="#" class="social-icon" title="LinkedIn">IN</a>
                <a href="#" class="social-icon" title="Instagram">IG</a>
              </div>

              <div class="upload-section">
                <h3>Upload Profile Picture</h3>
                <input type="file" id="profileUpload" class="upload-input" accept="image/*">
                <button class="upload-btn" onclick="document.getElementById('profileUpload').click()">
                  Choose Image
                </button>
                <div class="upload-status" id="uploadStatus"></div>
              </div>
            </div>

            <div class="footer">
              Built with Cloudflare Workers
            </div>
          </div>

          <script>
            document.getElementById('profileUpload').addEventListener('change', async (e) => {
              const file = e.target.files[0];
              if (!file) return;

              const statusDiv = document.getElementById('uploadStatus');
              statusDiv.textContent = 'Uploading...';

              try {
                const formData = new FormData();
                formData.append('file', file);

                const response = await fetch('/upload', {
                  method: 'POST',
                  body: formData
                });

                if (response.ok) {
                  const data = await response.json();
                  statusDiv.textContent = 'Upload successful! Refresh to see your new profile picture.';
                  
                  const avatar = document.querySelector('.avatar');
                  if (avatar) {
                    const img = avatar.querySelector('img');
                    if (img) {
                      img.src = data.url;
                    } else {
                      avatar.innerHTML = '<img src="' + data.url + '" alt="Profile Picture">';
                    }
                  }
                } else {
                  statusDiv.textContent = 'Upload failed. Please try again.';
                }
              } catch (error) {
                statusDiv.textContent = 'Error uploading file.';
                console.error('Upload error:', error);
              }
            });
          </script>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    if (path === '/upload' && request.method === 'POST') {
      const formData = await request.formData();
      const file = formData.get('file');
      
      if (!file) {
        return new Response(JSON.stringify({ error: 'No file provided' }), { status: 400 });
      }

      try {
        const filename = 'profile-' + Date.now() + '-' + file.name;
        await env.mybucket.put(filename, file);
        
        const publicUrl = 'https://pub-xxxxxxxxxxxxxxxx.r2.dev/' + filename;
        return new Response(JSON.stringify({ url: publicUrl }), {
          headers: { 'Content-Type': 'application/json' }
        });
      } catch (error) {
        return new Response(JSON.stringify({ error: 'Upload failed' }), { status: 500 });
      }
    }

    return new Response('Page not found', { status: 404 });
  }
};
```

---

## Step 7: Test the Upload Feature

1. Go to your profile page
2. Click the "Choose Image" button
3. Select an image from your device
4. The image will upload to R2 and display on your profile
5. Refresh the page to see the new profile picture

---

## Step 8: Display Your Profile Picture on Your Profile Page

Now let's update Module 3 to display your R2 profile picture:

1. Go back to your Worker (Module 3)
2. Click **Edit code**
3. Find the avatar section:
   ```javascript
   <div class="avatar">[Profile]</div>
   ```
4. Replace it with your R2 image URL:
   ```javascript
   <div class="avatar">
     <img src="https://pub-xxxxxxxxxxxxxxxx.r2.dev/profile-picture.jpg" alt="Profile Picture">
   </div>
   ```
5. Replace the URL with your actual R2 public URL
6. Click **Save and Deploy**
7. Visit your profile page to see your picture displayed!

The image will automatically resize to fit the circular profile picture frame.

---

## Step 8: Get Image URLs

Once uploaded, you can access images at:

```
https://pub-xxxxxxxxxxxxxxxx.r2.dev/[filename]
```

Example:
```
https://pub-xxxxxxxxxxxxxxxx.r2.dev/photo1.jpg
```

---

## Step 7: Test Image Access

1. Copy an image URL
2. Paste it in a new browser tab
3. The image should display
4. If it doesn't, check:
   - Public access is enabled
   - Filename is correct
   - File was uploaded successfully

---

## Step 8: Understand R2 Pricing

R2 is very affordable:

| Item | Cost |
|------|------|
| Storage | $0.015 per GB/month |
| Upload | Free |
| Download | Free (first 10GB/month) |
| Requests | $0.36 per million |

For this workshop, you'll stay in the free tier.

---

## Key Takeaways

 Created an R2 bucket  
 Configured public access  
 Uploaded images  
 Got image URLs  
 Tested image access  

---

## Dashboard Navigation Summary

```
Cloudflare Dashboard
├── Build (left sidebar)
│   ├── Storage & databases
│   │   ├── R2 object storage
│   │   │   ├── Create bucket
│   │   │   ├── Settings (public access)
│   │   │   ├── Objects (upload files)
│   │   │   └── Public URL
```

---

## Next Steps

Ready to add a database? Go to **[Module 5: D1 Database Setup](./05-d1-database.md)** to create a database for captions.

---

## Troubleshooting

### Can't upload files?
- Check file size (R2 supports up to 5GB per file)
- Try a different browser
- Check internet connection

### Images not displaying?
- Verify public access is enabled
- Check the URL is correct
- Wait 30 seconds for CDN cache

### Bucket name already taken?
- R2 bucket names are globally unique
- Try adding a random number or date
- Example: `my-photos-2024-01-15`

---

## Resources

- [R2 Documentation](https://developers.cloudflare.com/r2/)
- [R2 Pricing](https://www.cloudflare.com/products/r2/)
- [Getting Started with R2](https://developers.cloudflare.com/r2/get-started/)
