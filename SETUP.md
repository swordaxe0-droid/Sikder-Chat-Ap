# Chat App Setup Guide

## Overview
This is a single-page Flutter chat application with Google Sign-In authentication, user profile management, and real-time chat functionality using Firebase.

## Features
- ✅ Google Sign-In authentication via Firebase
- ✅ User profile creation (name and date of birth)
- ✅ Real-time global chat with Firestore
- ✅ Messages display in descending order by timestamp
- ✅ Live message updates using StreamBuilder
- ✅ All logic in a single StatefulWidget

## Prerequisites
- Flutter SDK installed
- Android Studio or VS Code with Flutter extensions
- Firebase project set up (already configured)

## Firebase Setup

### 1. Enable Authentication
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your project: `sikder-chat-app`
3. Navigate to **Authentication** → **Sign-in method**
4. Enable **Google** sign-in provider

### 2. Add SHA-1 Fingerprint (Required for Google Sign-In)

For development (debug keystore):
```bash
# On Windows (PowerShell)
cd "C:\Program Files\Android\Android Studio\jbr\bin"
.\keytool -list -v -keystore "$env:USERPROFILE\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android

# Or using Java keytool if available in PATH
keytool -list -v -keystore %USERPROFILE%\.android\debug.keystore -alias androiddebugkey -storepass android -keypass android
```

Copy the **SHA-1** fingerprint and add it to Firebase:
1. Go to Firebase Console → Project Settings
2. Scroll to "Your apps" → Select your Android app
3. Click "Add fingerprint"
4. Paste the SHA-1 fingerprint

### 3. Firebase Security Rules

Set up Firestore security rules:

**Firestore Database Rules:**
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users collection - users can read all, but only write their own
    match /users/{userId} {
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Messages collection - authenticated users can read and write
    match /messages/{messageId} {
      allow read: if true;
      allow create: if request.auth != null && 
                       request.resource.data.uid == request.auth.uid;
      allow update, delete: if request.auth != null && 
                               resource.data.uid == request.auth.uid;
    }
  }
}
```

### 4. Create Firestore Collections

Firestore will automatically create collections when data is first written, but you can create them manually:

1. Go to Firebase Console → Firestore Database
2. Click "Start collection"
3. Create collection named: `users` (will be populated on first sign-in)
4. Create collection named: `messages` (will be populated on first message)

### 5. Firestore Indexes

Create a composite index for efficient message queries:

1. Go to Firestore → Indexes
2. Create a composite index:
   - Collection ID: `messages`
   - Fields:
     - `timestamp` - Descending
   - Query scope: Collection

Or wait for the app to run and Firebase will provide a link to auto-create the index when needed.

## Installation Steps

### 1. Install Dependencies
```bash
flutter pub get
```

### 2. Clean and Build
```bash
flutter clean
flutter pub get
```

### 3. Run the App
```bash
# For Android
flutter run

# Or specify device
flutter devices
flutter run -d <device-id>
```

## App Flow

### 1. Sign-In Screen
- User clicks "Sign in with Google"
- Google account picker appears
- After successful authentication, moves to next step

### 2. Profile Setup (First Time Only)
- If user profile doesn't exist in Firestore
- Form to collect:
  - Name
  - Date of Birth
- Saves to Firestore under `users/{uid}`

### 3. Global Chat Interface
- Real-time message display
- Messages sorted by timestamp (newest at bottom)
- Send new messages with send button
- Each message shows:
  - Sender's email
  - Message text
  - Timestamp

## Firestore Data Structure

### Users Collection
```
users/{uid}
  ├── name: string
  ├── dateOfBirth: timestamp
  ├── email: string
  └── createdAt: timestamp
```

### Messages Collection
```
messages/{messageId}
  ├── text: string
  ├── email: string
  ├── uid: string
  └── timestamp: timestamp
```

## Troubleshooting

### Google Sign-In Fails
1. Ensure SHA-1 fingerprint is added to Firebase
2. Check that Google Sign-In is enabled in Firebase Console
3. Verify `google-services.json` is in `android/app/` directory
4. Try clearing app data and reinstalling

### "PlatformException" Errors
```bash
flutter clean
flutter pub get
flutter run
```

### Firestore Permission Denied
1. Check Firestore security rules
2. Ensure authentication is working
3. Verify user is signed in

### Messages Not Appearing
1. Check internet connection
2. Verify Firestore rules allow read access
3. Create the Firestore index if prompted

### Build Errors
1. Ensure minSdk is set to 21 in `android/app/build.gradle.kts`
2. Check that all dependencies are installed
3. Sync Gradle files in Android Studio

## Testing the App

1. **Sign In**: Use a Google account to sign in
2. **Create Profile**: Fill in name and date of birth
3. **Send Messages**: Type and send messages
4. **Test Real-Time**: Open app on another device/emulator and see messages sync in real-time
5. **Sign Out**: Click logout button in app bar

## Project Structure

```
lib/
  ├── main.dart              # Single-page app with all logic
  └── firebase_options.dart  # Firebase configuration

android/
  ├── app/
  │   ├── build.gradle.kts   # Android build config
  │   └── google-services.json  # Firebase Android config
  └── build.gradle.kts       # Project-level Gradle

pubspec.yaml                  # Flutter dependencies
```

## Key Features Implementation

### Single StatefulWidget
All app logic (login, profile form, chat) is in `_ChatHomePageState` with conditional rendering based on:
- `_user`: Current authenticated user
- `_hasProfile`: Whether user profile exists in Firestore
- `_isLoading`: Loading state

### Real-Time Updates
Uses `StreamBuilder` with Firestore snapshots:
```dart
StreamBuilder<QuerySnapshot>(
  stream: _firestore
      .collection('messages')
      .orderBy('timestamp', descending: true)
      .snapshots(),
  // ...
)
```

### Server Timestamp
Messages use `FieldValue.serverTimestamp()` for consistent timestamps across devices.

## Next Steps

- Add user avatars
- Implement message deletion
- Add message editing
- Create chat rooms/channels
- Add typing indicators
- Implement read receipts
- Add image/file sharing

## Support

If you encounter issues:
1. Check Firebase Console for errors
2. Review Firestore security rules
3. Verify SHA-1 fingerprint is correct
4. Check Flutter and Firebase package versions

## Dependencies Used

- `firebase_core`: ^3.8.1 - Firebase initialization
- `firebase_auth`: ^5.3.3 - Authentication
- `cloud_firestore`: ^5.5.2 - Real-time database
- `google_sign_in`: ^6.2.2 - Google authentication
- `intl`: ^0.20.1 - Date formatting
