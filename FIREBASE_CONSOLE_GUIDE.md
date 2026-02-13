# Firebase Console Setup Guide

Step-by-step guide with exact locations in Firebase Console.

---

## 🔐 Step 1: Enable Google Sign-In

### Navigate to Authentication:
1. Open [Firebase Console](https://console.firebase.google.com/)
2. Click on your project: **sikder-chat-app**
3. In the left sidebar, click **"Build"** section
4. Click **"Authentication"**

### Enable Google Provider:
1. Click the **"Sign-in method"** tab (top of page)
2. You'll see a list of providers (Google, Email/Password, etc.)
3. Find **"Google"** in the list
4. Click on the **"Google"** row
5. You'll see a toggle switch and form:
   - Toggle the **"Enable"** switch to ON (blue)
   - "Project support email" should auto-fill
6. Click **"Save"** button at the bottom

**Expected Result:** Google should now show "Enabled" in the Sign-in method list

---

## 🔑 Step 2: Add SHA-1 Fingerprint

### Get SHA-1 from Your Computer:

#### Option A: PowerShell (Recommended)
1. Open **PowerShell** (Windows key + X → "Windows PowerShell")
2. Copy and paste this entire command:
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```
3. Press **Enter**
4. Look for output like this:
```
Certificate fingerprints:
     SHA1: A1:B2:C3:D4:E5:F6:78:90:12:34:56:78:90:AB:CD:EF:12:34:56:78
     SHA256: ...
```
5. **Copy the entire SHA1 value** (all the characters after "SHA1:")

#### Option B: If keytool is in your PATH
```powershell
keytool -list -v -keystore %USERPROFILE%\.android\debug.keystore -alias androiddebugkey -storepass android -keypass android
```

#### Option C: Using Android Studio Terminal
1. Open Android Studio
2. Click **"Terminal"** tab at bottom
3. Run the command from Option A

### Add SHA-1 to Firebase:

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select project: **sikder-chat-app**
3. Click the **gear icon (⚙️)** next to "Project Overview" at top-left
4. Click **"Project settings"**
5. Scroll down to **"Your apps"** section
6. Find your Android app with package name: `com.example.thechat_app`
7. Look for **"SHA certificate fingerprints"** section
8. Click the **"Add fingerprint"** button
9. Paste your SHA-1 value (should look like: `A1:B2:C3:...`)
10. Press **Enter** or click the checkmark

**Expected Result:** Your SHA-1 should now appear in the list of fingerprints

**Important:** Wait 2-3 minutes after adding the fingerprint before testing the app. Firebase needs time to propagate the changes.

---

## 🔥 Step 3: Set Up Firestore Database

### Create Firestore Database (if not exists):

1. In Firebase Console, left sidebar → **"Build"** section
2. Click **"Firestore Database"**
3. If you see "Get started", click it:
   - Select **"Start in test mode"** (we'll add rules next)
   - Choose location (closest to your users)
   - Click **"Enable"**

### Set Security Rules:

1. In **Firestore Database** page
2. Click the **"Rules"** tab (top of page, next to "Data", "Indexes", etc.)
3. You'll see a rules editor with existing rules
4. **Delete all existing text** in the editor
5. Open the `firestore.rules` file from your project folder
6. **Copy all the rules** from that file
7. **Paste** into the Firebase rules editor
8. Click **"Publish"** button (top-right)

**The rules should look like this:**
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

**Expected Result:** Rules are published. You should see "Last published: Just now"

---

## 📊 Step 4: Create Firestore Index (Optional - Can Do Later)

You can skip this step. Firebase will automatically prompt you to create the index when needed.

### When Running the App:
1. App tries to query messages ordered by timestamp
2. If index doesn't exist, you'll see an error in console
3. Error will include a **URL link** to create the index
4. Click the link → Firebase opens → Click **"Create Index"**
5. Wait 1-2 minutes for index to build

### Or Create Manually Now:

1. In **Firestore Database** → Click **"Indexes"** tab
2. Click **"Create Index"** button
3. Fill in:
   - **Collection ID:** `messages`
   - **Field:** `timestamp`
   - **Order:** Descending
   - **Query scope:** Collection
4. Click **"Create"**
5. Wait for "Building" to change to "Enabled"

**Expected Result:** Index shows as "Enabled" and query becomes fast

---

## ✅ Verification Checklist

After completing all steps, verify:

### In Firebase Console → Authentication → Sign-in method:
- [ ] Google provider shows as **"Enabled"**

### In Firebase Console → Project Settings → Your apps:
- [ ] SHA-1 fingerprint is listed under your Android app
- [ ] Package name is `com.example.thechat_app`

### In Firebase Console → Firestore Database → Rules:
- [ ] Rules are published (not in draft)
- [ ] Rules include `match /users/{userId}` and `match /messages/{messageId}`

### In Firebase Console → Firestore Database → Data:
- [ ] Database exists (may be empty, that's fine)

---

## 🎯 Quick Links

Copy these links for fast access (replace project ID if needed):

**Authentication:**
```
https://console.firebase.google.com/u/0/project/sikder-chat-app/authentication/providers
```

**Project Settings:**
```
https://console.firebase.google.com/u/0/project/sikder-chat-app/settings/general
```

**Firestore Database:**
```
https://console.firebase.google.com/u/0/project/sikder-chat-app/firestore
```

**Firestore Rules:**
```
https://console.firebase.google.com/u/0/project/sikder-chat-app/firestore/rules
```

**Firestore Indexes:**
```
https://console.firebase.google.com/u/0/project/sikder-chat-app/firestore/indexes
```

---

## 🐛 Common Issues

### "Can't find keytool"
**Solution:** Use full path:
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool ...
```

### "Access denied" to .android folder
**Solution:** Run PowerShell as Administrator

### SHA-1 doesn't work
**Solution:** 
1. Make sure you copied the ENTIRE fingerprint (including all colons)
2. Wait 2-3 minutes after adding
3. Uninstall app and reinstall

### Rules won't publish
**Solution:** Check for syntax errors. Copy rules exactly from `firestore.rules`

### Can't enable Google Sign-In
**Solution:** Make sure you're logged in as project owner, not viewer

---

## 🎉 All Done!

After completing these steps:
1. Authentication is set up ✅
2. SHA-1 is added ✅
3. Firestore rules are published ✅
4. Database is ready ✅

Now run your app:
```bash
flutter pub get
flutter run
```

---

## 📞 Still Need Help?

If something's not working:
1. Double-check each step above
2. Wait 2-3 minutes after making changes
3. Clear app data or reinstall
4. Check [OAUTH_SETUP.md](OAUTH_SETUP.md) for detailed OAuth troubleshooting
5. See [SETUP.md](SETUP.md) for general troubleshooting

**Tip:** Take screenshots of each completed step so you can verify later!
