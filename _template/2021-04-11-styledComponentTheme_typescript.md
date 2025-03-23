---
title: Theme with styled-components and TypeScript
date: 2021-04-11 12:00:00 -0500
categories: [JavaScript, TypeScript]
tags: [TypeScript, styled-components, theme]
---

Project 를 진행하면서 `styled-components` package와 TypeScript 를 이용한 theme 설정하는 방법에 대해 정리한다.

## Installation

```
$ npm install @types/styled-components
```

## Style Declaration

Create `styled.d.ts`

```typescript
import "styled-components";
interface IPalette {
  main: string;
  contrast: string;
}
declare module "styled-components" {
  export interface DefaultTheme {
    backgroundColor: string;
    textColor: string;
    primary: IPalette;
    secondary: IPalette;
    common: {
      black: string;
      white: string;
    };
  }
}
```

## Create a theme

Create `theme.ts`

```typescript
import { DefaultTheme } from "styled-components";
export const defaultTheme: DefaultTheme = {
  backgroundColor: "white",
  textColor: "black",
  common: {
    black: "black",
    white: "white",
  },
  primary: {
    main: "blue",
    contrast: "orange",
  },
  secondary: {
    main: "green",
    contrast: "brown",
  },
};

export const lightTheme = {
  backgroundColor: "white",
  textColor: "black",
  common: {
    black: "black",
    white: "white",
  },
  primary: {
    main: "blue",
    contrast: "orange",
  },
  secondary: {
    main: "green",
    contrast: "brown",
  },
};

export const darkTheme = {
  backgroundColor: "darkgray",
  textColor: "white",
  common: {
    black: "black",
    white: "white",
  },
  primary: {
    main: "lightblue",
    contrast: "orange",
  },
  secondary: {
    main: "lightgreen",
    contrast: "brown",
  },
};
```

## Theme Provider

In `App.tsx`

Apollo client 에서 반응형 변수 (Reactive variable) 을 사용하여 theme 의 상태를 정한다.

```typescript
import { ThemeProvider } from "styled-components";
import { darkTheme, lightTheme } from "./theme";

export default function App() {
  (...)
  const isDarkMode = useReactiveVar(isDarkModeVar);
  const preloadAssets = () => {
    (...)
  };
  const preload = async () => {
    (...)
    const theme = await AsyncStorage.getItem("darkMode");
    if (theme) {
      isDarkModeVar(true);
    }
    return preloadAssets();
  };

  return (
    <ThemeProvider theme={isDarkMode ? lightTheme : darkTheme}>
      <NavigationContainer>
        {isLoggedIn ? <LoggedInNav /> : <LoggedOutNav />}
      </NavigationContainer>
    </ThemeProvider>
  );
}
```

## Use theme from Components

1. Using `styled-components`

   ```typescript
   const Container = styled.View`
     background-color: ${(props) => props.theme.backgroundColor};
   `;
   ```

2. Add theme on render

   ```typescript
   import { useTheme } from "styled-components";
   const Card = () => {
     const theme = useTheme();
     return (
       <View>
         <Text style={{ '{{' }} color: theme.textColor }}>Test String</Text>
       </View>
     );
   };
   export default Card;
   ```

## References:

- [Theme with styled-components and TypeScript](https://medium.com/rbi-tech/theme-with-styled-components-and-typescript-209244ec15a3)
