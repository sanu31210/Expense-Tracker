# Setting up free cross-device sync (Firebase)

This connects your Ledger app to a free Firebase project so your data
follows you across your phone, laptop, etc. Firebase's Spark plan
(Auth + Firestore) is genuinely free, no credit card required.

## 1. Create the Firebase project
1. Go to https://console.firebase.google.com → **Add project**
2. Name it anything (e.g. "ledger-app") → skip Google Analytics (not needed) → **Create**

## 2. Enable Google sign-in
1. In the left sidebar: **Build → Authentication → Get started**
2. Under "Sign-in method," enable **Google** → set a support email → **Save**

## 3. Create the Firestore database
1. Left sidebar: **Build → Firestore Database → Create database**
2. Choose **Start in production mode** → pick any region close to you → **Enable**

## 4. Set the security rules
1. In Firestore, go to the **Rules** tab and replace the contents with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/ledger/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

2. Click **Publish**. This ensures only you can read or write your own data.

## 5. Register a web app and get your config
1. Project settings (gear icon, top left) → scroll to "Your apps" → click the **</>** (web) icon
2. Give it a nickname (e.g. "ledger-web") → **Register app**
3. Copy the `firebaseConfig` object shown — it looks like this:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "ledger-app-xxxxx.firebaseapp.com",
  projectId: "ledger-app-xxxxx",
  storageBucket: "ledger-app-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef123456",
};
```

## 6. Paste it into index.html
Open `index.html`, find the `firebaseConfig` block near the top of the
`<script>` section, and replace the placeholder values with your real
ones from step 5.

## 7. Authorize your GitHub Pages domain
1. Back in Firebase: **Authentication → Settings → Authorized domains**
2. Click **Add domain** and add your GitHub Pages domain, e.g.
   `yourusername.github.io`
   (Without this step, sign-in will fail with an "unauthorized domain" error.)

## 8. Deploy
Upload all 5 files (`index.html`, `manifest.json`, `sw.js`, `icon-192.png`,
`icon-512.png`) to your GitHub repo as before, and enable GitHub Pages
(Settings → Pages → main branch → root).

## 9. Sign in
Open your GitHub Pages URL, tap **Sign in with Google**, and use the same
Google account on every device you want synced. Your data now lives in
your Firestore database and updates live across devices.

---

### Notes
- Free tier limits: 50,000 reads / 20,000 writes / 1 GB storage per day —
  far more than a personal expense tracker will ever use.
- Your `firebaseConfig` values are not secret; Firebase apps are designed
  to have this visible in client code. Security comes from the rules in
  step 4, not from hiding these values.
- If you ever want to add a second person to see your data, that's a
  bigger change (shared docs) — ask if you'd like that added later.
