# Module 3: Profile Page (30 minutes)

Enhance your Worker with a beautiful personal profile page with advanced styling.

---

## What You'll Learn

- Create a professional profile page
- Use CSS Grid for layout
- Add interactive elements
- Style with modern CSS

---

## Step 1: Open Your Worker for Editing

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. Click **Build** → **Compute & AI** → **Workers & Pages**
3. Click on your Worker name (created in Module 2)
4. Click the **Edit code** button

---

## Step 2: Update Your Worker with Profile Page

Replace all code with this enhanced profile page:

```javascript
export default {
  fetch(request) {
    const url = new URL(request.url);
    const path = url.pathname;

    if (path === '/') {
      return new Response(`
        <!DOCTYPE html>
        <html lang="en">
        <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
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

              .name {
                font-size: 1.5em;
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
                <a href="#" class="social-icon" title="Email">@</a>
              </div>
            </div>

            <div class="footer">
              Built with Cloudflare Workers 
            </div>
          </div>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    if (path === '/portfolio') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>Portfolio</title>
          <style>
            body { font-family: Arial; max-width: 600px; margin: 50px auto; padding: 20px; }
            h1 { color: #FF6633; }
            a { color: #FF6633; text-decoration: none; }
          </style>
        </head>
        <body>
          <h1> Portfolio</h1>
          <p>My work and projects showcase.</p>
          <p><a href="/">Back to Profile</a></p>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    if (path === '/projects') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>Projects</title>
          <style>
            body { font-family: Arial; max-width: 600px; margin: 50px auto; padding: 20px; }
            h1 { color: #FF6633; }
            a { color: #FF6633; text-decoration: none; }
          </style>
        </head>
        <body>
          <h1> Projects</h1>
          <p>Check out my latest projects built with Cloudflare Workers.</p>
          <p><a href="/">Back to Profile</a></p>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    if (path === '/blog') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>Blog</title>
          <style>
            body { font-family: Arial; max-width: 600px; margin: 50px auto; padding: 20px; }
            h1 { color: #FF6633; }
            a { color: #FF6633; text-decoration: none; }
          </style>
        </head>
        <body>
          <h1> Blog</h1>
          <p>Articles about web development and Cloudflare.</p>
          <p><a href="/">Back to Profile</a></p>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    if (path === '/contact') {
      return new Response(`
        <!DOCTYPE html>
        <html>
        <head>
          <title>Contact</title>
          <style>
            body { font-family: Arial; max-width: 600px; margin: 50px auto; padding: 20px; }
            h1 { color: #FF6633; }
            a { color: #FF6633; text-decoration: none; }
          </style>
        </head>
        <body>
          <h1> Contact Me</h1>
          <p>Get in touch with me for collaborations or inquiries.</p>
          <p>Email: your.email@example.com</p>
          <p><a href="/">Back to Profile</a></p>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    return new Response('Page not found', { status: 404 });
  }
};
```

---

## Step 3: Save and Deploy

1. Click **Save and Deploy**
2. Wait for deployment
3. Click your Worker URL to see the profile page

---

## Step 4: Customize Your Profile

Edit these values in the code:

```javascript
<div class="avatar">‍</div>           // Change emoji
<div class="name">Your Name</div>       // Your name
<div class="title">Full Stack Developer</div>  // Your title
<div class="location"> Your City, Country</div>  // Your location
```

---

## Step 5: Add Your Links

Update the social media links:

```javascript
<a href="https://github.com/yourname" class="social-icon"></a>
<a href="https://twitter.com/yourname" class="social-icon"></a>
```

---

## Key Features

 Responsive design (works on mobile)  
 Gradient background  
 Hover effects on buttons  
 Professional styling  
 Multiple pages  
 Social media links  

---

## Next Steps

Ready to store files? Go to **[Module 4: R2 Storage Setup](./04-r2-storage.md)** to upload images.

---

## Resources

- [CSS Gradients](https://developer.mozilla.org/en-US/docs/Web/CSS/gradient)
- [CSS Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout)
- [Responsive Design](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design)
