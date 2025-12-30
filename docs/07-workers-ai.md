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
3. Click **Create application** → **Start with Hello World!**
4. Name it: `ai-chatbot` (or any name you like)
5. Click **Deploy**
6. You'll see the default Hello World preview — leave it for now
7. Stay on this new Worker; we'll add bindings next

---

## Step 2: Add AI Binding

1. Click the **Bindings** tab at the top (next to Overview, Metrics, Deployments)
2. Click **Add binding**
3. A modal with binding types will appear — select **Worker AI**
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
      return new Response(`<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Cloudflare AI Studio</title>
  <style>
    :root {
      --cf-orange: #F6821F;
      --cf-orange-dark: #FF6633;
      --cf-midnight: #050914;
      --cf-card: rgba(15, 23, 42, 0.85);
      --cf-border: rgba(255, 255, 255, 0.12);
      --cf-cloud: #f8fafc;
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Inter', 'Segoe UI', system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
      min-height: 100vh;
      background: radial-gradient(circle at top right, rgba(255, 102, 51, 0.25), transparent 40%),
                  radial-gradient(circle at bottom left, rgba(246, 130, 31, 0.25), transparent 35%),
                  #050914;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 32px;
      color: white;
    }

    .surface {
      width: min(1080px, 100%);
      background: var(--cf-card);
      border-radius: 32px;
      border: 1px solid var(--cf-border);
      box-shadow: 0 40px 90px rgba(0, 0, 0, 0.45);
      overflow: hidden;
      backdrop-filter: blur(18px);
    }

    .hero {
      padding: 32px;
      background: linear-gradient(135deg, rgba(255, 102, 51, 0.25), rgba(246, 130, 31, 0.05));
      border-bottom: 1px solid var(--cf-border);
    }

    .eyebrow {
      text-transform: uppercase;
      font-size: 12px;
      letter-spacing: 0.2em;
      color: rgba(255,255,255,0.6);
      margin-bottom: 8px;
    }

    .hero h1 {
      font-size: 32px;
      margin-bottom: 8px;
    }

    .hero p {
      color: rgba(255,255,255,0.75);
      font-size: 15px;
      max-width: 640px;
    }

    .tabs {
      margin-top: 24px;
      display: inline-flex;
      background: rgba(255,255,255,0.08);
      border-radius: 999px;
      padding: 6px;
    }

    .tab {
      border: none;
      background: transparent;
      color: rgba(255,255,255,0.7);
      padding: 10px 24px;
      border-radius: 999px;
      cursor: pointer;
      font-weight: 600;
      font-size: 14px;
      transition: background 0.2s, color 0.2s;
    }

    .tab.active {
      background: linear-gradient(135deg, var(--cf-orange), var(--cf-orange-dark));
      color: white;
      box-shadow: 0 12px 24px rgba(255,102,51,0.25);
    }

    .layout {
      display: grid;
      grid-template-columns: 320px 1fr;
      min-height: 560px;
    }

    .sidebar {
      padding: 28px;
      border-right: 1px solid var(--cf-border);
      background: rgba(255,255,255,0.02);
    }

    .sidebar h2 {
      font-size: 18px;
      margin-bottom: 18px;
    }

    .model-grid {
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .model-grid.hidden {
      display: none !important;
    }

    .model-option {
      width: 100%;
      border-radius: 16px;
      padding: 16px;
      border: 1px solid var(--cf-border);
      background: rgba(255,255,255,0.02);
      color: inherit;
      text-align: left;
      cursor: pointer;
      transition: border 0.2s, transform 0.2s, background 0.2s;
    }

    .model-option span {
      display: block;
    }

    .model-name {
      font-weight: 600;
      font-size: 15px;
    }

    .model-desc {
      font-size: 12px;
      color: rgba(255,255,255,0.65);
      margin-top: 4px;
    }

    .model-option.active {
      border-color: rgba(255,102,51,0.9);
      background: rgba(255,102,51,0.15);
      box-shadow: 0 15px 30px rgba(255,102,51,0.2);
    }

    .model-option:hover {
      border-color: rgba(246,130,31,0.9);
      transform: translateY(-2px);
    }

    .panels {
      padding: 28px;
      display: flex;
      flex-direction: column;
      gap: 24px;
    }

    .panel {
      background: rgba(255,255,255,0.03);
      border: 1px solid var(--cf-border);
      border-radius: 24px;
      padding: 24px;
      display: flex;
      flex-direction: column;
      gap: 20px;
    }

    .panel.image-mode {
      min-height: 520px;
    }

    .image-grid {
      display: grid;
      grid-template-columns: minmax(0, 1.1fr) minmax(0, 0.9fr);
      gap: 28px;
      height: 100%;
    }

    .hidden {
      display: none !important;
    }

    .pill {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      background: rgba(255,255,255,0.06);
      border-radius: 999px;
      padding: 8px 16px;
      font-size: 13px;
      color: rgba(255,255,255,0.8);
    }

    .pill strong {
      color: white;
    }

    .messages {
      flex: 1;
      min-height: 320px;
      max-height: 420px;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
      gap: 16px;
      padding-right: 8px;
    }

    .message {
      padding: 16px 18px;
      border-radius: 18px;
      max-width: 85%;
      line-height: 1.5;
      font-size: 15px;
      color: var(--cf-cloud);
      box-shadow: 0 10px 30px rgba(0,0,0,0.25);
      animation: fadeUp 0.2s ease;
    }

    .message.user {
      align-self: flex-end;
      background: linear-gradient(135deg, var(--cf-orange), var(--cf-orange-dark));
    }

    .message.ai {
      align-self: flex-start;
      background: rgba(255,255,255,0.06);
      border: 1px solid var(--cf-border);
    }

    .message .meta {
      display: block;
      font-size: 11px;
      letter-spacing: 0.08em;
      opacity: 0.65;
      margin-top: 8px;
      text-transform: uppercase;
    }

    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(8px); }
      to { opacity: 1; transform: translateY(0); }
    }

    form.chat-form {
      background: rgba(255,255,255,0.04);
      border: 1px solid var(--cf-border);
      border-radius: 20px;
      padding: 16px;
      display: flex;
      gap: 12px;
      align-items: center;
    }

    textarea {
      flex: 1;
      background: transparent;
      border: none;
      color: white;
      resize: none;
      min-height: 56px;
      font-size: 15px;
      line-height: 1.5;
      font-family: inherit;
    }

    textarea:focus {
      outline: none;
    }

    button.primary {
      background: linear-gradient(135deg, var(--cf-orange), var(--cf-orange-dark));
      border: none;
      color: white;
      font-weight: 600;
      padding: 14px 26px;
      border-radius: 999px;
      cursor: pointer;
      transition: transform 0.2s, box-shadow 0.2s;
    }

    button.primary:hover:not(:disabled) {
      transform: translateY(-2px);
      box-shadow: 0 18px 35px rgba(255,102,51,0.35);
    }

    button.primary:disabled {
      opacity: 0.5;
      cursor: not-allowed;
      transform: none;
      box-shadow: none;
    }

    .image-card label {
      font-size: 14px;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      color: #ffffff;
    }

    .image-card textarea {
      min-height: 140px;
      font-size: 15px;
      line-height: 1.5;
    }

    .examples {
      margin: 12px 0;
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
    }

    .example {
      border: 1px solid var(--cf-border);
      background: rgba(255,255,255,0.04);
      color: rgba(255,255,255,0.85);
      border-radius: 999px;
      padding: 6px 14px;
      font-size: 13px;
      cursor: pointer;
      transition: border 0.2s, background 0.2s;
    }

    .example:hover {
      border-color: rgba(246,130,31,0.8);
      background: rgba(246,130,31,0.15);
    }

    .image-result {
      margin-top: 16px;
      text-align: center;
    }

    .image-result img {
      max-width: 100%;
      border-radius: 18px;
      box-shadow: 0 25px 60px rgba(0,0,0,0.45);
      margin-bottom: 8px;
    }

    .muted {
      color: rgba(255,255,255,0.65);
      font-size: 13px;
    }

    .image-preview {
      border: 1px solid var(--cf-border);
      border-radius: 20px;
      background: rgba(255,255,255,0.02);
      padding: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100%;
    }

    .error {
      color: #FCA5A5;
      font-size: 14px;
    }

    @media (max-width: 960px) {
      .layout {
        grid-template-columns: 1fr;
      }

      .sidebar {
        border-right: none;
        border-bottom: 1px solid var(--cf-border);
      }
    }
  </style>
</head>
<body>
  <div class="surface">
    <header class="hero">
      <div class="eyebrow">Workers AI</div>
      <h1>Cloudflare AI Studio</h1>
      <p>Pick a model, ask questions, and even generate images — all powered by Workers AI and styled with Cloudflare's signature gradients.</p>
      <div class="tabs">
        <button class="tab active" data-mode="chat">Chat Mode</button>
        <button class="tab" data-mode="image">Image Studio</button>
      </div>
    </header>
    <div class="layout">
      <aside class="sidebar">
        <h2 id="sidebar-title">Pick a Model</h2>
        <div class="model-grid" id="chat-models">
          <button class="model-option active" data-model="@cf/meta/llama-3.1-8b-instruct" data-label="Llama 3.1 8B">
            <span class="model-name">Llama 3.1 8B</span>
            <span class="model-desc">Balanced, friendly answers</span>
          </button>
          <button class="model-option" data-model="@cf/meta/llama-3.2-3b-instruct" data-label="Llama 3.2 3B">
            <span class="model-name">Llama 3.2 3B</span>
            <span class="model-desc">Fast responses</span>
          </button>
          <button class="model-option" data-model="@cf/mistral/mistral-7b-instruct-v0.1" data-label="Mistral 7B">
            <span class="model-name">Mistral 7B</span>
            <span class="model-desc">Creative storytelling</span>
          </button>
          <button class="model-option" data-model="@cf/google/gemma-3-12b-it" data-label="Gemma 3 12B">
            <span class="model-name">Gemma 3 12B</span>
            <span class="model-desc">Multilingual + structured</span>
          </button>
        </div>
        <div class="model-grid hidden" id="image-models">
          <button class="model-option active" data-model="@cf/stabilityai/stable-diffusion-xl-base-1.0" data-label="Stable Diffusion XL">
            <span class="model-name">Stable Diffusion XL</span>
            <span class="model-desc">High quality detail</span>
          </button>
          <button class="model-option" data-model="@cf/bytedance/stable-diffusion-xl-lightning" data-label="SDXL Lightning">
            <span class="model-name">SDXL Lightning</span>
            <span class="model-desc">Fast 4-step render</span>
          </button>
        </div>
      </aside>
      <section class="panels">
        <div class="panel" id="chat-panel">
          <div class="pill"><span id="model-pill-label">Current model:</span> <strong id="model-pill">Llama 3.1 8B</strong></div>
          <div class="messages" id="messages">
            <div class="message ai">
              👋 Hi! I'm running on Workers AI at Cloudflare's edge. Pick a model and ask me something.
              <span class="meta">System</span>
            </div>
          </div>
          <form class="chat-form" id="chat-form">
            <textarea id="chat-input" placeholder="Ask me about Workers, R2, D1, or anything else..."></textarea>
            <button type="submit" class="primary" id="send-btn">Send</button>
          </form>
        </div>
        <div class="panel image-mode hidden" id="image-panel">
          <div class="image-grid">
            <div class="image-card">
              <label for="image-input">Describe an image</label>
              <form id="image-form">
                <textarea id="image-input" placeholder="A glowing Cloudflare data center at sunset..."></textarea>
                <div class="examples">
                  <span class="muted">Try:</span>
                  <button type="button" class="example" data-prompt="A cute robot playing guitar in a neon city">Robot + Guitar</button>
                  <button type="button" class="example" data-prompt="A magical forest with glowing mushrooms at night">Magic Forest</button>
                  <button type="button" class="example" data-prompt="A cozy coffee shop interior with warm lighting">Coffee Shop</button>
                  <button type="button" class="example" data-prompt="A futuristic Cloudflare edge data center above the clouds">Edge Cloud</button>
                </div>
                <button type="submit" class="primary" id="image-btn">Generate image</button>
              </form>
            </div>
            <div class="image-preview">
              <div class="image-result" id="image-result">
                <p class="muted">Your AI artwork will appear here.</p>
              </div>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>

  <script>
    const tabs = document.querySelectorAll('.tab');
    const chatPanel = document.getElementById('chat-panel');
    const imagePanel = document.getElementById('image-panel');
    const sidebarTitle = document.getElementById('sidebar-title');
    const modelPill = document.getElementById('model-pill');
    const modelPillLabel = document.getElementById('model-pill-label');

    const chatModels = [
      { label: 'Llama 3.1 8B', desc: 'Balanced, friendly answers', value: '@cf/meta/llama-3.1-8b-instruct' },
      { label: 'Llama 3.2 3B', desc: 'Fast responses', value: '@cf/meta/llama-3.2-3b-instruct' },
      { label: 'Mistral 7B', desc: 'Creative storytelling', value: '@cf/mistral/mistral-7b-instruct-v0.1' },
      { label: 'Gemma 3 12B', desc: 'Multilingual + structured', value: '@cf/google/gemma-3-12b-it' }
    ];

    const imageEngines = [
      { label: 'Stable Diffusion XL', desc: 'High quality detail', value: '@cf/stabilityai/stable-diffusion-xl-base-1.0' },
      { label: 'SDXL Lightning', desc: 'Fast 4-step render', value: '@cf/bytedance/stable-diffusion-xl-lightning' }
    ];

    let currentMode = 'chat';
    let selectedChatModel = chatModels[0].value;
    let selectedImageModel = imageEngines[0].value;
    const chatModelsGrid = document.getElementById('chat-models');
    const imageModelsGrid = document.getElementById('image-models');

    function appendMessage(role, text, meta) {
      const block = document.createElement('div');
      block.className = 'message ' + role;
      block.innerHTML = text.replace(/\\n/g, '<br>');
      if (meta) {
        const metaEl = document.createElement('span');
        metaEl.className = 'meta';
        metaEl.textContent = meta;
        block.appendChild(metaEl);
      }
      const container = document.getElementById('messages');
      container.appendChild(block);
      container.scrollTop = container.scrollHeight;
    }

    function setActiveButton(grid, targetModel) {
      grid.querySelectorAll('.model-option').forEach((btn) => {
        if (btn.dataset.model === targetModel) {
          btn.classList.add('active');
        } else {
          btn.classList.remove('active');
        }
      });
    }

    function renderSidebar() {
      if (currentMode === 'chat') {
        sidebarTitle.textContent = 'Pick a Model';
        modelPillLabel.textContent = 'Current model:';
        chatModelsGrid.classList.remove('hidden');
        imageModelsGrid.classList.add('hidden');
        const active = chatModels.find((m) => m.value === selectedChatModel) || chatModels[0];
        modelPill.textContent = active.label;
        setActiveButton(chatModelsGrid, selectedChatModel);
      } else {
        sidebarTitle.textContent = 'Choose an Image Engine';
        modelPillLabel.textContent = 'Image model:';
        chatModelsGrid.classList.add('hidden');
        imageModelsGrid.classList.remove('hidden');
        const active = imageEngines.find((m) => m.value === selectedImageModel) || imageEngines[0];
        modelPill.textContent = active.label;
        setActiveButton(imageModelsGrid, selectedImageModel);
      }
    }

    chatModelsGrid.querySelectorAll('.model-option').forEach((btn) => {
      btn.addEventListener('click', () => {
        selectedChatModel = btn.dataset.model;
        renderSidebar();
        appendMessage('ai', 'Switched to <strong>' + btn.dataset.label + '</strong>. Ask me anything!', 'System');
      });
    });

    imageModelsGrid.querySelectorAll('.model-option').forEach((btn) => {
      btn.addEventListener('click', () => {
        selectedImageModel = btn.dataset.model;
        renderSidebar();
      });
    });

    tabs.forEach((tab) => {
      tab.addEventListener('click', (event) => {
        tabs.forEach((t) => t.classList.remove('active'));
        const target = event.currentTarget;
        target.classList.add('active');
        currentMode = target.dataset.mode;
        if (currentMode === 'chat') {
          chatPanel.classList.remove('hidden');
          imagePanel.classList.add('hidden');
        } else {
          imagePanel.classList.remove('hidden');
          chatPanel.classList.add('hidden');
        }
        renderSidebar();
      });
    });

    renderSidebar();

    const chatForm = document.getElementById('chat-form');
    const chatInput = document.getElementById('chat-input');
    const sendBtn = document.getElementById('send-btn');

    chatForm.addEventListener('submit', (event) => {
      event.preventDefault();
      sendMessage();
    });

    chatInput.addEventListener('keydown', (event) => {
      if (event.key === 'Enter' && !event.shiftKey) {
        event.preventDefault();
        sendMessage();
      }
    });

    async function sendMessage() {
      const text = chatInput.value.trim();
      if (!text) return;
      appendMessage('user', text);
      chatInput.value = '';
      sendBtn.disabled = true;
      try {
        const response = await fetch('/api/chat', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ message: text, model: selectedChatModel })
        });
        const data = await response.json();
        if (response.ok) {
          appendMessage('ai', data.response, data.model);
        } else {
          appendMessage('ai', data.error || 'Workers AI returned an error.', 'Error');
        }
      } catch (error) {
        appendMessage('ai', 'Unable to reach Workers AI right now. Please try again.', 'Network');
      } finally {
        sendBtn.disabled = false;
        chatInput.focus();
      }
    }

    const imageForm = document.getElementById('image-form');
    const imageInput = document.getElementById('image-input');
    const imageBtn = document.getElementById('image-btn');
    const imageResult = document.getElementById('image-result');
    const exampleButtons = document.querySelectorAll('.example');

    exampleButtons.forEach((btn) => {
      btn.addEventListener('click', () => {
        imageInput.value = btn.dataset.prompt;
      });
    });

    imageForm.addEventListener('submit', async (event) => {
      event.preventDefault();
      await generateImage();
    });

    async function generateImage() {
      const prompt = imageInput.value.trim();
      if (!prompt) return;
      imageBtn.disabled = true;
      imageBtn.textContent = 'Generating...';
      imageResult.innerHTML = '<p class="muted">Rendering image with Workers AI...</p>';
      try {
        const response = await fetch('/api/image', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ prompt, model: selectedImageModel })
        });
        if (!response.ok) {
          const error = await response.json();
          throw new Error(error.error || 'Image generation failed');
        }
        const blob = await response.blob();
        const url = URL.createObjectURL(blob);
        const engine = imageEngines.find((e) => e.value === selectedImageModel)?.label || 'Workers AI';
        imageResult.innerHTML = '<img src="' + url + '" alt="AI generated image"><p class="muted">Prompt: ' + prompt + '<br>Engine: ' + engine + '</p>';
      } catch (error) {
        imageResult.innerHTML = '<p class="error">Error: ' + error.message + '</p>';
      } finally {
        imageBtn.disabled = false;
        imageBtn.textContent = 'Generate image';
      }
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
        const { message, model } = await request.json();

        if (!message) {
          return new Response(JSON.stringify({
            error: 'Message is required'
          }), {
            status: 400,
            headers: { 'Content-Type': 'application/json' }
          });
        }

        const selectedModel = model || '@cf/meta/llama-3.1-8b-instruct';
        const modelName = selectedModel.split('/').pop();

        const response = await env.AI.run(selectedModel, {
          messages: [
            {
              role: 'system',
              content: 'You are Cloudflare\'s helpful AI assistant. Keep answers concise, friendly, and accurate.'
            },
            { role: 'user', content: message }
          ],
          max_tokens: 256
        });

        let text = 'No response';
        if (typeof response.response === 'string') {
          text = response.response;
        } else if (Array.isArray(response.result)) {
          text = response.result
            .flatMap((entry) => entry.output_text ?? entry.response ?? entry.content ?? [])
            .join('\n')
            .trim() || 'No response';
        }

        return new Response(JSON.stringify({
          response: text || 'No response',
          model: modelName
        }), {
          headers: { 'Content-Type': 'application/json' }
        });
      } catch (error) {
        return new Response(JSON.stringify({
          error: error.message || 'Workers AI call failed'
        }), {
          status: 500,
          headers: { 'Content-Type': 'application/json' }
        });
      }
    }

    // API endpoint for image generation
    if (path === '/api/image' && request.method === 'POST') {
      try {
        const { prompt, model } = await request.json();
        if (!prompt) {
          return new Response(JSON.stringify({ error: 'Prompt is required' }), {
            status: 400,
            headers: { 'Content-Type': 'application/json' }
          });
        }

        const selectedImageModel = model || '@cf/stabilityai/stable-diffusion-xl-base-1.0';
        const result = await env.AI.run(selectedImageModel, { prompt });

        return new Response(result, {
          headers: { 'Content-Type': 'image/png' }
        });
      } catch (error) {
        return new Response(JSON.stringify({
          error: error.message || 'Image generation failed'
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

## Step 5: Try Both Modes

### Chat Mode
1. Use the **Chat Mode** tab.
2. Pick a chat model in the sidebar (Llama 3.1 8B, Llama 3.2 3B, Mistral 7B, or Gemma 3 12B).
3. Ask the same question across models to compare tone, speed, and style.  
   Example prompts:
   - “What is Cloudflare Workers?”
   - “Explain R2 vs. D1.”
   - “Write a haiku about orange gradients.”

### Image Studio
1. Switch to the **Image Studio** tab.
2. Pick an image engine (Stable Diffusion XL for detail, SDXL Lightning for speed).
3. Type a description or click a preset prompt (Robot + Guitar, Magic Forest, etc.).
4. Click **Generate image** and wait 10‑30 seconds. The preview panel shows the result plus which engine rendered it.

---

## Step 6: Understand the Available Models

### Chat Models
| Model | Description |
|-------|-------------|
| `@cf/meta/llama-3.1-8b-instruct` | Balanced assistant for detailed answers |
| `@cf/meta/llama-3.2-3b-instruct` | Faster responses for lightweight queries |
| `@cf/mistral/mistral-7b-instruct-v0.1` | Creative storytelling & ideation |
| `@cf/google/gemma-3-12b-it` | Structured, multilingual support |

### Image Engines
| Engine | Description |
|--------|-------------|
| `@cf/stabilityai/stable-diffusion-xl-base-1.0` | Highest fidelity, slower |
| `@cf/bytedance/stable-diffusion-xl-lightning` | Fast 4-step renders |

---

## Step 7: Advanced Customization

- **System Prompt**: Update the `role: 'system'` message to change tone or instructions.
- **Default Models**: Change `selectedChatModel` or `selectedImageModel` to preselect a different option.
- **Add Options**: Duplicate the button markup in the sidebar to add more models/engines (be sure to update `imageEngines` or `chatModels` arrays).

---

## Key Features

- Real-time chat interface powered by Workers AI  
- Two-mode UI (Chat + Image Studio) with Cloudflare gradients  
- Model pickers synced to each mode  
- Image previews and preset prompts  
- Error handling + status messages  
- Fully responsive layout  

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
