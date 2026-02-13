# Quick Start Guide

## ⚡ Fast Setup (5 minutes)

### 1. Enable Google Sign-In in Firebase
1. Go to [Firebase Console](https://console.firebase.google.com/u/0/project/sikder-chat-app)
2. Click **Authentication** → **Sign-in method**
3. Click **Google** → **Enable** → **Save**

### 2. Add SHA-1 Fingerprint
Run this command in PowerShell:
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

Copy the SHA-1 line (looks like: `A1:B2:C3:...`)

Then:
1. Go to [Firebase Project Settings](https://console.firebase.google.com/u/0/project/sikder-chat-app/settings/general)
2. Scroll to "Your apps" → Click your Android app
3. Click "Add fingerprint"
4. Paste SHA-1 → **Save**

### 3. Set Firestore Rules
1. Go to [Firestore Database](https://console.firebase.google.com/u/0/project/sikder-chat-app/firestore)
2. Click **Rules** tab
3. Copy and paste rules from `firestore.rules` file
4. Click **Publish**

### 4. Install and Run
```bash
flutter pub get
flutter run
```

## 🎯 What You Need to Do in Firebase

### ✅ Checklist
- [ ] Enable Google Sign-In (Authentication → Sign-in method)
- [ ] Add SHA-1 fingerprint (Project Settings → Your apps)
- [ ] Publish Firestore rules (Firestore Database → Rules)
- [ ] (Optional) Create composite index when app prompts

### 🔥 Firestore Rules (Copy & Paste)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if true;
      allow create, update: if request.auth != null && request.auth.uid == userId;
    }
    
    match /messages/{messageId} {
      allow read: if true;
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
      allow update, delete: if request.auth != null && resource.data.uid == request.auth.uid;
    }
  }
}
```

## 🐛 Quick Fixes

### "PlatformException" when signing in
→ Did you add SHA-1 fingerprint? (Step 2 above)

### "Permission denied" on Firestore
→ Did you publish Firestore rules? (Step 3 above)

### "Failed to sign in"
→ Did you enable Google Sign-In? (Step 1 above)

### App won't build
```bash
flutter clean
flutter pub get
flutter run
```

## 📱 How to Use the App

1. **First Time**: Sign in with Google → Fill profile (name + DOB)
2. **Chat**: Type message → Click send button
3. **Sign Out**: Click logout icon in top-right

## 🎉 That's It!

The app should now work. If you have issues, check `SETUP.md` for detailed troubleshooting.
