💬 Study Games Chat

A real-time Discord-style chat app built with Node.js and Socket.io. Includes DMs, group chats, profile pictures, AI moderation, and a full admin panel.


📋 Requirements

- Node.js v16 or higher — check with `node -v`
- npm — comes with Node.js
- A Gemini API key (free) for AI moderation — get one at [aistudio.google.com](https://aistudio.google.com)

📁 File Structure

After cloning, your folder should look like this:

chat/
├── server.js            ← the backend server
├── package.json         ← dependencies
├── admin-config.json    ← set your admin password here
├── blocklist.json       ← words to auto-censor
├── banned.json          ← auto-managed, don't touch
├── warnings.json        ← auto-managed, don't touch
├── profiles.json        ← auto-managed, don't touch
└── public/
    ├── index.html       ← the chat app
    ├── admin.html       ← the admin panel
    └── uploads/         ← where chat images are saved (auto-created)


Some of these files are auto-created on first run (`users.json`, `groups.json`, `chats.txt`, `profiles.json`, `warnings.json`, `banned.json`, the `uploads/` folder). You only need to worry about the ones listed in Setup below.


⚙️ Setup

 1. Install dependencies

bash
cd chat
npm install

2. Set your admin password

Open `admin-config.json` and change the password:

json
{
  "password": "yourpasswordhere"
}
```

3. Edit the word blocklist (optional)

Open `blocklist.json` and add any words you want auto-censored with `***`:

json
["badword1", "badword2", "example"]


You can also edit this live from the admin panel later.

---

🚀 Running the Server

With AI moderation (recommended)

bash
GEMINI_API_KEY=AIzaYOURKEYHERE node server.js

Get your free Gemini API key at [aistudio.google.com](https://aistudio.google.com) → Get API Key.

Without AI moderation

bash
node server.js


Everything works the same, messages just aren't moderated automatically.

Custom port (default is 3000)

bash
PORT=8080 GEMINI_API_KEY=AIza... node server.js

🖥️ Keeping it Running (Production)

Use PM2 so the server stays up when you close your terminal:

bash
# Install PM2
npm install -g pm2

# Start the server
pm2 start server.js --name "study-games-chat" --env production

# To include your Gemini key:
GEMINI_API_KEY=AIza... pm2 start server.js --name "study-games-chat"

# Make it survive reboots
pm2 startup
pm2 save

# Useful PM2 commands
pm2 status                        # check if running
pm2 logs study-games-chat         # view live logs
pm2 restart study-games-chat      # restart
pm2 stop study-games-chat         # stop
```

---

## 🌐 Accessing the App

Once running, open your browser:

| Page | URL |
|---|---|
| Chat app | `http://localhost:3000` |
| Admin panel | `http://localhost:3000/admin` |

If you're hosting on a server (not your own computer), replace `localhost` with your server's IP address or domain name, e.g. `http://192.168.1.100:3000`.

---

## 💬 How the Chat Works

### For users
- **First visit** — you're automatically assigned a random 6-digit ID (e.g. `#482910`). This is saved in your browser and stays the same every time you visit.
- **Adding friends** — click "Add Friend by ID", enter someone's 6-digit ID. They get a notification and can accept or decline.
- **Profile** — click the bottom-left bar (or the ⚙️ gear) to open Settings. You can set a display name and upload a profile picture. Both are synced to your friends instantly.
- **Nicknames** — right-click any friend to set a nickname. Only you see it.
- **Group chats** — click "Create Group", name it, and pick friends to add. The group owner can rename it, add/remove members, and kick people.
- **Images** — click the 📷 icon in the message box to send an image (max 8MB).

### IDs explained
Think of your 6-digit ID like a phone number — share it with people so they can add you. It never changes unless you clear your browser's local storage.

---

## 🔐 Admin Panel

Go to `/admin` and log in with the password from `admin-config.json`.

| Tab | What it does |
|---|---|
| **Overview** | Live stats — online users, total users, banned count, warnings, groups |
| **Chat Logs** | Full searchable log of every message. Color coded by type. Can be cleared. |
| **Blocklist** | Add/remove censored words. Click Save to apply instantly (no restart needed). |
| **Users & Bans** | See all users, who's online, ban/unban anyone. Banned users are kicked immediately. |
| **⚠️ Warnings** | See who's been flagged by the AI, how many warnings they have, clear warnings. |
| **🖼️ Images** | Browse all images sent in chat, see disk usage, delete individual images. |
| **🟢 Site Online** | Toggle button in the top bar — takes the site offline for everyone instantly. Shows a maintenance screen to all users. Click again to bring it back. |

---

## 🤖 AI Moderation

Every text message is checked by Google Gemini before being delivered. If a message violates the rules:

- **Warning 1 & 2** — message is deleted silently. The sender sees a popup with a warning count (1/3, 2/3) and the reason.
- **Warning 3** — message deleted, sender is automatically banned and disconnected.

**What gets flagged:** hate speech, slurs, explicit sexual content, threats, severe harassment, self-harm encouragement.

**What does NOT get flagged:** normal conversation, mild swearing, arguments, venting.

Admins can clear someone's warnings from the **⚠️ Warnings** tab if a false positive occurs.

If no `GEMINI_API_KEY` is set, messages pass through with no AI moderation (the word blocklist still works).

---

## 🗂️ Data Files

These files are auto-created and managed by the server. Don't edit them manually while the server is running.

| File | Contents |
|---|---|
| `users.json` | Friend lists and pending requests for every user |
| `groups.json` | All group chats and their members |
| `profiles.json` | Display names and profile pictures |
| `warnings.json` | AI warning counts per user |
| `banned.json` | List of banned user IDs |
| `chats.txt` | Append-only log of all messages |
| `public/uploads/` | Images sent in chat |

---

## 🛠️ Common Issues

**Port already in use**
```bash
ss -tlnp | grep 3000       # find what's using the port
kill <PID>                  # kill it, replace with the number you found
```

**Can't connect from another device**
Make sure your firewall allows the port:
```bash
sudo ufw allow 3000
```

**Server crashes on start**
Check that all your JSON files are valid. A common issue is a corrupted `users.json`. You can reset it:
```bash
echo '{}' > users.json
```

**AI moderation is slow**
This is normal if your server has a slow connection to Google's API. Each message waits for the AI response before being delivered. If it's too slow, remove the `GEMINI_API_KEY` to disable it.

**Images not showing**
Make sure the `public/uploads/` folder exists:
```bash
mkdir -p public/uploads
```

---

## 📝 License

Do whatever you want with it.
