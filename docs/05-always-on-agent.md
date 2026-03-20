# Always-On Agent: Telegram & Discord

Right now your agent only works when you open a terminal. Let's change that.

With a messaging integration, you can:
- Message your agent from your phone
- Get proactive updates without opening a terminal
- Run tasks in the background

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
// See the full example in the repo
```

This is a minimal starting point. In practice, you'd want error handling, message queuing, and proper async handling.

### The Real Solution: Use a Bot Library

For a production-ready Telegram agent, use:
- **Telegraf** (Node.js Telegram framework) — `npm install telegraf`
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

---

## Running it 24/7

A bot that only runs when your laptop is on isn't very useful.

### Option 1: Keep Terminal Running (Temporary)
```bash
node agent-bot.js &  # Run in background
```

### Option 2: PM2 (Simple Process Manager)
```bash
npm install -g pm2
pm2 start agent-bot.js --name "my-agent"
pm2 save
pm2 startup  # Auto-start on reboot
```

### Option 3: VPS (Recommended for Always-On)
A 5 EUR/month VPS from Hetzner or DigitalOcean runs your agent 24/7 without using your laptop. This is what serious setups use.

Setup:
```bash
# On your VPS:
git clone your-repo
npm install
pm2 start agent-bot.js
```

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
