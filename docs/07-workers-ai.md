# Module 7: Workers AI Integration (35 minutes)

Add an AI chatbot to your Worker using Cloudflare's Workers AI.

---

## What You'll Learn

- Enable Workers AI
- Use AI models in your Worker
- Build a chat interface
- Handle AI responses

---

## Step 1: Create a New Worker for the AI Chatbot

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. Click **Build** → **Compute & AI** → **Workers & Pages**
3. Click **Create application** → **Create Worker**
4. Name it: `ai-chatbot` (or any name you like)
5. Click **Deploy**
6. You'll see the default Hello World preview — leave it for now
7. Stay on this new Worker; we'll add bindings next

---

## Step 2: Add AI Binding

1. Click the **Bindings** tab at the top (next to Overview, Metrics, Deployments)
2. Click **Add binding**
3. A modal with binding types will appear — select **AI**
4. Click **Add binding** in the modal footer
5. In the “AI binding” form, fill in:
   - **Variable name**: `AI`
6. Click **Deploy** to save the binding

---

## Step 3: Update Your Worker Code

Replace your Worker code with this AI chatbot:

```javascript
export default {
  async fetch(request, env) {
    const url = new URL(request.url);
    const path = url.pathname;

    // Chat page
    if (path === '/') {
      return new Response(`
        <!DOCTYPE html>
        <html lang="en">
        <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>AI Chatbot</title>
          <style>
            * {
              margin: 0;
              padding: 0;
              box-sizing: border-box;
            }

            body {
              font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
              background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
              min-height: 100vh;
              display: flex;
              align-items: center;
              justify-content: center;
              padding: 20px;
            }

            .chat-container {
              background: white;
              border-radius: 15px;
              box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
              width: 100%;
              max-width: 500px;
              display: flex;
              flex-direction: column;
              height: 600px;
            }

            .chat-header {
              background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
              color: white;
              padding: 20px;
              border-radius: 15px 15px 0 0;
              text-align: center;
            }

            .chat-header h1 {
              font-size: 1.5em;
              margin-bottom: 5px;
            }

            .chat-header p {
              font-size: 0.9em;
              opacity: 0.9;
            }

            .messages {
              flex: 1;
              overflow-y: auto;
              padding: 20px;
              display: flex;
              flex-direction: column;
              gap: 15px;
            }

            .message {
              display: flex;
              gap: 10px;
              animation: slideIn 0.3s ease-out;
            }

            @keyframes slideIn {
              from {
                opacity: 0;
                transform: translateY(10px);
              }
              to {
                opacity: 1;
                transform: translateY(0);
              }
            }

            .message.user {
              justify-content: flex-end;
            }

            .message-content {
              max-width: 80%;
              padding: 12px 16px;
              border-radius: 12px;
              line-height: 1.5;
            }

            .message.user .message-content {
              background: #667eea;
              color: white;
              border-radius: 12px 0 12px 12px;
            }

            .message.ai .message-content {
              background: #f0f0f0;
              color: #333;
              border-radius: 0 12px 12px 12px;
            }

            .message.ai .avatar {
              font-size: 1.5em;
            }

            .input-area {
              padding: 20px;
              border-top: 1px solid #eee;
              display: flex;
              gap: 10px;
            }

            input {
              flex: 1;
              padding: 12px 16px;
              border: 1px solid #ddd;
              border-radius: 25px;
              font-size: 1em;
              outline: none;
              transition: border-color 0.2s;
            }

            input:focus {
              border-color: #667eea;
            }

            button {
              padding: 12px 24px;
              background: #667eea;
              color: white;
              border: none;
              border-radius: 25px;
              cursor: pointer;
              font-weight: 600;
              transition: background 0.2s;
            }

            button:hover {
              background: #764ba2;
            }

            button:disabled {
              background: #ccc;
              cursor: not-allowed;
            }

            .loading {
              display: flex;
              gap: 5px;
              align-items: center;
            }

            .loading span {
              width: 8px;
              height: 8px;
              background: #667eea;
              border-radius: 50%;
              animation: bounce 1.4s infinite;
            }

            .loading span:nth-child(2) {
              animation-delay: 0.2s;
            }

            .loading span:nth-child(3) {
              animation-delay: 0.4s;
            }

            @keyframes bounce {
              0%, 80%, 100% { transform: scale(0); }
              40% { transform: scale(1); }
            }

            @media (max-width: 600px) {
              .chat-container {
                height: 100vh;
                border-radius: 0;
              }

              .message-content {
                max-width: 90%;
              }
            }
          </style>
        </head>
        <body>
          <div class="chat-container">
            <div class="chat-header">
              <h1> AI Chatbot</h1>
              <p>Powered by Cloudflare Workers AI</p>
            </div>

            <div class="messages" id="messages">
              <div class="message ai">
                <div class="avatar"></div>
                <div class="message-content">
                  Hi! I'm an AI chatbot running on Cloudflare Workers. How can I help you today?
                </div>
              </div>
            </div>

            <div class="input-area">
              <input
                type="text"
                id="input"
                placeholder="Type your message..."
                autocomplete="off"
              />
              <button id="send" onclick="sendMessage()">Send</button>
            </div>
          </div>

          <script>
            const messagesDiv = document.getElementById('messages');
            const input = document.getElementById('input');
            const sendBtn = document.getElementById('send');

            input.addEventListener('keypress', (e) => {
              if (e.key === 'Enter') sendMessage();
            });

            async function sendMessage() {
              const text = input.value.trim();
              if (!text) return;

              // Add user message
              const userMsg = document.createElement('div');
              userMsg.className = 'message user';
              userMsg.innerHTML = \`<div class="message-content">\${text}</div>\`;
              messagesDiv.appendChild(userMsg);

              input.value = '';
              sendBtn.disabled = true;

              // Scroll to bottom
              messagesDiv.scrollTop = messagesDiv.scrollHeight;

              try {
                // Send to AI endpoint
                const response = await fetch('/api/chat', {
                  method: 'POST',
                  headers: { 'Content-Type': 'application/json' },
                  body: JSON.stringify({ message: text })
                });

                const data = await response.json();

                // Add AI response
                const aiMsg = document.createElement('div');
                aiMsg.className = 'message ai';
                aiMsg.innerHTML = \`
                  <div class="avatar"></div>
                  <div class="message-content">\${data.response}</div>
                \`;
                messagesDiv.appendChild(aiMsg);

                // Scroll to bottom
                messagesDiv.scrollTop = messagesDiv.scrollHeight;
              } catch (error) {
                const errorMsg = document.createElement('div');
                errorMsg.className = 'message ai';
                errorMsg.innerHTML = \`
                  <div class="avatar"></div>
                  <div class="message-content">Sorry, I encountered an error. Please try again.</div>
                \`;
                messagesDiv.appendChild(errorMsg);
              }

              sendBtn.disabled = false;
              input.focus();
            }
          </script>
        </body>
        </html>
      `, {
        headers: { 'Content-Type': 'text/html' }
      });
    }

    // API endpoint for chat
    if (path === '/api/chat' && request.method === 'POST') {
      try {
        const { message } = await request.json();

        if (!message) {
          return new Response(JSON.stringify({
            error: 'Message is required'
          }), {
            status: 400,
            headers: { 'Content-Type': 'application/json' }
          });
        }

        // Call Workers AI
        const response = await env.AI.run('@cf/mistral/mistral-7b-instruct-v0.1', {
          prompt: message,
          max_tokens: 256
        });

        const aiResponse = response.response || 'No response';

        return new Response(JSON.stringify({
          response: aiResponse,
          model: 'Mistral 7B'
        }), {
          headers: { 'Content-Type': 'application/json' }
        });
      } catch (error) {
        return new Response(JSON.stringify({
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

## Step 4: Save and Deploy

1. Click **Save and Deploy**
2. Wait for deployment
3. Click your Worker URL
4. You should see the chat interface

---

## Step 5: Test the Chatbot

1. Type a message in the chat box
2. Click **Send** or press Enter
3. The AI will respond
4. Try different questions:
   - "What is Cloudflare Workers?"
   - "Tell me a joke"
   - "Explain machine learning"

---

## Step 6: Understand the AI Models

Workers AI supports multiple models:

| Model | Best For |
|-------|----------|
| `@cf/mistral/mistral-7b-instruct-v0.1` | General chat (default) |
| `@cf/meta/llama-2-7b-chat-int8` | Conversational AI |
| `@cf/openai/whisper` | Speech to text |
| `@cf/stabilityai/stable-diffusion-xl-base-1.0` | Image generation |

---

## Step 7: Change the AI Model (Optional)

To use a different model, change this line:

```javascript
const response = await env.AI.run('@cf/mistral/mistral-7b-instruct-v0.1', {
```

To:

```javascript
const response = await env.AI.run('@cf/meta/llama-2-7b-chat-int8', {
```

---

## Key Features

 Real-time chat interface  
 AI-powered responses  
 Beautiful UI with animations  
 Mobile responsive  
 Error handling  
 Multiple AI models available  

---

## Dashboard Navigation Summary

```
Cloudflare Dashboard
├── Build
│   ├── Compute & AI
│   │   ├── Workers & Pages
│   │   │   ├── Your Worker
│   │   │   │   ├── Editor (code)
│   │   │   │   └── Settings (AI binding)
│   │   │
│   │   └── Workers AI
```

---

## Next Steps

Ready to deploy? Go to **[Module 8: Deploy & Share](./08-deploy-share.md)** to share your app with the world.

---

## Troubleshooting

### AI not responding?
- Check AI binding is configured in Settings
- Verify binding variable name is `AI`
- Check your Cloudflare account has AI enabled

### Getting errors?
- Check the browser console (F12)
- Look at Worker logs in the Dashboard
- Try a different AI model

### Slow responses?
- AI models take 5-10 seconds to respond
- This is normal for the first request
- Subsequent requests are faster

---

## Resources

- [Workers AI Documentation](https://developers.cloudflare.com/workers-ai/)
- [Available AI Models](https://developers.cloudflare.com/workers-ai/models/)
