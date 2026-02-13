# OAuth Client ID Setup for Google Sign-In

## Why This is Needed
Google Sign-In requires OAuth 2.0 credentials. The `google-services.json` file contains a Web OAuth client, but for Android app authentication, you need to configure it properly with your SHA-1 fingerprint.

## Step-by-Step Setup

### 1. Get Your SHA-1 Fingerprint

#### For Debug Build (Development)
Open PowerShell and run:
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

Look for the line that starts with `SHA1:` and copy the entire fingerprint.

Example output:
```
Certificate fingerprint (SHA1): A1:B2:C3:D4:E5:F6:78:90:12:34:56:78:90:AB:CD:EF:12:34:56:78
```

#### For Release Build (Production)
You'll need to use your release keystore:
```powershell
keytool -list -v -keystore "C:\path\to\your\release.keystore" -alias your-alias-name
```

### 2. Add SHA-1 to Firebase Project

1. Open [Firebase Console](https://console.firebase.google.com/)
2. Select project: **sikder-chat-app**
3. Click gear icon ⚙️ → **Project settings**
4. Scroll down to **Your apps** section
5. Find your Android app: `com.example.thechat_app`
6. Click **Add fingerprint** button
7. Paste your SHA-1 fingerprint
8. Click **Save**

### 3. Download Updated google-services.json (Optional but Recommended)

After adding SHA-1:
1. In Firebase Console → Project settings → Your apps
2. Click the download icon to get updated `google-services.json`
3. Replace the existing file in: `android/app/google-services.json`

### 4. Verify OAuth Client in Google Cloud Console

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Select project: **sikder-chat-app**
3. Navigate to **APIs & Services** → **Credentials**
4. You should see:
   - **Web client (auto created by Google Service)** - Type: Web application
   - **Android client (auto created by Google Service)** - Type: Android (with your SHA-1)

If Android client doesn't exist or doesn't have your SHA-1, it will be auto-created when you add SHA-1 to Firebase.

### 5. Enable Required APIs

In Google Cloud Console, enable these APIs:
1. **Google Sign-In API**
2. **Google Cloud Firestore API**
3. **Firebase Authentication API**

Usually these are auto-enabled when you set up Firebase, but verify:
- Go to **APIs & Services** → **Enabled APIs & services**
- Check if they're listed

## Troubleshooting

### Error: "PlatformException(sign_in_failed)"
**Cause**: SHA-1 not added or incorrect

**Fix**:
1. Double-check you copied the entire SHA-1 fingerprint
2. Make sure you used the correct keystore (debug vs release)
3. Wait 5 minutes after adding SHA-1 (Firebase needs to propagate changes)
4. Uninstall app and reinstall

### Error: "API key not valid"
**Cause**: API key restrictions or not enabled

**Fix**:
1. Go to Google Cloud Console → APIs & Services → Credentials
2. Click on your API key
3. Under "Application restrictions", select "Android apps"
4. Add your package name: `com.example.thechat_app`
5. Add your SHA-1 fingerprint
6. Save

### Error: "Invalid client ID"
**Cause**: OAuth client not properly configured

**Fix**:
1. Verify SHA-1 is added in Firebase
2. Download fresh `google-services.json`
3. Clean and rebuild:
   ```bash
   flutter clean
   flutter pub get
   flutter run
   ```

### Sign-In Works on Emulator but Not on Physical Device
**Cause**: Different SHA-1 fingerprints for debug and device

**Fix**:
- Add multiple SHA-1 fingerprints in Firebase (you can have many)
- Get SHA-1 from the actual signed APK if using release build

## Package Name Verification

Ensure package name matches everywhere:

1. **Firebase Console**: `com.example.thechat_app`
2. **android/app/build.gradle.kts**: 
   ```kotlin
   applicationId = "com.example.thechat_app"
   ```
3. **AndroidManifest.xml**: Auto-generated from applicationId
4. **google-services.json**: 
   ```json
   "package_name": "com.example.thechat_app"
   ```

## Testing OAuth Setup

After setup, test with these steps:
1. Uninstall app completely from device/emulator
2. Run: `flutter clean`
3. Run: `flutter pub get`
4. Run: `flutter run`
5. Click "Sign in with Google"
6. Should see Google account picker
7. Select account
8. Should sign in successfully

## Important Notes

- **Debug keystore** is automatically created by Android SDK
  - Location: `%USERPROFILE%\.android\debug.keystore`
  - Password: `android`
  - Alias: `androiddebugkey`

- **Multiple SHA-1s**: You can add multiple fingerprints (debug, release, multiple developers)

- **SHA-256**: You can also add SHA-256 fingerprints for additional security

- **Regeneration**: If you regenerate keystores, you must update SHA-1 in Firebase

## Quick Reference Commands

### Get SHA-1 (Windows PowerShell)
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

### Get SHA-1 (Alternative - if keytool is in PATH)
```powershell
keytool -list -v -keystore %USERPROFILE%\.android\debug.keystore -alias androiddebugkey -storepass android -keypass android
```

### Get SHA-1 and SHA-256
```powershell
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android | Select-String "SHA1|SHA256"
```

## Resources

- [Firebase Android Setup](https://firebase.google.com/docs/android/setup)
- [Google Sign-In for Android](https://developers.google.com/identity/sign-in/android/start)
- [Firebase Authentication](https://firebase.google.com/docs/auth/android/start)
