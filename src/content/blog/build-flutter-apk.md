---
title: How to Build Android APK from Flutter Project
description: A quick guide to building a release APK for Android from a Flutter project.
pubDate: 2026-09-08
categories: [Flutter, Android, Mobile]
---

### 1. Prepare App Icon & Name
Update `android/app/src/main/AndroidManifest.xml` for the app name and use `flutter_launcher_icons` for the icon.

### 2. Build the APK
Run the build command in the project root:
```bash
flutter build apk --release
```
*For split-per-ABI (reduces file size):*
```bash
flutter build apk --split-per-abi
```

### 3. Locate the File
The APK is generated at:
`build/app/outputs/flutter-apk/app-release.apk`

### 4. Pro Tips
- **Obfuscation:** Use `--obfuscate --split-debug-info=/<path>` to shrink code and hide logic.
- **Signing:** For Play Store, you must configure a keystore in `android/key.properties` and update `build.gradle`.
