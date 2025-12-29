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
2. Enter a bucket name (e.g., `my-photos`, `workshop-images`)
   - Must be lowercase
   - Can contain hyphens
   - Must be globally unique
3. Choose a region (pick the closest to you)
4. Click **Create bucket**
5. Wait for the bucket to be created (usually 10-30 seconds)

---

## Step 3: Configure Public Access

By default, R2 buckets are private. Let's make it public:

1. Click on your bucket name
2. Click **Settings** tab
3. Scroll down to **Public access**
4. Click **Allow public access**
5. You'll see a confirmation dialog
6. Click **Allow public access** again to confirm

---

## Step 4: Get Your R2 Public URL

1. In the **Settings** tab, look for **Public access URL**
2. You'll see something like:
   ```
   https://pub-xxxxxxxxxxxxxxxx.r2.dev
   ```
3. Copy this URL (you'll need it later)

---

## Step 5: Upload Your Profile Picture

Upload your profile picture from your laptop or mobile phone:

### Option A: Upload via Dashboard (Easiest)

1. Click the **Objects** tab in your bucket
2. Click **Upload** button
3. Select your profile picture from your device
4. Wait for upload to complete
5. You'll see the file listed

### Option B: Drag and Drop

1. Click the **Objects** tab
2. Drag your profile picture from your computer/phone into the browser
3. Drop it in the upload area
4. File will upload automatically

**Recommended:** Use a square image (e.g., 500x500px) for best results as a profile picture.

---

## Step 6: Get Your Profile Picture URL

1. In the **Objects** tab, find your uploaded profile picture
2. Click on the file to see its details
3. Copy the public URL (should look like):
   ```
   https://pub-xxxxxxxxxxxxxxxx.r2.dev/profile-picture.jpg
   ```

---

## Step 7: Display Your Profile Picture on Your Profile Page

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
