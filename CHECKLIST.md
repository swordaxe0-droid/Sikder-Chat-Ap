# 🚀 Launch Checklist

Follow this checklist to get your chat app running in **5 minutes**.

---

## ✅ Pre-Flight Checklist

### Step 1: Firebase Authentication Setup
**Time: 2 minutes**

- [ ] Open [Firebase Console](https://console.firebase.google.com/u/0/project/sikder-chat-app/authentication/providers)
- [ ] Click on **"Sign-in method"** tab
- [ ] Find **"Google"** in the providers list
- [ ] Click **"Google"** → Click the **"Enable"** toggle → Click **"Save"**

### Step 2: Add SHA-1 Fingerprint
**Time: 2 minutes**

#### Get SHA-1:
- [ ] Open **PowerShell**
- [ ] Copy and paste this command:
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass androidpowershell
```
- [ ] Press **Enter**
- [ ] Find the line starting with `SHA1:` (looks like: `A1:B2:C3:D4:...`)
- [ ] Copy the entire SHA-1 fingerprint

#### Add to Firebase:
- [ ] Open [Firebase Project Settings](https://console.firebase.google.com/u/0/project/sikder-chat-app/settings/general)
- [ ] Scroll down to **"Your apps"** section
- [ ] Find your Android app: `com.example.thechat_app`
- [ ] Click **"Add fingerprint"** button
- [ ] Paste your SHA-1 fingerprint
- [ ] Click **"Save"**

### Step 3: Firestore Security Rules
**Time: 1 minute**

- [ ] Open [Firestore Database](https://console.firebase.google.com/u/0/project/sikder-chat-app/firestore)
- [ ] Click **"Rules"** tab at the top
- [ ] Delete all existing rules
- [ ] Copy the rules from `firestore.rules` file (in project root)
- [ ] Paste into the rules editor
- [ ] Click **"Publish"** button

### Step 4: Install & Run
**Time: 1 minute**

- [ ] Open **Terminal** in VS Code or PowerShell
- [ ] Navigate to project folder (if not already there)
- [ ] Run these commands:
```bash
flutter pub get
flutter run
```

---

## 🎯 First Launch

### Expected Flow:
1. **App Opens** → Shows "Welcome to Chat App" screen
2. **Click "Sign in with Google"** → Google account picker appears
3. **Select Google Account** → Sign in
4. **Profile Setup** → Enter name and date of birth
5. **Click "Save Profile"** → Chat screen loads
6. **Type Message** → Click send → Message appears

### If Something Goes Wrong:

#### ❌ "PlatformException(sign_in_failed)"
**Fix**: Did you add SHA-1 fingerprint? (Step 2)
- Go back to Step 2
- Make sure you copied the ENTIRE SHA-1 string
- Wait 2-3 minutes after adding (Firebase needs time to sync)
- Uninstall app and run again

#### ❌ "Permission denied" when loading messages
**Fix**: Did you publish Firestore rules? (Step 3)
- Go to [Firestore Rules](https://console.firebase.google.com/u/0/project/sikder-chat-app/firestore/rules)
- Copy rules from `firestore.rules` file
- Paste and click "Publish"

#### ❌ App won't build
**Fix**: Clean and rebuild
```bash
flutter clean
flutter pub get
flutter run
```

#### ❌ "Failed to initialize Firebase"
**Fix**: Check `google-services.json` file exists at:
`android/app/google-services.json`

---

## 📋 Post-Launch

### Optional Setup:
- [ ] Create Firestore index (Firebase will prompt with a link on first message query)
- [ ] Test on physical device
- [ ] Invite friends to test chat
- [ ] Customize app name and icon

### When Using Multiple Devices:
If testing on different devices/emulators, you may need to add multiple SHA-1 fingerprints:
- Debug keystore SHA-1 (already added)
- Physical device SHA-1 (if different signing key)
- Release keystore SHA-1 (for production)

Each device/build type can have different fingerprints. Add them all to Firebase.

---

## 🎨 Customization Ideas

After it's working, try these:
- [ ] Change app colors (edit `seedColor` in `main.dart`)
- [ ] Modify message bubble design
- [ ] Add user avatars
- [ ] Implement message deletion
- [ ] Create chat rooms

---

## 📚 Documentation Reference

- **Quick Issues?** → Read `QUICKSTART.md`
- **Detailed Setup?** → Read `SETUP.md`
- **OAuth Issues?** → Read `OAUTH_SETUP.md`
- **What Was Built?** → Read `IMPLEMENTATION_SUMMARY.md`

---

## ✨ Success Checklist

Your app is working correctly if:
- [ ] You can sign in with Google
- [ ] Profile form appears on first login
- [ ] Can save profile (name + DOB)
- [ ] Chat screen loads after saving profile
- [ ] Can send messages
- [ ] Messages appear instantly
- [ ] Can see other users' messages (if testing with multiple accounts)
- [ ] Can sign out and sign back in
- [ ] Profile persists (doesn't ask for name/DOB again)

---

## 🎉 You're Done!

If all steps are complete and app is running, you're ready to chat!

**Testing with Multiple Users:**
1. Run app on one device → Sign in with Account A
2. Run app on another device/emulator → Sign in with Account B
3. Send messages from both
4. See them sync in real-time! ⚡

---

## 🆘 Still Stuck?

1. Check Firebase Console for errors
2. Read detailed docs (`SETUP.md`, `OAUTH_SETUP.md`)
3. Run `flutter doctor` to check SDK setup
4. Verify all files are saved
5. Try `flutter clean && flutter pub get && flutter run`

**Common Mistake**: Forgetting to wait 2-3 minutes after adding SHA-1. Firebase needs time to propagate the changes!

---

**Happy Chatting! 💬**
