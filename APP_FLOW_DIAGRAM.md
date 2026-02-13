# 📊 App Flow Diagram

Visual representation of the chat app's user flow and architecture.

---

## 🎯 User Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                         APP STARTS                              │
│                    Firebase.initializeApp()                     │
└───────────────────────────────┬─────────────────────────────────┘
                                │
                                ▼
                    ┌───────────────────────┐
                    │   Check Auth State    │
                    │   _auth.currentUser   │
                    └───────────┬───────────┘
                                │
                ┌───────────────┴───────────────┐
                │                               │
                ▼                               ▼
        ┌──────────────┐              ┌─────────────────┐
        │  User = null │              │  User exists    │
        │ (Not signed in)             │ (Signed in)     │
        └──────┬───────┘              └────────┬────────┘
               │                               │
               ▼                               ▼
    ┌────────────────────┐         ┌────────────────────┐
    │  LOGIN SCREEN  👤  │         │ Check Firestore    │
    │                    │         │ users/{uid}.exists │
    │  - Welcome message │         └─────────┬──────────┘
    │  - Google Sign-In  │                   │
    │    button          │         ┌─────────┴─────────┐
    └─────────┬──────────┘         │                   │
              │                    ▼                   ▼
              │            ┌────────────────┐  ┌──────────────┐
              │            │ Profile = null │  │Profile exists│
              │            │ (No profile)   │  │(Has profile) │
              │            └──────┬─────────┘  └──────┬───────┘
              │                   │                   │
              │ Click              ▼                   ▼
              │ "Sign in"  ┌─────────────────┐ ┌─────────────────┐
              └───────────>│ PROFILE FORM 📝 │ │  CHAT SCREEN 💬 │
                           │                 │ │                 │
                Google     │ - Name input    │ │ - Message list  │
                Sign-In    │ - DOB picker    │ │ - Send message  │
                           │ - Save button   │ │ - Real-time     │
                           │                 │ │   updates       │
                           └────────┬────────┘ └─────────────────┘
                                    │                   │
                                    │ Click             │ Click
                                    │ "Save"            │ "Logout"
                                    ▼                   ▼
                           ┌──────────────────┐ ┌─────────────────┐
                           │ Save to Firestore│ │  Sign Out       │
                           │ users/{uid}      │ │  _auth.signOut()│
                           └────────┬─────────┘ └────────┬────────┘
                                    │                     │
                                    │                     │
                                    └──────────┬──────────┘
                                               │
                                               ▼
                                    ┌────────────────────┐
                                    │   CHAT SCREEN 💬   │
                                    │   (Ready to chat)  │
                                    └────────────────────┘
```

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         Flutter App                             │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                 Single StatefulWidget                     │ │
│  │                  (_ChatHomePageState)                     │ │
│  │                                                           │ │
│  │  State Variables:                                        │ │
│  │  • User? _user               (Current authenticated user)│ │
│  │  • bool _isLoading           (Loading indicator)        │ │
│  │  • bool _hasProfile          (Profile exists in DB)     │ │
│  │                                                           │ │
│  │  ┌─────────────────────────────────────────────────┐    │ │
│  │  │           Conditional Rendering Logic           │    │ │
│  │  │                                                 │    │ │
│  │  │  if (_isLoading)                               │    │ │
│  │  │    return LoadingScreen()                      │    │ │
│  │  │                                                 │    │ │
│  │  │  if (_user == null)                            │    │ │
│  │  │    return LoginScreen()                        │    │ │
│  │  │                                                 │    │ │
│  │  │  if (!_hasProfile)                             │    │ │
│  │  │    return ProfileFormScreen()                  │    │ │
│  │  │                                                 │    │ │
│  │  │  return ChatScreen()                           │    │ │
│  │  └─────────────────────────────────────────────────┘    │ │
│  └───────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                            │
                            │ Uses
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Firebase Services                          │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐   │
│  │   Firebase   │  │   Firebase   │  │  Google Sign-In    │   │
│  │     Auth     │  │  Firestore   │  │     OAuth 2.0      │   │
│  │              │  │              │  │                    │   │
│  │ • Sign-in    │  │ • users/     │  │ • Account picker   │   │
│  │ • Sign-out   │  │ • messages/  │  │ • Auth token       │   │
│  │ • Auth state │  │ • Real-time  │  │ • Credentials      │   │
│  └──────────────┘  └──────────────┘  └────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📊 Data Flow

```
┌────────────────────────────────────────────────────────────────┐
│                      User Interactions                         │
└────────────────┬───────────────────────────────────────────────┘
                 │
    ┌────────────┼────────────┬─────────────────────────┐
    │            │            │                         │
    ▼            ▼            ▼                         ▼
┌────────┐  ┌────────┐  ┌──────────┐           ┌──────────────┐
│Sign-in │  │ Submit │  │  Send    │           │   Sign-out   │
│ Google │  │Profile │  │ Message  │           │              │
└───┬────┘  └───┬────┘  └────┬─────┘           └──────┬───────┘
    │           │            │                         │
    ▼           ▼            ▼                         ▼
┌────────────────────────────────────────────────────────────────┐
│                     Firebase Services                          │
│                                                                │
│  Sign-in Flow:                                                │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │ GoogleSignIn → OAuthCredential → FirebaseAuth.signIn    │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                │
│  Profile Save:                                                │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │ Firestore.collection('users').doc(uid).set({...})       │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                │
│  Message Send:                                                │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │ Firestore.collection('messages').add({...})              │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                │
│  Real-time Updates:                                           │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │ StreamBuilder listens to messages.snapshots()           │ │
│  │ Auto-updates UI when new messages arrive                │ │
│  └──────────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────┘
```

---

## 🔄 State Management Flow

```
                    ┌──────────────────┐
                    │  Auth State      │
                    │  Changes         │
                    └────────┬─────────┘
                             │
                             ▼
              ┌──────────────────────────┐
              │ authStateChanges()       │
              │ .listen((user) {...})    │
              └──────────┬───────────────┘
                         │
        ┌────────────────┴────────────────┐
        │                                 │
        ▼                                 ▼
   ┌────────┐                       ┌────────┐
   │ user   │                       │ user   │
   │ == null│                       │ != null│
   └───┬────┘                       └────┬───┘
       │                                 │
       ▼                                 ▼
   setState(() {              ┌──────────────────┐
     _user = null;            │ Check Firestore  │
     _hasProfile = false;     │ for user profile │
     _isLoading = false;      └─────────┬────────┘
   })                                   │
       │                      ┌─────────┴──────────┐
       │                      ▼                    ▼
       │             ┌─────────────┐      ┌─────────────┐
       │             │userDoc.     │      │userDoc.     │
       │             │exists       │      │!exists      │
       │             └──────┬──────┘      └──────┬──────┘
       │                    │                    │
       │                    ▼                    ▼
       │           setState(() {         setState(() {
       │             _user = user;         _user = user;
       │             _hasProfile = true;   _hasProfile = false;
       │             _isLoading = false;   _isLoading = false;
       │           })                     })
       │                    │                    │
       └────────────────────┴────────────────────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ build() called│
                    │ UI updates    │
                    └───────────────┘
```

---

## 💬 Chat Screen Real-Time Updates

```
┌──────────────────────────────────────────────────────────────┐
│                      Chat Screen                             │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐ │
│  │              StreamBuilder                             │ │
│  │                                                        │ │
│  │  stream: firestore.collection('messages')             │ │
│  │           .orderBy('timestamp', descending: true)     │ │
│  │           .snapshots()                                │ │
│  └────────────────────┬───────────────────────────────────┘ │
│                       │                                     │
│                       │ Listens for changes                │
│                       │                                     │
│                       ▼                                     │
│  ┌────────────────────────────────────────────────────────┐ │
│  │         Firestore Realtime Stream                      │ │
│  │                                                        │ │
│  │  When new message added:                              │ │
│  │    1. Firestore detects change                        │ │
│  │    2. Pushes update to all listeners                  │ │
│  │    3. StreamBuilder receives snapshot                 │ │
│  │    4. Rebuilds ListView automatically                 │ │
│  └────────────────────────────────────────────────────────┘ │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐ │
│  │               Message Display                          │ │
│  │                                                        │ │
│  │  ListView.builder(                                    │ │
│  │    reverse: true,  // Newest at bottom               │ │
│  │    itemCount: messages.length,                       │ │
│  │    itemBuilder: (context, index) {                   │ │
│  │      return MessageBubble(...);                      │ │
│  │    }                                                  │ │
│  │  )                                                    │ │
│  └────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│                    Message Input                             │
│                                                              │
│  TextField → User types → Send button clicked               │
│       │                           │                          │
│       │                           ▼                          │
│       │                  _sendMessage()                     │
│       │                           │                          │
│       └───────────────────────────┼──────────────────────────┘
│                                   │
│                                   ▼
│                    ┌──────────────────────────┐
│                    │ Firestore.add({          │
│                    │   text: "...",           │
│                    │   email: "...",          │
│                    │   uid: "...",            │
│                    │   timestamp: ServerTime  │
│                    │ })                       │
│                    └────────────┬─────────────┘
│                                 │
│                                 ▼
│                    ┌──────────────────────────┐
│                    │  Message saved to        │
│                    │  Firestore               │
│                    └────────────┬─────────────┘
│                                 │
│                                 ▼
│                    ┌──────────────────────────┐
│                    │  StreamBuilder notified  │
│                    │  All clients receive     │
│                    │  new message instantly   │
│                    └──────────────────────────┘
```

---

## 🔐 Firebase Security Flow

```
┌──────────────────────────────────────────────────────────────┐
│                    User Action                               │
└────────────────────────┬─────────────────────────────────────┘
                         │
                         ▼
          ┌──────────────────────────┐
          │   Firebase Request       │
          │   (read/write)           │
          └──────────┬───────────────┘
                     │
                     ▼
          ┌──────────────────────────┐
          │  Firestore Security      │
          │  Rules Check             │
          └──────────┬───────────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
┌──────────────┐          ┌──────────────┐
│ Read Request │          │Write Request │
└──────┬───────┘          └──────┬───────┘
       │                         │
       ▼                         ▼
┌──────────────┐          ┌──────────────┐
│ Check:       │          │ Check:       │
│ allow read:  │          │ allow write: │
│ if true;     │          │ if auth!=null│
└──────┬───────┘          └──────┬───────┘
       │                         │
   ┌───┴────┐              ┌─────┴──────┐
   ▼        ▼              ▼            ▼
┌──────┐ ┌────┐      ┌────────┐  ┌──────┐
│Allow │ │Deny│      │ Allow  │  │ Deny │
└──┬───┘ └──┬─┘      └───┬────┘  └───┬──┘
   │        │            │           │
   └────────┼────────────┴───────────┘
            │
            ▼
     ┌──────────────┐
     │   Return     │
     │   Result     │
     └──────────────┘
```

---

## 🎨 UI Component Hierarchy

```
MaterialApp
  └── ChatHomePage (StatefulWidget)
       └── _ChatHomePageState (State)
            │
            ├─ if (_isLoading)
            │   └── Scaffold
            │        └── Center
            │             └── CircularProgressIndicator
            │
            ├─ if (_user == null)
            │   └── Scaffold
            │        └── Center
            │             └── Column
            │                  ├── Icon (chat bubble)
            │                  ├── Text (welcome)
            │                  └── ElevatedButton (Google Sign-in)
            │
            ├─ if (!_hasProfile)
            │   └── Scaffold
            │        ├── AppBar
            │        │    ├── Text (title)
            │        │    └── IconButton (logout)
            │        └── SingleChildScrollView
            │             └── Column
            │                  ├── Icon (person)
            │                  ├── Text (welcome email)
            │                  ├── TextField (name)
            │                  ├── InputDecorator (DOB)
            │                  └── ElevatedButton (save)
            │
            └─ else (chat screen)
                └── Scaffold
                     ├── AppBar
                     │    ├── Text (title)
                     │    └── IconButton (logout)
                     └── Column
                          ├── Expanded
                          │    └── StreamBuilder
                          │         └── ListView.builder
                          │              └── MessageBubble (each message)
                          │                   └── Container
                          │                        └── Column
                          │                             ├── Text (email)
                          │                             ├── Text (message)
                          │                             └── Text (timestamp)
                          │
                          └── Container (input area)
                               └── Row
                                    ├── TextField (message input)
                                    └── IconButton (send)
```

---

## 📦 Dependency Graph

```
┌─────────────────────────────────────────────────────────────┐
│                     Flutter App                             │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           │ depends on
                           │
      ┌────────────────────┼────────────────────┐
      │                    │                    │
      ▼                    ▼                    ▼
┌───────────┐      ┌──────────────┐    ┌──────────────┐
│firebase   │      │firebase_auth │    │cloud_        │
│_core      │      │              │    │firestore     │
│           │      │ depends on   │    │              │
│           │◄─────┤ firebase_core│    │ depends on   │
│           │      └──────────────┘    │ firebase_core│
│           │                          └──────┬───────┘
│           │                                 │
│           │◄────────────────────────────────┘
└─────┬─────┘
      │
      ▼
┌───────────┐      ┌──────────────┐
│google     │      │intl          │
│_sign_in   │      │              │
│           │      │ (date format)│
│ depends on│      └──────────────┘
│ firebase  │
│ _auth     │
└───────────┘
```

---

## 🚀 Deployment Flow

```
┌─────────────────┐
│ Development     │
│                 │
│ 1. Write code   │
│ 2. Test locally │
│ 3. Fix bugs     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Firebase Setup  │
│                 │
│ 1. Enable Auth  │
│ 2. Add SHA-1    │
│ 3. Set rules    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Build           │
│                 │
│ flutter build   │
│ apk --release   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Distribute      │
│                 │
│ - Direct APK    │
│ - Play Store    │
│ - TestFlight    │
└─────────────────┘
```

---

## 📊 Summary

This chat app uses:
- **Single-page architecture** with conditional rendering
- **Real-time Firestore streams** for instant message updates
- **Firebase Auth** for secure Google Sign-In
- **Stateful widget** for state management
- **StreamBuilder** for reactive UI updates

The flow is simple:
1. Sign in → 2. Create profile → 3. Chat in real-time

All in **one StatefulWidget** with **~500 lines of code**! 🎉
