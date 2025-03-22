---
title: "React Native: use custom fonts"
date: 2022-09-10 12:00:00 -0500
categories: [Mobile, React Native]
tags: [React Native, Expo]
---

I love to working with mobile application. Now I start mobile development for my project, and I forgot many of React Native features as well as Expo. Many of them have been updated and deprecated. So I start again from the basic. This post is for setting up the custom fonts.

## Install

Install the expo-font package on your project.

```bash
$ expo install expo-font
```

## Usage

```js
import { useEffect, useCallback } from 'react';
import { Text, View, StyleSheet } from 'react-native';
import { useFonts } from 'expo-font';
import * as SplashScreen from 'expo-splash-screen';

export default function App() {
  const [fontsLoaded] = useFonts({
    'Inter-Black': require('./assets/fonts/Inter-Black.otf'),
  });

  /**
   * without this block:
   * fontFamily "Inter-Black" is not a system font and
   * has not been loaded throught Font.loadAsync
  */
  if (!fontsLoaded) {
    return null;
  }

  return (
    <View style={styles.container} >
      <Text style={{ '{{' }} fontFamily: 'Inter-Black', fontSize: 30 }}>Inter Black</Text>
      <Text style={{ '{{' }} fontSize: 30 }}>Platform Default</Text>
    </View>
  );
}

const styles = StyleSheet.create({ ... });
```

`useFonts` hook returns `loaded` and `error`. With the return value `loaded`, we must handle the case when the fonts are not loaded. (See the comment above).

For better practice, we can also import `SplashScreen` to continue showing the `SplashScreen`, when the fonts are not loaded.

## Better Solution

```js
import { useEffect, useCallback } from 'react';
import { Text, View, StyleSheet } from 'react-native';
import { useFonts } from 'expo-font';
import * as SplashScreen from 'expo-splash-screen';

export default function App() {
  const [fontsLoaded] = useFonts({
    'Inter-Black': require('./assets/fonts/Inter-Black.otf'),
  });

  useEffect(() => {
    /**
     * This asynchronous function prevents SplashScreen
     * from auto hiding while the fonts are loaded.
     */
    async function prepare() {
      await SplashScreen.preventAutoHideAsync();
    }

    prepare();
  }, []);

  /**
   * After the custom fonts have loaded,
   * we can hide the splash screen and display the app screen.
  */
  const onLayoutRootView = useCallback(async () => {
    if (fontsLoaded) {
      await SplashScreen.hideAsync();
    }
  }, [fontsLoaded]);

  if (!fontsLoaded) {
    return null;
  }

  return (
    <View style={styles.container} onLayout={onLayoutRootView}>
      <Text style={{ '{{' }} fontFamily: 'Inter-Black', fontSize: 30 }}>Inter Black</Text>
      <Text style={{ '{{' }} fontSize: 30 }}>Platform Default</Text>
    </View>
  );
}

const styles = StyleSheet.create({ ... });
```

## References:

- [Expo: Font](https://docs.expo.dev/versions/latest/sdk/font/#usefontsmap)
