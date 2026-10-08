# Flex Smart Queue

A role draft for a League of Legends flex 5-stack. The captain creates a squad and shares a join link. Everyone ranks their own roles from favorite to least favorite. Nobody can refuse a role outright; the ranking decides how often you get each one. Each draft balances preferences, rotates people off roles they keep getting, and gives priority to whoever has been stuck on off-roles. Wins and losses are recorded but don't affect the draft.

It's a static site (GitHub Pages) with Firebase for the shared data. Friends don't need an account: the site signs them in as a guest in their browser.

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app |
| `firebase-config.js` | Your Firebase project's web config (you fill this in) |
| `firestore.rules` | Database security rules (you paste these into Firebase) |

---

## Setup (about 15 minutes, free)

### 1. Create a Firebase project

1. Go to <https://console.firebase.google.com> and sign in with a Google account.
2. Click **Create a project** (or **Add project**).
3. Name it, e.g. `flex-smart-queue`. Click **Continue**.
4. Turn **Google Analytics off** (not needed). Click **Create project**, wait, then **Continue**.

The free **Spark** plan is plenty for a squad. You don't need to add a credit card.

### 2. Register a web app and copy its config

1. On the project overview page, click the **`</>`** (Web) icon under "Get started by adding Firebase to your app".
2. App nickname: `flex-smart-queue`. Leave **Firebase Hosting unchecked**. Click **Register app**.
3. You'll see a code block with `const firebaseConfig = { apiKey: ..., authDomain: ..., ... }`.
4. Copy those values into `firebase-config.js` in this folder, replacing the placeholders. Keep the `export const firebaseConfig = {...}` line as it is.
5. Click **Continue to console**.

You can find this config again later under the gear icon → **Project settings** → **General** → **Your apps**.

### 3. Turn on guest sign-in

1. Left sidebar: **Build → Authentication** → **Get started**.
2. **Sign-in method** tab → **Anonymous** → toggle **Enable** → **Save**.
3. Optional but recommended: also enable **Google** (pick a support email, **Save**). This powers the "Keep me on other devices" button so you can use the same player on your phone and PC, and keep captain rights if you clear your browser.

### 4. Create the database

1. Left sidebar: **Build → Firestore Database** → **Create database**.
2. Pick a location near you (e.g. `nam5` for the US, `eur3` for Europe). You can't change this later.
3. Choose **Start in production mode**. Click **Create**.

### 5. Paste the security rules

1. In Firestore, open the **Rules** tab.
2. Delete everything there and paste the full contents of `firestore.rules`.
3. Click **Publish**.

These rules are what stop people from editing each other or the settings. Without them the database is locked (production mode) and the app won't save anything.

### 6. Try it locally (optional)

The page uses JavaScript modules, so double-clicking `index.html` won't work. Run a tiny local server instead:

```sh
cd ~/FlexSmartQueue
python3 -m http.server 8000
```

Open <http://localhost:8000>. `localhost` is already allowed by Firebase. Create a test squad to check everything works. Stop the server with `Ctrl+C`.

### 7. Put it on GitHub Pages

**Option A: GitHub website, no terminal**

1. Go to <https://github.com/new>. Name the repo, e.g. `flex-smart-queue`. Set it to **Public** (free GitHub Pages needs a public repo). Click **Create repository**.
2. Click **uploading an existing file**, drag in `index.html`, `firebase-config.js`, `firestore.rules` and `README.md`, then **Commit changes**.

**Option B: terminal**

```sh
cd ~/FlexSmartQueue
git init
git add index.html firebase-config.js firestore.rules README.md
git commit -m "Flex Smart Queue"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/flex-smart-queue.git
git push -u origin main
```

**Then turn on Pages:**

1. In the repo: **Settings → Pages**.
2. **Source**: *Deploy from a branch*. **Branch**: `main`, folder `/ (root)`. **Save**.
3. After a minute or two, the page shows your URL: `https://YOUR-USERNAME.github.io/flex-smart-queue/`.

### 8. Allow your GitHub Pages domain in Firebase

Firebase blocks sign-in from domains it doesn't know.

1. Firebase console → **Authentication** → **Settings** tab → **Authorized domains**.
2. Click **Add domain** and enter `YOUR-USERNAME.github.io` (just the domain, no `https://` and no repo path).

### 9. Create your squad and invite people

1. Open your GitHub Pages URL.
2. Enter a squad name, your name and your role order, then **Create squad**. You're the captain.
3. In **Invite your squad**, click **Copy link** and send it to your friends (Discord, etc.).
4. They open it, type their name, rank their roles, and click **Join squad**.

---

## How the draft works

- **Preferences:** each player drags their roles into order, 1st to 5th. Getting a lower choice costs more, but every role is possible.
- **Rotation:** roles you played in recent games cost extra, with the most recent games counting most. Repeating a role you dislike costs much more than repeating your main.
- **Fairness ("Owed"):** players who've recently had off-roles get more weight in the next draft.
- **Results:** W/L is kept as your squad's record only. It never changes who plays what.
- The app tries all 120 ways to assign roles and picks the fairest. **Another fair option** cycles through ones that are almost as good. Tap two players to swap them by hand.

The captain can tune all of this under **Draft settings**.

## Who can do what

| | Captain | Squad member | Someone with the link who hasn't joined |
| --- | --- | --- | --- |
| See the squad, drafts, games | ✓ | ✓ | ✓ |
| Join and edit own name/roles | ✓ | ✓ | ✓ (by joining) |
| Edit someone else's roles | Only people the captain added by name | ✗ | ✗ |
| Roll drafts, lock in, mark W/L | ✓ | ✗ | ✗ |
| Swap people between starting 5 and reserves | ✓ | ✗ | ✗ |
| Change draft settings | ✓ | ✗ | ✗ |
| Remove players, delete games | ✓ | ✗ (can leave) | ✗ |
| Delete the whole squad | ✓ | ✗ | ✗ |

**Starting 5 and reserves:** the first five people to join are the starting 5. Anyone after that joins as a reserve. The captain swaps people in and out from the Squad panel, and each person's game history and "Owed" meter follow them.

The Firestore rules enforce this, not just the page. The join link is the only way in, so share it only with your squad.

## Things to know

- **Guests are tied to their browser.** If someone clears site data or opens the link on a new device, they show up as a new player. Fix: use **Keep me on other devices (Google)** at the bottom of the page (needs Google enabled in step 3). This matters most for the captain, since captain rights belong to that sign-in.
- **Updating the app:** edit `index.html`, then upload or push it again. GitHub Pages redeploys in about a minute. Squad data lives in Firebase, so it isn't affected.
- **Changed the rules?** Paste the new `firestore.rules` into the Firestore **Rules** tab and **Publish**.
