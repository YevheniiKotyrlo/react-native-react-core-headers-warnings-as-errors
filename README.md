# react-native-react-core-headers-warnings-as-errors

React Native's reproducer template with two changes:

- `ReproducerApp/package.json` uses the nightly built from `main`, `react-native@0.89.0-nightly-20261031-e57dcb3cc`, and the `@react-native/*` packages of the same nightly. The template's `yarn.lock` is removed, since it pinned 0.88.1.
- The app target sets `SWIFT_TREAT_WARNINGS_AS_ERRORS = YES` (`ReproducerApp/ios/ReproducerApp.xcodeproj/project.pbxproj`, Debug and Release). The template's `SceneDelegate.swift` already has `import React`.

## Steps

```sh
cd ReproducerApp
npm install
cd ios
pod install
xcodebuild -workspace ReproducerApp.xcworkspace -scheme ReproducerApp -configuration Debug -sdk iphonesimulator -destination 'generic/platform=iOS Simulator' CODE_SIGNING_ALLOWED=NO build
```

Expected: the app builds. Seen: Xcode builds the `React` Clang module for the Swift import with the target's warnings as errors, and it fails on nullability warnings in React-Core's public headers.
