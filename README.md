# VibeCatch 🚀

Pinterest-inspired visual social discovery app, redesigned with a unique VibeCatch identity. Built for Android + iPhone using Flutter and Firebase.

## Included in this version
- Email/password signup + login
- Auth gate and logout
- Firebase Firestore Vibe feed
- Category filtering
- Image upload to Firebase Storage
- Create/post a Vibe
- Like + save service logic
- Profile screen
- Responsive dark/neon VibeCatch UI

## 1. Install Flutter
Install Flutter from the official Flutter website, then verify:

    flutter doctor

## 2. Get packages
From this folder:

    flutter pub get

## 3. Connect Firebase
Create a Firebase project named VibeCatch in the Firebase Console.
Enable:
- Authentication → Email/Password
- Firestore Database
- Storage

Install FlutterFire CLI and run:

    dart pub global activate flutterfire_cli
    flutterfire configure

Choose Android and iOS. This creates `lib/firebase_options.dart` and platform configuration.

Then change `main.dart` from:

    await Firebase.initializeApp();

To:

    await Firebase.initializeApp(
      options: DefaultFirebaseOptions.currentPlatform,
    );

and add:

    import 'firebase_options.dart';

## 4. Firestore structure

    vibes/{vibeId}
      userId: string
      username: string
      mediaUrl: string
      caption: string
      category: string
      tags: array
      likes: number
      saves: number
      comments: number
      createdAt: timestamp

    vibes/{vibeId}/likes/{uid}
    users/{uid}/saved/{vibeId}

## 5. Security rules (starter)
Use Firebase Console → Firestore → Rules and adapt these before production:

    rules_version = '2';
    service cloud.firestore {
      match /databases/{database}/documents {
        match /vibes/{vibeId} {
          allow read: if true;
          allow create: if request.auth != null && request.resource.data.userId == request.auth.uid;
          allow update, delete: if request.auth != null && resource.data.userId == request.auth.uid;
          match /likes/{uid} { allow read, write: if request.auth != null && request.auth.uid == uid; }
        }
        match /users/{uid}/saved/{vibeId} {
          allow read, write: if request.auth != null && request.auth.uid == uid;
        }
      }
    }

For Storage, allow authenticated users to upload into their own `vibes/{uid}/...` path and read public media as appropriate.

## 6. Run
Android:
    flutter run

iPhone (on macOS with Xcode):
    flutter run -d ios

## Next production milestones
1. Google/Apple login
2. Follow/follower system
3. Real saved screen
4. Comments screen
5. Notifications
6. Search + Explore ranking
7. Video/Reels support
8. Report/block/moderation
9. Image compression + CDN
10. App Store / Play Store release builds

## v0.3 features
- Explore search across captions, usernames, categories and tags.
- Saved Vibes backed by Firestore user subcollection.
- Vibe detail screen with like/save/comment actions.
- Comments stored under each Vibe.
- Follow/unfollow service with follower/following counters.
- Notifications for likes and follows.
- User profile initialization on Firebase Auth changes.

### Firestore shape
users/{uid}
users/{uid}/saved/{vibeId}
users/{uid}/following/{targetUid}
users/{uid}/followers/{followerUid}
users/{uid}/notifications/{notificationId}
vibes/{vibeId}
vibes/{vibeId}/likes/{uid}
vibes/{vibeId}/comments/{commentId}
