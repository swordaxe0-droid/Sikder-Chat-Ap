# Chat App 💬

A real-time Flutter chat application with Google Sign-In authentication and Firebase Cloud Firestore.

## 🌟 Features

- **Google Sign-In** - Secure authentication via Firebase
- **User Profiles** - Name and date of birth saved in Firestore
- **Real-Time Chat** - Live message updates with StreamBuilder
- **Global Chat Room** - All users can see and send messages
- **Beautiful UI** - Modern Material Design 3 interface
- **Single Page App** - All logic in one StatefulWidget

## 🚀 Quick Start

**New here? Start with the checklist:**

👉 **[CHECKLIST.md](CHECKLIST.md)** - 5-minute setup guide

### Other Documentation

- **[QUICKSTART.md](QUICKSTART.md)** - Fast setup with minimal explanation
- **[SETUP.md](SETUP.md)** - Comprehensive setup and troubleshooting
- **[OAUTH_SETUP.md](OAUTH_SETUP.md)** - Detailed OAuth and SHA-1 setup
- **[IMPLEMENTATION_SUMMARY.md](IMPLEMENTATION_SUMMARY.md)** - What was built and how it works
- **[firestore.rules](firestore.rules)** - Firestore security rules (copy to Firebase Console)

## 📋 What You Need to Do

### 3 Firebase Setup Steps:

1. ✅ Enable Google Sign-In in Firebase Console
2. ✅ Add SHA-1 fingerprint to Firebase  
3. ✅ Publish Firestore security rules

Then run:
```bash
flutter pub get
flutter run
```

**See [CHECKLIST.md](CHECKLIST.md) for detailed steps.**

## 🏗️ Architecture

### Single StatefulWidget Design
All app logic in one widget with three conditional states:

1. **Login Screen** - When user is not authenticated
2. **Profile Form** - When authenticated but no profile in Firestore
3. **Chat Interface** - When authenticated with profile

### Tech Stack

- **Flutter** - UI framework
- **Firebase Auth** - Google Sign-In authentication
- **Cloud Firestore** - Real-time database
- **Google Sign-In** - OAuth provider
- **Material 3** - Modern design system

## 📱 Screenshots

### Flow
```
Login Screen → Profile Setup → Global Chat
    ↓              ↓               ↓
  Google      Name + DOB    Real-time messages
  Sign-In        Form         with timestamps
```

## 📊 Data Structure

### Firestore Collections

**users/{uid}**
```javascript
{
  name: "John Doe",
  dateOfBirth: Timestamp,
  email: "john@example.com",
  createdAt: Timestamp
}
```

**messages/{auto-id}**
```javascript
{
  text: "Hello, world!",
  email: "john@example.com", 
  uid: "user-id",
  timestamp: Timestamp
}
```

## 🛠️ Development

### Requirements
- Flutter SDK 3.8.1 or higher
- Android Studio / VS Code
- Firebase project (already configured)

### Run Locally
```bash
# Install dependencies
flutter pub get

# Run on connected device
flutter run

# Run on specific device
flutter devices
flutter run -d <device-id>
```

### Build Release
```bash
# Android APK
flutter build apk --release

# Android App Bundle (for Play Store)
flutter build appbundle --release
```

## 🔒 Security

Firestore security rules ensure:
- Users can only write their own profile
- Authenticated users can create messages
- Users can only edit/delete their own messages
- Everyone can read messages (public chat)

See `firestore.rules` for complete rules.

## 🐛 Troubleshooting

### Sign-in fails?
→ Add SHA-1 fingerprint (see [OAUTH_SETUP.md](OAUTH_SETUP.md))

### Permission denied?
→ Publish Firestore rules (see [CHECKLIST.md](CHECKLIST.md))

### Build errors?
```bash
flutter clean
flutter pub get
flutter run
```

**More help:** See [SETUP.md](SETUP.md) for detailed troubleshooting.

## 📝 Project Structure

```
lib/
├── main.dart              # Single-page app with all logic
└── firebase_options.dart  # Firebase configuration

android/
├── app/
│   ├── build.gradle.kts   # Android build config
│   └── google-services.json  # Firebase credentials
└── build.gradle.kts       # Project-level Gradle

Documentation/
├── CHECKLIST.md           # Quick setup checklist
├── QUICKSTART.md          # 5-minute guide
├── SETUP.md               # Comprehensive guide
├── OAUTH_SETUP.md         # SHA-1 and OAuth setup
├── IMPLEMENTATION_SUMMARY.md  # What was built
└── firestore.rules        # Firestore security rules
```

## 🎯 Key Features Implementation

### Authentication
- Google Sign-In via Firebase Auth
- Auth state listener for automatic login
- Profile existence check in Firestore

### Real-Time Updates
```dart
StreamBuilder<QuerySnapshot>(
  stream: _firestore
    .collection('messages')
    .orderBy('timestamp', descending: true)
    .snapshots(),
  // Live message updates!
)
```

### State Management
```dart
if (_isLoading) return LoadingScreen;
if (_user == null) return LoginScreen;
if (!_hasProfile) return ProfileForm;
return ChatScreen;
```

## 🚧 Future Enhancements

- [ ] User avatars
- [ ] Message deletion
- [ ] Private chat rooms
- [ ] Image/file sharing
- [ ] Push notifications
- [ ] Typing indicators
- [ ] Read receipts

## 📚 Learn More

- [Firebase Documentation](https://firebase.google.com/docs)
- [Flutter Documentation](https://flutter.dev/docs)
- [Material Design 3](https://m3.material.io/)

## 📄 License

This project is created for educational purposes.

## 🤝 Contributing

This is a learning project. Feel free to fork and modify!

## 📞 Support

Having issues? Check these docs in order:
1. [CHECKLIST.md](CHECKLIST.md) - Quick setup
2. [QUICKSTART.md](QUICKSTART.md) - Fast troubleshooting
3. [SETUP.md](SETUP.md) - Detailed guide
4. [OAUTH_SETUP.md](OAUTH_SETUP.md) - Sign-in issues

---

**Made with ❤️ using Flutter and Firebase**

## Getting Started

This project is a starting point for a Flutter application.

A few resources to get you started if this is your first Flutter project:

- [Lab: Write your first Flutter app](https://docs.flutter.dev/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://docs.flutter.dev/cookbook)

For help getting started with Flutter development, view the
[online documentation](https://docs.flutter.dev/), which offers tutorials,
samples, guidance on mobile development, and a full API reference.
