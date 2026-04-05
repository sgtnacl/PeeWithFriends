# 🚽 Piss With Friends

A competitive daily pee tracker. Log your bathroom trips, climb the leaderboard, and find out who's most hydrated.

**Live at:** https://sgtnacl.github.io/PeeWithFriends/

---

## Setup

This app requires a free Firebase project for shared real-time data.

### 1. Create a Firebase project

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it (e.g. `piss-with-friends`) → Create
3. Skip Google Analytics if prompted

### 2. Enable Anonymous Authentication

1. In your project, go to **Build → Authentication → Sign-in method**
2. Enable **Anonymous** and save

### 3. Create a Realtime Database

1. Go to **Build → Realtime Database → Create database**
2. Choose a region → Start in **test mode** (you'll lock it down next)
3. After it's created, go to the **Rules** tab and paste:

```json
{
  "rules": {
    "leaderboard": {
      "$date": {
        ".read": true,
        "$uid": {
          ".write": "auth != null && auth.uid === $uid"
        }
      }
    }
  }
}
```

4. Click **Publish**

### 4. Get your config

1. Go to **Project Settings** (gear icon) → **General**
2. Scroll to **Your apps** → click **</>** (Web) → register the app
3. Copy the `firebaseConfig` object values

### 5. Fill in firebase-config.js

Open `firebase-config.js` and replace each `"YOUR_..."` placeholder with your actual values:

```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",
  authDomain:        "my-project.firebaseapp.com",
  databaseURL:       "https://my-project-default-rtdb.firebaseio.com",
  projectId:         "my-project",
  storageBucket:     "my-project.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abc123"
};
```

### 6. Deploy to GitHub Pages

```bash
git add .
git commit -m "Initial commit"
git push
```

Then in your repo: **Settings → Pages → Source → Deploy from branch → main → / (root) → Save**

Your app will be live at `https://sgtnacl.github.io/PeeWithFriends/` within a minute.

---

## How it works

- Each player gets a persistent anonymous identity tied to their browser (Firebase Anonymous Auth)
- Display name is chosen on first visit and saved locally
- Tapping **I Peed!** records a timestamp and increments your daily count
- A **30-minute cooldown** is enforced both client-side and server-side to prevent spam
- The leaderboard updates in real-time for all connected players
- Counts reset each day (data is stored under the current date key)

## Cheating protections

| Protection | Where enforced |
|---|---|
| One identity per browser | Firebase Anonymous Auth (UID persists across sessions) |
| 30-min cooldown | localStorage + Firebase server timestamp check |
| Can't edit other users' data | Firebase security rules (`auth.uid === $uid`) |
| Name spoofing prevented | Name is set per-entry, only writable by owner |
