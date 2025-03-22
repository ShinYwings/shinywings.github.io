---
title: "React Native: build fail on iOS using 'react-native-vector-icons'"
date: 2022-12-15 12:00:00 -0500
categories: [Mobile, React Native]
tags: [React Native, Bugfix, iOS, Xcode, Build Fail]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

`Xcode`에서 `react-native-vector-icons`을 등록하는 과정에서 다중의 Fonts path로 인한 오류로 짐작된다. `pod install`에서 등록되는 `[CP] Copy Pods Resources`와 `Copy Bundle Resources`에 충돌인데 `Copy Bundle Resources` 안의 Fonts를 지워주는 것으로 오류를 해결 할 수 있다.

## Error Message

> <pre>
> error Failed to build iOS project. We ran "xcodebuild" command but it exited with error code 65. To debug build logs further, consider building your app with Xcode.app, by opening MyApp.xcworkspace.
> Command line invocation:
>     /Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild -workspace MyApp.xcworkspace -configuration Debug -scheme MyApp -destination id=MyApp_id
> 
> User defaults from command line:
>     IDEPackageSupportUseBuiltinSCM = YES
> 
> Prepare packages
> 
> Computing target dependency graph and provisioning inputs
> 
> Create build description
> Build description signature: &lt;build-description-signature&gt;
> Build description path: /Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Intermediates.noindex/XCBuildData/&lt;build-description-signature&gt;-desc.xcbuild
> 
> note: Building targets in dependency order
> error: Multiple commands produce '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/AntDesign.ttf'
>     note: Target 'MyApp' (project 'MyApp') has copy command from '/Users/username/Documents/workspace/MyApp/node_modules/react-native-vector-icons/Fonts/AntDesign.ttf' to '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/AntDesign.ttf'
>     note: That command depends on command in Target 'MyApp' (project 'MyApp'): script phase “[CP] Copy Pods Resources”
> error: Multiple commands produce '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/Entypo.ttf'
>     note: Target 'MyApp' (project 'MyApp') has copy command from '/Users/username/Documents/workspace/MyApp/node_modules/react-native-vector-icons/Fonts/Entypo.ttf' to '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/Entypo.ttf'
>     note: That command depends on command in Target 'MyApp' (project 'MyApp'): script phase “[CP] Copy Pods Resources”
> error: Multiple commands produce '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/EvilIcons.ttf'
>     note: Target 'MyApp' (project 'MyApp') has copy command from '/Users/username/Documents/workspace/MyApp/node_modules/react-native-vector-icons/Fonts/EvilIcons.ttf' to '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/EvilIcons.ttf'
>     note: That command depends on command in Target 'MyApp' (project 'MyApp'): script phase “[CP] Copy Pods Resources”
> error: Multiple commands produce '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/Feather.ttf'
>     note: Target 'MyApp' (project 'MyApp') has copy command from '/Users/username/Documents/workspace/MyApp/node_modules/react-native-vector-icons/Fonts/Feather.ttf' to '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/Feather.ttf'
>     note: That command depends on command in Target 'MyApp' (project 'MyApp'): script phase “[CP] Copy Pods Resources”
> error: Multiple commands produce '/Users/username/Library/Developer/Xcode/DerivedData/MyApp/Build/Products/Debug-iphonesimulator/MyApp.app/FontAwesome.ttf'
>  </pre>

## Solution

1. `react-native-vector-icons`를 설치한다.

2. Xcode 를 열어 MyApp project 에서 New Group: `Fonts`를 만들고 `Add files to 'MyApp'`으로 사용할 Fonts(`.ttf`)를 선택한다.

   ```
   MyApp
   ├── MyApp
   │   ├── Fonts <- create new group: Fonts and add fonts here
   │   ├── AppDelegate.h
   │   ├── AppDelegate.mm
   │   ├── Images
   │   ├── Info
   │   ├── LaunchScreen
   │   └── main
   ├── Libraries
   ├── MyAppTests
   ├── Products
   ├── Frameworks
   └── Pod
   ```
   {: file='File Path'}

3. `MyApp` > `Build Phase` > `Copy Bundle Resources`로 이동하여 Fonts에 이전 Step에서 넣었던 Fonts들을 지워준다.

4. `react-native.config.js` 안에 `react-native-vector-icons` dependencies settings 한다

   ```js
   module.exports = {
     dependencies: {
       "react-native-vector-icons": {
         platforms: {
           ios: null,
         },
       },
     },
   };
   ```

## Reference

- [Solution: Multiple commands produce](https://github.com/oblador/react-native-vector-icons/issues/1074#issuecomment-534053163)
