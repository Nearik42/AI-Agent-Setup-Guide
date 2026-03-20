# Always-On Agent: Telegram & Discord

Right now your agent only works when you open a terminal. Let's change that.

With a messaging integration, you can:
- Message your agent from your phone
- Get proactive updates without opening a terminal
- Run tasks in the background

> 💬 **Community:** [@openclaw](https://x.com/openclaw/status/2032694261993427260) — "How to set up live Chrome sessions" — another approach to always-on agents via browser integration.

---

## Option A: Telegram Bot (Easiest — Start Here)

Telegram bots are free, easy to set up, and work on every device.

### Step 1: Create a Telegram Bot

1. Open Telegram, search for `@BotFather`
2. Send: `/newbot`
3. Follow prompts, give it a name
4. Copy the **Bot Token** (looks like `1234567890:ABCdefGHIjklMNOpqrsTUVwxyz`)

### Step 2: Get Your Chat ID

1. Start a conversation with your new bot
2. Visit: `https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates`
3. Find `"chat": {"id": XXXXXX}` — that's your chat ID

### Step 3: Simple Agent Bridge

Create `agent-bot.js`:
```javascript
const { execSync } = require('child_process');

const TOKEN = process.env.TELEGRAM_TOKEN;
const CHAT_ID = process.env.TELEGRAM_CHAT_ID;


```javascript
// Helper functions
async function sendMessage(chatId, text) {
  const url = `https://api.telegram.org/bot${TOKEN}/sendMessage`;
  const resp = await fetch(url, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ chat_id: chatId, text: text.substring(0, 4096) })
  });
  return resp.json();
}

async function getUpdates(offset) {
  const url = `https://api.telegram.org/bot${TOKEN}/getUpdates?offset=${offset}&timeout=10`;
  const resp = await fetch(url);
  const data = await resp.json();
  return data.result || [];
}

function sleep(ms) { return new Promise(r => setTimeout(r, ms)); }
```

async function pollMessages() {
  let offset = 0;
  while (true) {
    const updates = await getUpdates(offset);
    for (const update of updates) {
      offset = update.update_id + 1;
      if (update.message?.text) {
        const response = runClaude(update.message.text);
        await sendMessage(CHAT_ID, response);
      }
    }
    await sleep(1000);
  }
}

function runClaude(prompt) {
  try {
    const result = execSync(
      `claude --print --permission-mode bypassPermissions -p "${prompt.replace(/"/g, '\\"')}"`,
      { env: { ...process.env }, timeout: 60000, encoding: 'utf8' }
    );
    return result.trim();
  } catch (e) {
    return `Error: ${e.message}`;
  }
}

// sendMessage and getUpdates helpers omitted for brevity
// For a complete working example with all helper functions, see [telegraf.js.org](https://telegraf.js.org) (Node.js) or [python-telegram-bot.readthedocs.io](https://python-telegram-bot.readthedocs.io) (Python) — both have copy-paste quickstart guides
```

This is a minimal starting point. In practice, you'd want error handling, message queuing, and proper async handling.

### The Real Solution: Use a Bot Library

For a production-ready Telegram agent, use:
- **[Telegraf](https://telegraf.js.org)** (Node.js Telegram framework) — `npm install telegraf`
- **python-telegram-bot** (Python) — `pip install python-telegram-bot`
- **OpenClaw** (which handles this natively — Stage 8)

---

## Option B: Discord Bot

Discord gives you more structure: channels per project, threads, file uploads.

### Step 1: Create a Discord Bot

1. Go to discord.com/developers/applications
2. "New Application" > give it a name
3. Go to "Bot" tab > "Add Bot"
4. Copy the token
5. Enable: "Message Content Intent", "Server Members Intent", "Presence Intent"
6. Invite to your server via OAuth2 URL

### Step 2: Minimal Discord Agent

```javascript
const { Client, GatewayIntentBits } = require('discord.js');
const { execSync } = require('child_process');

const client = new Client({
  intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent]
});

client.on('messageCreate', async (message) => {
  if (message.author.bot) return;
  if (!message.mentions.has(client.user)) return; // Only respond when mentioned

  const prompt = message.content.replace(/<@!?\d+>/g, '').trim();

  await message.reply('Working on it...');

  const response = execSync(
    `claude --print --permission-mode bypassPermissions -p "${prompt}"`,
    { env: process.env, encoding: 'utf8', timeout: 120000 }
  ).trim();

  await message.reply(response.substring(0, 2000)); // Discord 2000 char limit
});

client.login(process.env.DISCORD_TOKEN);
```

Use the [discord.js](https://discord.js.org) library for more robust Discord bot development.

---

## Running it 24/7

A bot that only runs when your laptop is on isn't very useful.

### Option 1: Keep Terminal Running (Temporary)
```bash
node agent-bot.js &  # Run in background
```

### Option 2: PM2 (Simple Process Manager)
[PM2](https://pm2.keymetrics.io) is the standard tool for keeping Node.js processes alive:
```bash
npm install -g pm2
pm2 start agent-bot.js --name "my-agent"
pm2 save
pm2 startup  # Auto-start on reboot
```

### Option 3: VPS (Recommended for Always-On)
A 5 EUR/month VPS from [Hetzner Cloud](https://www.hetzner.com/cloud) or [DigitalOcean](https://digitalocean.com) runs your agent 24/7 without using your laptop. This is what serious setups use.

Setup:
```bash
# On your VPS:
git clone your-repo
npm install
pm2 start agent-bot.js
```

> 💬 **Community:** [@Voxyz_ai](https://x.com/Voxyz_ai/status/2033207871286980840) — "Dead simple way to maintain openclaw. Bugs, oauth expiring" — maintenance patterns for keeping your always-on agent healthy.

---

## Security Note

When your agent is connected to messaging:
- Only respond to messages from your own account/chat ID
- Don't expose the bot token publicly
- Consider a whitelist of allowed commands

---

## Your Agent Is Now Available on Your Phone

Test it:
1. Open Telegram/Discord on your phone
2. Send: "Create a quick summary of what I should know about FastAPI"
3. Watch it respond

That's an AI agent. Always on, accessible anywhere.

---

[← 🎯 Skills](04b-skills.md) · [📚 Table of Contents](../README.md) · [⚙️ Automation & Workflows →](06-automation-workflows.md)
