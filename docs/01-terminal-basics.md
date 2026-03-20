# Terminal Basics: A Survival Guide for Non-Developers

> 💡 **Good news before we start:** Once Claude Code is set up, you won't need to remember most of what's in this chapter. You'll type in plain English — "create a project folder", "install what I need", "set this up" — and Claude Code handles the actual commands. This chapter is the **one-time setup**, not a new skill you'll need forever. Think of it like learning to turn on a car. You do it once, then you just drive.

The terminal is where AI agents live. You don't need to be a developer, but you do need to be comfortable with a few basics.

This chapter covers everything you need — nothing more.

---

## Mac vs. Windows vs. Linux

### Mac (recommended for beginners)
The Terminal app is already installed. Find it in:
- Applications > Utilities > Terminal
- Or: Spotlight (Cmd+Space) > type "Terminal"

A better alternative: install **iTerm2** (free, more features) from iterm2.com

### Windows
Two options:
1. **PowerShell** — built-in, fine for basic tasks
2. **WSL2 (Windows Subsystem for Linux)** — recommended for AI development

WSL2 gives you a full Linux environment inside Windows. It's what most tutorials assume. Install it:
```
wsl --install
```
After reboot, you'll have Ubuntu. Use this as your main terminal for everything in this guide.

### Linux
You already know what you're doing. Use whatever shell you prefer.

---

## The 10 Commands You Actually Need

```bash
# Navigate
pwd                       # Where am I? (print working directory)
ls                        # List files here (Mac/Linux)
dir                       # List files here (Windows PowerShell)
cd foldername             # Enter a folder
cd ..                     # Go up one level
cd ~                      # Go to your home folder

# Files
cat filename.txt          # Show file contents
mkdir foldername          # Create a folder
rm filename.txt           # Delete a file (careful — no undo!)
cp source dest            # Copy a file

# Run things
node --version            # Check if Node.js is installed
npm install -g something  # Install a global package
```

That's it. You'll use these constantly. Everything else you can Google as you go.

---

## Install Node.js

Use [FNM (Fast Node Manager)](https://github.com/Schniz/fnm) — it's faster and cleaner than nvm:

Almost everything in this guide requires Node.js. Install it once, forget about it.

**Recommended: FNM (Fast Node Manager)** — works on Mac, Windows, and Linux. Much faster than the alternatives.

### Mac/Linux:
```bash
curl -fsSL https://fnm.vercel.app/install | bash
# Restart terminal, then:
fnm install 20
fnm use 20
node --version  # Should show v20.x.x
```

### Windows (PowerShell):
```powershell
winget install Schniz.fnm
# Restart PowerShell, then:
fnm install 20
fnm use 20
node --version
```

---

## API Keys: Handle With Care

Every AI service needs an API key — a secret password that identifies you and charges your account.

**The golden rules:**
1. **Never paste API keys into a chat, email, or public document**
2. **Never commit them to a GitHub repository**
3. **Store them in a `.env` file — which you add to `.gitignore`**

### How to store API keys (the right way):
```bash
# Create a .env file in your project folder
echo 'ANTHROPIC_API_KEY=your-key-here' > .env

# Make sure .gitignore excludes it
echo '.env' >> .gitignore
```

Your code then reads it:
```javascript
// Node.js
require('dotenv').config();
const key = process.env.ANTHROPIC_API_KEY;
```

---

## Getting an Anthropic API Key

1. Go to console.anthropic.com
2. Sign up / sign in
3. Go to "API Keys" > "Create Key"
4. Copy the key immediately — you won't see it again
5. Add $5-10 credit to start (more than enough for experimenting)

**Cost reality check**: Claude Sonnet costs ~$3 per million tokens in, ~$15 per million out. A typical agent session with 10-20 exchanges costs cents, not dollars.

---

## Common Beginner Mistakes

| Mistake | What happens | Fix |
|---------|-------------|-----|
| Spaces in folder names | Commands break | Use underscores: my_project |
| Running command in wrong folder | "File not found" errors | Check with pwd, navigate with cd |
| Forgetting to restart terminal | New tools not found | Close and reopen terminal after installs |
| API key spacing wrong | Authentication fails | No extra spaces: KEY=value not KEY= value |
| Windows paths with backslashes | Breaks in WSL2 | Use forward slashes in WSL2: /home/user/ |

---

## You're Ready

If you can:
- Open a terminal
- Navigate folders with `cd` and `ls`
- Install Node.js

Use [FNM (Fast Node Manager)](https://github.com/Schniz/fnm) — it's faster and cleaner than nvm:
- Store an API key in a `.env` file

...you have everything you need for the next stage.
