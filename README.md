# react-native-react-core-headers-warnings-as-errors

React Native's reproducer template with two changes:

- `ReproducerApp/package.json` uses the nightly built from `main`, `react-native@0.89.0-nightly-20261031-e57dcb3cc`, and the `@react-native/*` packages of the same nightly. `main` no longer ships `react-native.config.js`, so the app takes `@react-native-community/cli@21.0.0-alpha.1`, which registers the iOS and Android platforms itself, in place of 20.2.0 and its two platform packages. The template's `yarn.lock` is removed, since it pinned 0.88.1.
- The app target sets `SWIFT_TREAT_WARNINGS_AS_ERRORS = YES` (`ReproducerApp/ios/ReproducerApp.xcodeproj/project.pbxproj`, Debug and Release). The template's `SceneDelegate.swift` already has `import React`.

## Steps

```sh
cd ReproducerApp
npm install
cd ios
pod install
xcodebuild -workspace ReproducerApp.xcworkspace -scheme ReproducerApp -configuration Debug -sdk iphonesimulator -destination 'generic/platform=iOS Simulator' CODE_SIGNING_ALLOWED=NO build
```

Expected: the app builds. Seen: Xcode precompiles the Clang modules the Swift files import with the target's warnings as errors, and the build fails on warnings in React Native's own headers:

- the default prebuilt core: the `React` module, on `RCTWebSocketModule.h:23` and `RCTReactTaggedView.h:29`;
- built from source (`RCT_USE_PREBUILT_RNCORE=0 pod install`): the `React_RCTAppDelegate` module, on `RCTRedBox+Internal.h:17`, `RCTRedBox2Controller+Internal.h:20`, `RCTRedBox2ErrorParser+Internal.h:17` and `:41`, and `RCTUIConfiguratorProtocol.h:40`.
