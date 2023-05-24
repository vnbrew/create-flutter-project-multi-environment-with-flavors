# Create Flutter project step-by-step

## 1. [very_good_cli](https://pub.dev/packages/very_good_cli)

### Installation

```sh
dart pub global activate very_good_cli
```

### How to use

```sh
very_good create base_app --desc "Flutter Base Application" --application-id "com.base.app" --windows false --macos false --linux false
```

## 2. add [firebase_core](https://pub.dev/packages/firebase_core)

```sh
dependencies:
  firebase_core: ^2.3.0
```

## 3. [flutterfire_cli](https://firebase.flutter.dev/docs/cli/)

### Installation

```sh
npm install -g firebase-tools
dart pub global activate flutterfire_cli
```

### How to use

```sh
firebase login
cd to project
```

`For dev`

```sh
flutterfire config --project=com-flutter-base-dev --out=lib/firebase_options_dev.dart --ios-bundle-id=com.base.app.dev --android-package-name=com.base.app.dev

Update code at `main_development.dart`

import 'package:base_app/firebase_options_dev.dart';
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  await bootstrap(() => const App());
}
```

Android: Move file google_service.json -> flavor development (android/app/developmant)
iOS: Move file GoogleService-Info.plist -> ios/Analytics/GoogleService-Info-dev.plist

`For stg`

```sh
flutterfire config --project=com-flutter-base-stg --out=lib/firebase_options_stg.dart --ios-bundle-id=com.base.app.stg --android-package-name=com.base.app.stg

Update code at `main_staging.dart`

import 'package:base_app/firebase_options_stg.dart';
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  await bootstrap(() => const App());
}
```

Android: Move file google_service.json -> flavor development (android/app/staging)
iOS: Move file GoogleService-Info.plist -> ios/Analytics/GoogleService-Info-stg.plist

`For prod`

```sh
flutterfire config --project=com-flutter-base-stg --out=lib/firebase_options.dart --ios-bundle-id=com.base.app --android-package-name=com.base.app

Update code at `main_production.dart`

import 'package:base_app/firebase_options.dart';
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  await bootstrap(() => const App());
}
```

Android: Move file google_service.json -> flavor development (android/app/prduction)
iOS: Move file GoogleService-Info.plist -> ios/Analytics/GoogleService-Info-prod.plist

## 3. [Google Analytics](https://firebase.flutter.dev/docs/analytics/get-started)

```sh
firebase_analytics: ^10.0.6
```

### Android [Checking analytics](https://firebase.google.com/docs/analytics/debugview#android)

    adb shell setprop debug.firebase.analytics.app com.base.app.dev -> enable
    adb shell setprop debug.firebase.analytics.app .none. -> disable

### iOS [Checking analytics](https://firebase.google.com/docs/analytics/debugview#ios+)

Remove reference of GoogleService-Info.plist by Xcode -> -Info.plist -> delete -> Reference
Check bundleid in Packaging -> Product Bundle Indentifier (Runner -> TAGGETS (Runner) -> Build Settings)

```sh
In Xcode, select Product > Scheme > Edit scheme...
Select Run from the left menu.
Select the Arguments tab.
In the Arguments Passed On Launch section, add (-FIRDebugEnabled) or (-FIRDebugDisabled)
-----------------
In GoogleService-Info.plist
<key>IS_ANALYTICS_ENABLED</key>
<true></true>
```

`Should be running iOS by Xcode(not Flutter) to see Debug Analytics`

script for :
Firebase Analytics script (GoogleService-Info.plist)

```sh
PATH_TO_GOOGLE_PLISTS="${PROJECT_DIR}/Analytics"

case "${CONFIGURATION}" in

  "Debug-development" | "Release-development" | "Profile-development" )
        cp -r "$PATH_TO_GOOGLE_PLISTS/GoogleService-Info-dev.plist" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.app/GoogleService-Info.plist" ;;

  "Debug-staging" | "Release-staging" | "Profile-staging" )
        cp -r "$PATH_TO_GOOGLE_PLISTS/GoogleService-Info-stg.plist" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.app/GoogleService-Info.plist" ;;

  "Debug-production" | "Release-production" | "Profile-production" )
      cp -r "$PATH_TO_GOOGLE_PLISTS/GoogleService-Info-prod.plist" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.app/GoogleService-Info.plist" ;;

    *)
        ;;
esac
```

### Docs

https://firebase.google.com/docs/crashlytics/get-started?hl=en&authuser=0&platform=ios
https://firebase.google.com/docs/ios/installation-methods?authuser=0
https://firebase.google.com/docs/crashlytics/get-started?hl=en&authuser=0&platform=ios#set-up-dsym-uploading
https://firebase.google.com/docs/crashlytics/get-started?platform=flutter
https://www.youtube.com/watch?v=cIFLFpKTy7c

### Some error:

Error: require': cannot load such file -- xcodeproj (LoadError)
Possible solutions to try:
There are several possible ways to address this issue. Two are listed here to try.
Install XCode command line tools from Apple
Install the gem "xcodeproj"

To install XCode command line tools...
Go to https://developer.apple.com/download/more/ and login
Search for the command line tools for your version of XCode, download it, then install it.

To instead install the gem "xcodeproj"...
Open a Terminal window
Use the command:
sudo gem install xcodeproj -> `recommend`

Notes:
This how-to was tested to work for a project on macOS High Sierra 10.13.4 and XCode 9.3. Your results may vary. It assumes a little working knowledge of XCode and Terminal.
Best of luck!
