# React Native Interview Questions & Answers

A comprehensive guide covering React Native fundamentals through advanced concepts, the New Architecture, navigation, native modules, performance, styling, state management, testing, and deployment.

---

## Table of Contents

1. [React Native Fundamentals](#1-react-native-fundamentals)
2. [Core Components & JSX](#2-core-components--jsx)
3. [Styling & Layout](#3-styling--layout)
4. [Architecture: Bridge, JSI & the New Architecture](#4-architecture-bridge-jsi--the-new-architecture)
5. [Navigation](#5-navigation)
6. [State Management](#6-state-management)
7. [Networking & Data Fetching](#7-networking--data-fetching)
8. [Native Modules & Native UI Components](#8-native-modules--native-ui-components)
9. [Platform-Specific Code](#9-platform-specific-code)
10. [Lists & Performance-Critical Components](#10-lists--performance-critical-components)
11. [Animations & Gestures](#11-animations--gestures)
12. [Performance Optimization](#12-performance-optimization)
13. [Storage & Persistence](#13-storage--persistence)
14. [Push Notifications & Background Tasks](#14-push-notifications--background-tasks)
15. [Permissions & Device APIs](#15-permissions--device-apis)
16. [Debugging & DevTools](#16-debugging--devtools)
17. [Testing](#17-testing)
18. [Build, Deployment & Over-the-Air Updates](#18-build-deployment--over-the-air-updates)
19. [Expo vs Bare React Native](#19-expo-vs-bare-react-native)
20. [TypeScript with React Native](#20-typescript-with-react-native)
21. [Miscellaneous & Best Practices](#21-miscellaneous--best-practices)

---

## 1. React Native Fundamentals

### Q1. What is React Native?
React Native is an open-source framework (created by Meta) for building native mobile applications for iOS and Android using JavaScript/TypeScript and React. Unlike hybrid frameworks that render a webview, React Native components compile down to **actual native UI elements** (`UIView` on iOS, `android.view.View` on Android), giving apps native look, feel, and performance while sharing a single JavaScript codebase across platforms.

### Q2. How does React Native differ from React (web)?
React targets the browser DOM, rendering HTML elements (`<div>`, `<span>`). React Native targets **native platform widgets** instead, using its own set of core components (`<View>`, `<Text>`, `<Image>`) that map to real native views — there's no DOM, no CSS files, and no HTML. The React programming model (components, props, state, hooks, JSX, unidirectional data flow) is otherwise the same, which is what makes React knowledge transferable.

### Q3. How does React Native differ from hybrid frameworks (e.g., Cordova/Ionic with a WebView)?
WebView-based hybrid frameworks run the entire UI inside an embedded browser rendering HTML/CSS/JS, which can suffer from performance limitations and a less native feel/interaction model. React Native instead renders genuine native components directly, and JavaScript only handles the logic/orchestration layer — communicating with native code to instantiate and update actual platform UI elements, resulting in closer-to-native performance and look-and-feel.

### Q4. How does React Native differ from Flutter?
Flutter (Google) uses its own **Dart** language and rendering engine (Skia, and more recently Impeller), drawing its own widgets pixel-by-pixel rather than using native platform components — giving very consistent cross-platform rendering but a UI that doesn't automatically pick up native platform look-and-feel changes. React Native uses JavaScript/TypeScript and renders genuine native components, giving a more platform-authentic feel by default, at the cost of needing to bridge to native code for some capabilities (though the New Architecture has narrowed this gap significantly).

### Q5. What are the main advantages of React Native?
- **Code sharing across iOS and Android** (and, with additional tooling, web/desktop) — often 70–90%+ of business logic and UI code shared.
- **Native performance and UI**, since it renders real native components rather than a webview.
- **Fast iteration** via Fast Refresh, letting developers see code changes reflected almost instantly without a full native rebuild.
- **Large ecosystem** — access to the broader JavaScript/npm ecosystem, plus a mature library of community native modules.
- **Ability to drop into native code** (native modules) when a capability isn't available in JS, without abandoning the framework entirely.

### Q6. What are the main limitations/trade-offs of React Native?
- Genuinely performance-critical or highly custom native UI/graphics work may still require writing native code directly.
- App size tends to be somewhat larger than a purely native equivalent, due to the bundled JS engine/runtime.
- Some platform-specific APIs/capabilities require native modules or third-party libraries, which vary in quality/maintenance.
- Upgrading React Native versions across a large app can be nontrivial (though tooling like Expo/upgrade helpers has improved this significantly).
- Debugging issues that span the JS/native boundary can be more complex than pure-JS or pure-native debugging.

### Q7. What is Expo, at a high level?
A framework and platform built on top of React Native that provides a managed toolchain, a large set of pre-built native modules (camera, location, notifications, etc.) accessible without writing native code, over-the-air update infrastructure, and simplified build/deployment tooling (EAS Build/Submit). See [Section 19](#19-expo-vs-bare-react-native) for more detail.

### Q8. What is Metro?
Metro is React Native's default JavaScript **bundler**, responsible for resolving and transforming all JavaScript/TypeScript modules (and assets like images/fonts) into a single bundle (or multiple bundles) that can be executed by the JS engine on-device, along with providing Fast Refresh/hot reloading during development.

---

## 2. Core Components & JSX

### Q9. What are the fundamental core components in React Native, and what do they correspond to on web?
- **`<View>`** — a generic container, analogous to a `<div>`, supporting layout (Flexbox), styling, and touch handling.
- **`<Text>`** — required to display any text content (unlike web, raw text cannot be placed directly inside a `<View>`); analogous to `<span>`/`<p>`.
- **`<Image>`** — displays images from local assets, the network, or a data URI.
- **`<ScrollView>`** — a scrollable container that renders **all** its children at once, suitable for smaller, bounded content.
- **`<TextInput>`** — captures user text input, analogous to `<input>`.
- **`<TouchableOpacity>` / `<Pressable>`** — wraps content to make it respond to touch/press interactions, analogous to a clickable element.
- **`<FlatList>` / `<SectionList>`** — performant, virtualized list rendering for large or dynamic datasets (see Section 10).

### Q10. Why must all text in React Native be wrapped in a `<Text>` component?
Because React Native maps components directly to native views, and native platforms don't have an equivalent to a raw DOM text node floating freely inside an arbitrary container — text rendering (font, line height, truncation behavior) is handled by a dedicated native text-rendering component, so React Native enforces that any displayed string must be inside a `<Text>` element (attempting to render raw text directly inside a `<View>` throws an error).

### Q11. What is `Pressable`, and how does it differ from `TouchableOpacity`/`TouchableHighlight`?
`Pressable` is the newer, more flexible, and more customizable API for handling touch interactions, supporting fine-grained access to press states (`pressed`, hover, focus) via a function-as-children/style pattern, and more granular control over press timing/hit areas. The older `Touchable*` components (`TouchableOpacity`, `TouchableHighlight`, `TouchableWithoutFeedback`) provide simpler, more opinionated built-in visual feedback (opacity fade, highlight color) but are less flexible; `Pressable` is generally the currently recommended approach for new code.
```jsx
<Pressable
  onPress={onPress}
  style={({ pressed }) => [styles.button, pressed && styles.buttonPressed]}
>
  <Text>Press me</Text>
</Pressable>
```

### Q12. What is the `SafeAreaView` component, and why is it needed?
Renders content within the safe boundaries of a device's screen, automatically avoiding areas obstructed by notches, the status bar, home indicators, or rounded corners (particularly relevant on modern iOS devices) — ensuring UI elements aren't clipped or hidden behind these system UI elements. (Note: the community `react-native-safe-area-context` package's `SafeAreaProvider`/`useSafeAreaInsets` is now generally preferred over the older built-in `SafeAreaView` for more reliable, flexible cross-platform behavior.)

### Q13. What is `KeyboardAvoidingView`, and what problem does it solve?
A component that automatically adjusts its layout (via `padding`, `height`, or `position` behavior) when the on-screen keyboard appears, preventing the keyboard from covering input fields or other important content — a common UX problem on mobile forms that this component addresses declaratively.

### Q14. How do you render conditional content in React Native (JSX)?
Identical to React web — using ternaries, logical `&&`, or early returns within the component function, since JSX syntax and JavaScript conditional logic are unchanged in React Native.
```jsx
{isLoading ? <ActivityIndicator /> : <Text>Loaded!</Text>}
```

### Q15. What is `ActivityIndicator`, and when is it used?
A built-in cross-platform loading spinner component (rendering the platform-appropriate native spinner style), used to indicate an in-progress operation (data loading, a pending network request) to the user.

---

## 3. Styling & Layout

### Q16. How is styling done in React Native, and how does it differ from CSS?
Styles are written as **JavaScript objects** (camelCase property names, e.g., `backgroundColor` instead of `background-color`), typically created via `StyleSheet.create()`, and applied via the `style` prop — there are no CSS files, cascading, class selectors, or CSS-specific units (`px`, `em`); numeric values are unitless density-independent pixels by default.
```jsx
const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#fff', padding: 16 },
  title: { fontSize: 20, fontWeight: 'bold' },
});

<View style={styles.container}><Text style={styles.title}>Hello</Text></View>
```

### Q17. What is `StyleSheet.create()`, and why use it instead of plain style objects?
It's a helper that validates style definitions and can provide minor performance optimizations (historically, it allowed styles to be referenced by an ID and sent across the bridge only once rather than serialized per-render; with the New Architecture this specific optimization matters less, but it remains the conventional, type-checked way to define styles and enables useful tooling/IDE support).

### Q18. Does React Native support CSS cascading or global stylesheets?
No — there's no cascade, no global stylesheet, and no specificity/selector system. Every component must have its styles applied explicitly (though styles can be composed/merged via arrays: `style={[styles.base, styles.override]}`, with later styles in the array taking precedence over earlier ones for matching properties).

### Q19. What layout system does React Native use, and what is the default `flexDirection`?
React Native uses **Flexbox** (via the Yoga layout engine) as its primary/only layout system — there's no CSS Grid. Unlike web CSS (where `flexDirection` defaults to `row`), React Native's default `flexDirection` is **`column`**, reflecting the more common need for vertically stacked mobile UI.

### Q20. What is Yoga, in the context of React Native's layout system?
Yoga is a cross-platform layout engine (written in C, implementing a Flexbox-compatible layout algorithm) that React Native uses to compute the position and size of native views based on the Flexbox style properties specified in JS — it runs natively (not in JavaScript) for performance, translating the declarative Flexbox styles into concrete native view frames.

### Q21. How do `flex`, `flexGrow`, `flexShrink`, and `flexBasis` behave in React Native?
Functionally similar to web Flexbox: `flex: 1` makes a component grow to fill available space along the main axis relative to its siblings; `flexGrow`/`flexShrink`/`flexBasis` provide finer control over growing, shrinking, and initial sizing respectively. One notable behavioral nuance: in React Native, `flex` doesn't support fractional/shorthand syntax exactly like CSS's `flex: 1 1 auto`—generally, a single numeric `flex` value is the most common usage.

### Q22. What units does React Native use for sizing, and how do you handle different screen densities?
Numeric style values (e.g., `width: 100`) are unitless — interpreted as **density-independent pixels (DIP)**, automatically scaled by the platform to render consistently across different physical pixel densities. For responsive sizing relative to the screen, the `Dimensions` API or the `useWindowDimensions()` hook provides the current screen width/height, and percentage strings (`width: '50%'`) are also supported relative to the parent container.

### Q23. How do you handle responsive design across different screen sizes/devices in React Native?
Common approaches: percentage-based widths/heights, the `useWindowDimensions()` hook to read current dimensions and adjust layout/styles reactively (including on orientation change), Flexbox's inherently flexible layout behavior, platform/size-based conditional styling, and libraries like `react-native-responsive-screen` or a custom scaling utility based on a reference design size.

### Q24. What is the difference between `absolute` and `relative` positioning in React Native, and how does it compare to web CSS positioning?
Functionally similar to CSS: `position: 'relative'` (the default) positions an element according to normal Flexbox flow, while `position: 'absolute'` removes it from that flow and positions it relative to its nearest positioned (non-static) ancestor. Unlike web CSS, React Native only supports `relative` and `absolute` — there's no `fixed` or `sticky` positioning value.

### Q25. How do you apply platform-specific styles (e.g., different shadow implementations for iOS vs Android)?
Shadows are a common example: iOS uses `shadowColor`/`shadowOffset`/`shadowOpacity`/`shadowRadius`, while Android uses a single `elevation` property (backed by Material Design's elevation system) — typically handled via `Platform.select()` or `Platform.OS` checks (see [Section 9](#9-platform-specific-code)).
```jsx
...Platform.select({
  ios: { shadowColor: '#000', shadowOffset: { width: 0, height: 2 }, shadowOpacity: 0.25 },
  android: { elevation: 4 },
})
```

---

## 4. Architecture: Bridge, JSI & the New Architecture

### Q26. What was the "old architecture" of React Native, and how did JavaScript communicate with native code?
In the original architecture, JavaScript (running on a JS engine like JavaScriptCore) and the native side (iOS/Android) ran on **separate threads** and communicated exclusively through **the Bridge** — an asynchronous, batched, serialized (JSON) messaging system. Every interaction (rendering a view, calling a native module method, receiving an event) required serializing data into JSON, passing it across the bridge, and deserializing it on the other side.

### Q27. What were the main limitations of the Bridge-based architecture?
- **Asynchronous-only communication** — no way to call native code synchronously from JS, even when synchronous behavior would be more natural/performant.
- **Serialization overhead** — every cross-boundary call required JSON serialization/deserialization, adding latency, especially problematic for high-frequency interactions (e.g., gesture-driven animations, scroll events).
- **Batching delays** — messages were batched and processed at the end of each event loop tick, which could introduce perceptible lag for time-sensitive interactions.

### Q28. What is JSI (JavaScript Interface)?
JSI is a lightweight, general-purpose C++ API that allows JavaScript objects to hold direct references to (and directly invoke methods on) C++ host objects — and vice versa — **without serialization**, and **synchronously** if desired. It's the foundational technology underpinning React Native's New Architecture, replacing the Bridge as the mechanism for JS-native communication.

### Q29. What is the "New Architecture" in React Native, and what are its main components?
A comprehensive re-architecture of React Native's internals (rolled out progressively and now the default for new apps as of React Native 0.76+), replacing the legacy Bridge-based system. Its main components:
- **JSI** — direct, synchronous JS-to-native object references/calls (replacing the async Bridge).
- **Fabric** — the new rendering system, enabling synchronous, more efficient view updates and better integration with React's concurrent features.
- **TurboModules** — the new native module system, allowing native modules to be lazily loaded (only when actually used) and invoked synchronously via JSI, instead of the older `NativeModules` bridge system.
- **Codegen** — automatically generates type-safe native interface code (C++/Java/Objective-C) from TypeScript/Flow type definitions, reducing manual boilerplate and mismatches between JS and native interfaces.

### Q30. What is Fabric, specifically?
Fabric is React Native's new **rendering engine**, replacing the legacy renderer. It allows the "shadow tree" (an intermediate representation of the UI used for layout calculation) to be constructed and manipulated directly in C++, shared efficiently across threads, and updated **synchronously** when needed — better supporting React 18's concurrent rendering features and enabling smoother, more consistent UI updates (particularly for gesture-driven and high-frequency interactions).

### Q31. What are TurboModules, and how do they differ from the legacy Native Modules system?
TurboModules are the New Architecture's replacement for the legacy `NativeModules`/Bridge-based native module system. Key improvements: modules are **lazily instantiated** (only loaded into memory when actually first accessed, improving app startup time versus the old system's eager loading of all registered modules), and methods can be called **synchronously** via JSI when appropriate, avoiding unnecessary async overhead for simple native calls.

### Q32. Why does the New Architecture matter for developers building apps (not just library authors)?
It brings meaningful performance improvements (especially for gesture handling, animations, and list rendering), better alignment with modern React features (concurrent rendering, Suspense), and improved app startup time — even for developers who never write native code directly, since most of the improvements come "for free" through the underlying framework and well-maintained third-party libraries adopting the New Architecture APIs.

---

## 5. Navigation

### Q33. What is React Navigation, and why is a dedicated library needed for navigation in React Native?
Unlike web, mobile apps don't have URLs/browser history natively driving the UI — **React Navigation** is the most widely used community library providing a JavaScript-based navigation and routing solution: stack, tab, and drawer navigators that manage screen transitions, back-button/gesture behavior, and navigation state, mimicking native navigation patterns on each platform.

### Q34. What is a Stack Navigator, and how does it behave differently on iOS vs Android by default?
A Stack Navigator manages a stack of screens, pushing new screens on top and popping them off (typically via a back button/gesture), similar to how native navigation controllers work. By default, it renders **platform-appropriate transition animations and gestures** — e.g., iOS's characteristic slide-from-right-with-swipe-back-gesture, versus Android's differing default transition style — aiming to feel native on each platform without extra configuration.

### Q35. What is a Tab Navigator, and when is it used?
Renders a persistent tab bar (typically at the bottom on mobile) allowing users to switch between top-level sections of an app, each maintaining its own independent state/navigation stack — used for apps with several distinct primary sections (e.g., Home, Search, Profile).

### Q36. What is a Drawer Navigator?
Renders a side-panel (drawer) menu, typically revealed via a swipe gesture or a menu icon, used for navigation menus with many items or secondary navigation options that don't need to be always visible like a tab bar.

### Q37. How do you pass parameters between screens in React Navigation?
```jsx
// Navigating with params
navigation.navigate('Details', { itemId: 42, title: 'First Item' });

// Reading params in the destination screen
function DetailsScreen({ route }) {
  const { itemId, title } = route.params;
  return <Text>{title} - {itemId}</Text>;
}
```

### Q38. What is the difference between `navigation.navigate()` and `navigation.push()`?
`navigate()` goes to a route, but if that route already exists in the stack, it typically jumps back to the existing instance rather than adding a duplicate. `push()` always adds a **new** instance of the route onto the stack, even if one already exists — useful for cases like pushing multiple "detail" screens for different items in sequence.

### Q39. What are navigation hooks, and what do `useNavigation()` and `useRoute()` provide?
`useNavigation()` gives access to the navigation object (for triggering `navigate`, `goBack`, etc.) from any component nested within a navigator, without needing it explicitly passed as a prop — useful for deeply nested components. `useRoute()` similarly provides access to the current route's parameters/name from anywhere within that screen's component tree.

### Q40. How do you implement deep linking in a React Native app?
Configuring a `linking` prop on the top-level `NavigationContainer`, mapping URL path patterns to specific screens/params — allowing the app to open directly to a specific screen when launched via a custom URL scheme or a universal/app link (e.g., tapping a link in an email opens the app directly to a relevant product screen).

### Q41. What is nested navigation, and why might you combine multiple navigator types?
Navigators can be nested inside one another (e.g., a Tab Navigator where one tab itself contains a Stack Navigator) to model complex real-world navigation hierarchies — such as a "Home" tab that can push additional detail screens on top while still showing the tab bar, versus a "Settings" tab with its own separate internal stack.

---

## 6. State Management

### Q42. What state management approaches are available in React Native, and how do they compare to React web?
Essentially the same options as React web, since state management is a React-level (not React-Native-specific) concern: local component state (`useState`/`useReducer`), the Context API for simpler shared/global state, and dedicated libraries (Redux/Redux Toolkit, Zustand, Jotai, MobX) for larger or more complex global state — the underlying mental models transfer directly from web React development.

### Q43. Are there React Native-specific considerations when choosing a state management library?
Bundle size and native-dependency-free implementations matter somewhat more on mobile (affecting app size and startup time), and some libraries offer mobile-specific persistence integrations (e.g., `redux-persist` combined with `AsyncStorage`) for saving/restoring state across app restarts, which is a more central concern on mobile than on typical web apps.

### Q44. How do you persist state across app restarts in React Native?
Commonly via `AsyncStorage` (or a faster alternative like MMKV) combined with a state management library's persistence middleware (`redux-persist` for Redux, or manually loading/saving state in a Zustand/Context setup on app start/state changes) — see [Section 13](#13-storage--persistence) for storage options in more depth.

### Q45. What is React Query (TanStack Query), and is it relevant in React Native?
Yes — like on web, React Query (or SWR) is commonly used in React Native apps to manage **server state** (caching, background refetching, retries, loading/error states for API data), separately from client/UI state — the same library and API work in React Native as in React web, since it doesn't depend on any DOM-specific behavior.

---

## 7. Networking & Data Fetching

### Q46. How do you make network requests in React Native?
The standard `fetch()` API is available globally (polyfilled by React Native), used identically to how it's used on the web:
```js
const response = await fetch('https://api.example.com/users');
const data = await response.json();
```
Axios and other HTTP client libraries also work normally, since they're built on top of `fetch`/`XMLHttpRequest`, both of which React Native provides.

### Q47. Are there any React-Native-specific considerations for networking (compared to web)?
- No CORS restrictions apply (CORS is a browser-enforced security mechanism; a native app isn't subject to it), though the backend API itself may still need appropriate configuration for other clients.
- **App Transport Security (iOS)** and **cleartext traffic restrictions (Android)** by default block plain HTTP requests (non-HTTPS) — requiring explicit configuration exceptions for HTTP endpoints (generally discouraged in production; HTTPS should be used).
- Network state can change (e.g., losing connectivity, switching from WiFi to cellular) more dynamically than typical web usage, often necessitating explicit handling via a library like `@react-native-community/netinfo` to detect connectivity state and adjust UX accordingly (e.g., showing an offline banner, queuing requests).

### Q48. How do you handle offline support/data synchronization in a React Native app?
Common strategies: caching fetched data locally (via a database like SQLite/WatermelonDB, or a caching layer like React Query configured with persistence) so the app remains usable offline; queuing writes/mutations locally while offline and syncing them to the server once connectivity is restored; and using `NetInfo` to detect connectivity changes and drive appropriate UI/sync logic.

---

## 8. Native Modules & Native UI Components

### Q49. What is a Native Module, and when would you need to write one?
A piece of native (Swift/Objective-C on iOS, Kotlin/Java on Android) code exposed to JavaScript, used when a required capability isn't available through React Native's core APIs or an existing library — e.g., accessing a specialized native SDK, low-level hardware features, or platform APIs that don't have JS-accessible equivalents.

### Q50. What is the difference between a Native Module and a Native (UI) Component?
A **Native Module** exposes native **functions/methods** to JavaScript (no visual UI of its own) — e.g., a module for reading device battery level. A **Native (UI) Component** exposes an actual **native view** to be rendered within the React tree (e.g., wrapping a native map SDK's view, or a native video player) — requiring additional work to bridge the view's rendering, props, and events into React's component model.

### Q51. How do TurboModules and Fabric Native Components relate to writing custom native code in the New Architecture?
Under the New Architecture, custom Native Modules are written as **TurboModules** (using Codegen to generate type-safe interface code from a JS/TS spec file), and custom Native UI Components are written as **Fabric Native Components** — both replacing the older `NativeModules`/`requireNativeComponent` APIs, offering better type safety, performance (synchronous calls where appropriate, lazy loading), and more consistent bridging conventions.

### Q52. What is the general workflow for writing a simple custom Native Module?
1. Define a JS/TS **spec file** describing the module's interface (methods, argument/return types) — used by Codegen to generate native scaffolding code.
2. Implement the native side (Swift/Kotlin) conforming to the generated interface, implementing the actual native logic.
3. Register the module with the native platform's module registry.
4. Import and call the module's methods from JavaScript as if it were a regular async (or sync, via JSI) function/object.

### Q53. What is a "native dependency" or "autolinking," and why does it matter?
Many third-party React Native libraries include native code (not just JS) — **autolinking** (introduced in React Native 0.60+) automatically detects and links these native dependencies into the iOS/Android native build configuration during the standard install/build process, eliminating the manual native linking steps that were historically a common source of setup friction and bugs.

---

## 9. Platform-Specific Code

### Q54. How do you write platform-specific logic in React Native?
Using the `Platform` module:
```js
import { Platform } from 'react-native';

const styles = {
  paddingTop: Platform.OS === 'ios' ? 20 : 0,
};

const value = Platform.select({
  ios: 'iOS value',
  android: 'Android value',
  default: 'fallback value',
});
```

### Q55. What is platform-specific file extension resolution (`.ios.js`/`.android.js`)?
Metro (the bundler) automatically resolves files with a `.ios.js`/`.ios.tsx` or `.android.js`/`.android.tsx` extension to the appropriate platform at build/bundle time — allowing an entire component's implementation to differ per platform simply by creating two separate files sharing the same base import name (e.g., `Button.ios.tsx` and `Button.android.tsx`, both imported simply as `./Button`), useful when the differences are substantial enough that inline `Platform.OS` checks would be unwieldy.

### Q56. What are common platform differences a React Native developer needs to account for?
- **Shadows/elevation** — different style properties (Q25).
- **Safe areas/notches** — more prevalent/differently handled on iOS.
- **Back button behavior** — Android's hardware/gesture back button needs explicit handling (`BackHandler` API) in some scenarios, with no direct iOS equivalent.
- **Permissions dialogs and flows** — differ significantly in API and required justification strings (iOS `Info.plist` usage descriptions vs Android manifest permissions and runtime request flows).
- **Push notification setup** — APNs (Apple) vs FCM (Firebase Cloud Messaging/Android), typically abstracted by a library but differing under the hood.
- **Fonts** — must be linked/registered slightly differently per platform.
- **Default navigation transition/gesture conventions** (Q34).

---

## 10. Lists & Performance-Critical Components

### Q57. What is the difference between `ScrollView` and `FlatList`, and when should you use each?
`ScrollView` renders **all** its children immediately, regardless of whether they're currently visible on screen — fine for short, bounded content, but a serious performance/memory problem for long or dynamic lists. `FlatList` is **virtualized** — it only renders items currently visible (plus a configurable buffer/"window"), recycling views as the user scrolls, making it the appropriate choice for any list of significant or unknown length.

### Q58. What are the essential props for `FlatList`?
```jsx
<FlatList
  data={items}
  renderItem={({ item }) => <ItemCard item={item} />}
  keyExtractor={(item) => item.id.toString()}
/>
```
`data` (the array to render), `renderItem` (a function returning the JSX for each item), and `keyExtractor` (returning a stable unique key per item — critical for correct reconciliation and recycling behavior, analogous to the `key` prop in web React lists).

### Q59. What are common `FlatList` performance-tuning props?
- **`initialNumToRender`** — how many items to render on initial mount.
- **`windowSize`** — how many "screens" worth of content to render/keep mounted around the visible area (as a multiplier of the viewport).
- **`maxToRenderPerBatch`** — limits how many items are rendered per render batch during scrolling, balancing responsiveness vs. how quickly new items appear.
- **`removeClippedSubviews`** — (Android-focused, use with care) detaches views that are off-screen from the native view hierarchy to save memory.
- **`getItemLayout`** — if item dimensions are fixed/known in advance, providing this avoids the need for FlatList to dynamically measure each item, significantly improving scroll performance and enabling accurate `scrollToIndex` behavior.

### Q60. Why is `keyExtractor` (or the `key` prop) critical for list performance and correctness?
Just as in web React, stable, unique keys let the underlying reconciliation/recycling system correctly track which rendered view corresponds to which data item across re-renders/scrolling — using an unstable key (like array index for a frequently reordered/filtered list) can cause incorrect item recycling, visual glitches, or lost component state as items shift positions.

### Q61. What is `SectionList`, and how does it differ from `FlatList`?
A virtualized list variant specifically designed for **grouped/sectioned data** (e.g., a contacts list grouped alphabetically by first letter), rendering section headers alongside grouped items while retaining the same virtualization performance benefits as `FlatList`.

### Q62. What are newer, higher-performance list alternatives to `FlatList`, and why might you reach for them?
Libraries like **`FlashList`** (by Shopify) aim to significantly improve list rendering performance over the stock `FlatList`, particularly for lists with variable item heights or very large datasets, using techniques like more efficient cell recycling and reduced blank-space-during-scroll issues — often recommended as a drop-in-ish replacement for `FlatList` in performance-sensitive apps.

### Q63. Why should you avoid defining `renderItem` (or other list-related functions/objects) inline without memoization for large or frequently-updating lists?
An inline arrow function creates a new function reference on every parent re-render, which can defeat `React.memo`-based optimizations on the row component and cause unnecessary re-renders of every visible row. Using `useCallback` for `renderItem` (and `React.memo` on the row component itself) helps avoid this class of unnecessary re-render, especially important given how frequently list-heavy screens tend to re-render during scrolling/data updates.

---

## 11. Animations & Gestures

### Q64. What is the built-in `Animated` API, and how does it work?
React Native's original core animation API, allowing declarative, interpolatable animations driven by `Animated.Value` objects, with a range of composable animation types (`Animated.timing`, `Animated.spring`, `Animated.decay`) and the ability to combine multiple animated values (`Animated.parallel`, `Animated.sequence`, `Animated.stagger`).
```jsx
const fadeAnim = useRef(new Animated.Value(0)).current;

useEffect(() => {
  Animated.timing(fadeAnim, { toValue: 1, duration: 500, useNativeDriver: true }).start();
}, []);

<Animated.View style={{ opacity: fadeAnim }}>...</Animated.View>
```

### Q65. What does `useNativeDriver: true` do, and why does it matter?
Offloads the actual animation execution to the **native side** (running on the UI thread, driven by native animation APIs) rather than recalculating and updating styles from JavaScript on every frame — this avoids frame drops caused by JS thread congestion, resulting in significantly smoother animations, especially under load. Its main limitation (historically, and still somewhat relevant) is that not all style properties are supported by the native driver (notably, layout properties like `width`/`height`/`flex` are generally not supported — only transform and opacity-related properties typically are).

### Q66. What is Reanimated (react-native-reanimated), and why is it often preferred over the built-in `Animated` API?
A community library that runs animations and even gesture-response logic **directly on the native UI thread** using a "worklet" system (small JS-like functions compiled/executed natively), enabling far more complex, performant, and responsive animations — including animations that need to run synchronously with a user's finger movement (gesture-driven interactions) — beyond what the built-in `Animated` API's native driver constraints allow.

### Q67. What is `react-native-gesture-handler`, and why is it commonly paired with Reanimated?
A library providing a more powerful, native-thread-driven gesture recognition system than React Native's built-in touch responder system — offering more reliable, performant handling of complex gestures (pans, pinches, swipes, simultaneous/competing gestures). It's commonly paired with Reanimated so that gesture events can drive animations entirely on the native thread without ever needing to cross back to JavaScript mid-gesture, essential for buttery-smooth gesture-driven UI (e.g., a swipeable card stack, a bottom sheet).

### Q68. What is the general performance concern with running animations driven by frequent JS-thread updates (pre-Reanimated/native-driver approaches)?
If an animation's frame-by-frame updates are computed in JavaScript and then sent across to the native side each frame (rather than being handled entirely natively), any JS thread congestion (from other application logic, large re-renders, etc.) can cause the animation to stutter or drop frames — since the JS thread being busy directly delays the animation updates it's responsible for producing.

---

## 12. Performance Optimization

### Q69. What are the distinct threads involved in a React Native app's execution model, and what runs on each?
- **JavaScript thread** — runs the actual JS application code (React rendering/reconciliation, business logic, event handlers).
- **Native/UI (main) thread** — handles actual native rendering, touch event dispatch, and platform UI framework operations.
- **Shadow/layout thread** (New Architecture) — handles Yoga layout calculations, decoupled to avoid blocking the UI thread on layout work.
- Additional background threads may handle networking or other native module work, depending on the specific module's implementation.

Understanding this separation is central to diagnosing performance issues — a "janky" UI could stem from JS thread congestion (business logic taking too long, blocking re-renders) or from native/UI thread congestion (heavy native rendering work).

### Q70. What causes "JS thread" performance issues, and how do you diagnose them?
Heavy synchronous computation, large re-renders, or excessive bridge/JSI traffic on the JS thread can delay React's ability to process events and produce updates promptly, causing visible lag/dropped frames. Diagnosed via the in-app **Performance Monitor** overlay (showing JS and UI frame rates separately), the React DevTools Profiler, or native profiling tools (Xcode Instruments, Android Studio Profiler) attached to a release-mode build.

### Q71. How do you reduce unnecessary re-renders in a React Native app?
The same techniques as React web apply directly: `React.memo`, `useMemo`/`useCallback` to stabilize references, splitting Context providers to avoid over-broad re-render triggers, and careful state colocation (keeping state as local as possible rather than lifting it further than necessary) — since React Native's reconciliation model is identical to React's.

### Q72. Why should performance always be measured on a **release** build rather than a development build?
Development builds include extensive additional overhead (development warnings, the in-app dev menu, unminified code, various debug-only checks/instrumentation) that significantly skews performance measurements — a component that appears sluggish in development mode may perform perfectly well in a release build, and vice versa; genuine performance profiling should always be done on release/production-configured builds.

### Q73. What is the app startup/"Time to Interactive" concern in React Native, and how do you improve it?
Initial JS bundle parsing/execution, module initialization, and (in the old architecture) eager native module loading all contribute to startup time. Improvements include: enabling **Hermes** (see Q74) for faster startup via precompiled bytecode, lazy-loading TurboModules (New Architecture default behavior), code-splitting/lazy-loading non-critical JS modules/screens, minimizing work done before the first meaningful render, and using RAM bundles/inline requires where applicable.

### Q74. What is Hermes, and how does it improve performance?
Hermes is a JavaScript engine developed by Meta, specifically optimized for React Native's mobile use case (as opposed to using a general-purpose engine like JavaScriptCore or V8). Its key optimization is **ahead-of-time (AOT) bytecode compilation** — JS is compiled to bytecode at build time rather than being parsed and JIT-compiled on-device at app startup — significantly reducing app startup time, memory usage, and app size on most devices. It's the default JS engine for new React Native apps.

### Q75. What are common causes of memory leaks/excessive memory usage in React Native apps?
Unremoved event listeners/subscriptions (native event emitters, navigation listeners), uncleaned timers/intervals, large images not properly downsized/cached, retaining references to unmounted component instances via closures, and excessive/unbounded list data held in memory without virtualization — largely analogous to standard JS memory-leak causes, compounded by mobile devices' comparatively constrained memory budgets versus typical desktop web usage.

---

## 13. Storage & Persistence

### Q76. What is `AsyncStorage`, and what are its characteristics?
An asynchronous, unencrypted, persistent **key-value** storage system (community-maintained as `@react-native-async-storage/async-storage` since it was extracted from React Native core) — simple to use for storing small amounts of non-sensitive data (user preferences, cached tokens for non-critical use, cached API responses) but not intended for large datasets or highly sensitive information.

### Q77. What is MMKV, and why might you choose it over `AsyncStorage`?
A fast, native key-value storage library (originally developed by WeChat, with a popular React Native binding via `react-native-mmkv`) offering significantly better read/write performance than `AsyncStorage` — since it uses memory-mapped files and, notably, can be accessed **synchronously** from JS (via JSI), unlike `AsyncStorage`'s inherently asynchronous API — a meaningful advantage for frequently-accessed, performance-sensitive storage needs.

### Q78. How do you securely store sensitive data (tokens, credentials) in React Native?
Using platform secure storage APIs — the **iOS Keychain** and **Android Keystore** — typically accessed via a library like `react-native-keychain` or `expo-secure-store`, which provide encrypted, OS-level-protected storage specifically designed for credentials/secrets, unlike `AsyncStorage`'s plain, unencrypted storage.

### Q79. What options exist for local relational/structured data storage in React Native?
- **SQLite** (via `react-native-sqlite-storage`, `expo-sqlite`, or similar) — a full relational database, suited for complex querying needs.
- **WatermelonDB** — a reactive database built on top of SQLite, optimized for large datasets and React-friendly reactive queries, commonly used in offline-first apps.
- **Realm** — an alternative embedded object database with its own query API and built-in sync capabilities (via Atlas Device Sync).

### Q80. What factors determine the right storage choice for a given React Native app?
Data volume and structure complexity (simple key-value preferences vs. relational data needing joins/complex queries), performance requirements (synchronous vs. asynchronous access needs), sensitivity of the data (requiring encrypted secure storage vs. general-purpose storage), and offline-sync requirements (whether data needs to be synchronized with a backend, favoring purpose-built offline-first solutions like WatermelonDB or Realm Sync).

---

## 14. Push Notifications & Background Tasks

### Q81. What services underlie push notifications on iOS and Android respectively?
**iOS** uses **APNs** (Apple Push Notification service). **Android** uses **FCM** (Firebase Cloud Messaging). Cross-platform React Native push notification libraries (e.g., `react-native-firebase`'s messaging module, or Expo's push notification service) typically abstract over both, though each still requires platform-specific setup (certificates/keys for APNs, a Firebase project for FCM).

### Q82. What is the general flow for implementing push notifications in a React Native app?
1. Request notification permissions from the user (required explicitly on both platforms, especially iOS).
2. Obtain a device push token (unique per device/app install) from APNs/FCM via the chosen library.
3. Send that token to your backend server, associating it with the relevant user/account.
4. The backend sends push payloads to APNs/FCM (or a unified service, like Expo's push service, which can relay to both) targeting specific device tokens.
5. Handle incoming notifications in the app — both when the app is in the foreground (typically requiring custom in-app handling/display) and when tapped from the background/killed state (typically triggering navigation to a relevant screen).

### Q83. What are background tasks in React Native, and what constraints do mobile OSes impose on them?
Background tasks allow limited code execution while an app isn't in the foreground (e.g., periodic data sync, location tracking, fetching new content) — implemented via libraries like `react-native-background-fetch` or Expo's `TaskManager`/`BackgroundFetch` APIs. Both iOS and Android impose **significant restrictions** on background execution (time limits, battery-optimization-driven throttling/deferral, requiring specific background modes/permissions to be declared) — background execution should never be assumed to run reliably/immediately, and OS-level constraints must be designed around rather than fought.

---

## 15. Permissions & Device APIs

### Q84. How does permission handling differ between iOS and Android at a conceptual level?
**iOS** requires a usage-description string declared in `Info.plist` for each sensitive permission category (camera, location, etc.) — the system automatically shows this description in its permission prompt, and the app is rejected from the App Store if a permission is used without a corresponding description. **Android** requires permissions declared in the `AndroidManifest.xml`, with "dangerous" permissions (camera, location, contacts, etc.) additionally requiring an explicit **runtime request** (since Android 6.0), separate from the manifest declaration.

### Q85. What library is commonly used to request and check permissions across both platforms in a unified way?
`react-native-permissions` (for bare React Native projects) or Expo's individual permission APIs (e.g., `expo-camera`'s `requestCameraPermissionsAsync()`) provide a cross-platform abstraction over the underlying platform-specific permission request/check flows.

### Q86. What are common device APIs accessed in React Native apps, and what libraries typically provide them?
- **Camera** — `react-native-vision-camera`, `expo-camera`.
- **Location** — `react-native-geolocation`, `expo-location`.
- **Contacts** — `react-native-contacts`, `expo-contacts`.
- **Biometrics (Face ID/Touch ID/fingerprint)** — `react-native-biometrics`, `expo-local-authentication`.
- **File system access** — `react-native-fs`, `expo-file-system`.
- **Sensors (accelerometer, gyroscope)** — `react-native-sensors`, `expo-sensors`.

### Q87. What is good UX practice around requesting permissions in a mobile app?
Request permissions **contextually**, at the point where the feature actually needing them is used (rather than requesting everything upfront at app launch), and ideally explain **why** the permission is needed before triggering the native OS prompt (a "pre-permission" explanation screen) — since users are far more likely to grant a permission when they understand its purpose in context, and denied permissions (especially on iOS) can be cumbersome for users to re-grant later.

---

## 16. Debugging & DevTools

### Q88. What debugging tools are available for React Native development?
- **React Native DevTools** (the modern, Chrome-DevTools-based debugger integrated with the New Architecture/Hermes) — supports breakpoints, console access, network inspection, and React DevTools' component tree/profiler integration.
- **Flipper** — a (historically popular, though its role has diminished with React Native DevTools' improvements) extensible desktop debugging platform supporting plugins for network inspection, layout inspection, and native/JS logs together.
- **Native platform tools** — Xcode (for iOS-specific native debugging, crash logs, Instruments profiling) and Android Studio (Logcat, native profiler) for issues that require native-level visibility.
- **`console.log`/the in-app dev menu** — basic logging, still commonly used for quick iteration.

### Q89. What is the in-app Developer Menu, and how do you access it?
A menu (shaken open on a physical device, or via a keyboard shortcut in a simulator/emulator — e.g., Cmd+D on iOS Simulator, Cmd/Ctrl+M on Android emulator) providing quick access to development tools: reload, toggle Fast Refresh, open the JS debugger, toggle the Performance Monitor overlay, and toggle inspecting elements.

### Q90. What is Fast Refresh, and how does it differ from a full reload?
Fast Refresh preserves component **state** while applying edited code changes almost instantly, by re-executing only the modules that changed (and their dependents) rather than reloading the entire JS bundle from scratch — dramatically speeding up the development iteration loop compared to a full reload, which restarts the entire app/JS context (losing all in-memory state).

### Q91. How do you debug native-side crashes that don't originate from JavaScript?
Native crashes require native-level debugging tools: **Xcode's Instruments/crash logs** and symbolicated crash reports for iOS, **Android Studio's Logcat** and native crash traces for Android, and crash reporting services (Sentry, Firebase Crashlytics, Bugsnag) configured with native symbol upload for meaningful production crash diagnostics — since a purely JS-focused debugger won't surface native-layer failures.

### Q92. What is the "red screen of death" / "yellow box" warnings in React Native?
The **red screen** is React Native's full-screen fatal error display shown for unhandled JavaScript errors during development, showing the error message and a stack trace. **Yellow box warnings** (an older term, now typically rendered as an in-app warning banner/log) surface non-fatal warnings (deprecated API usage, performance concerns, `console.warn` calls) without interrupting the app — both are development-mode-only conveniences, not shown in production release builds.

---

## 17. Testing

### Q93. What testing tools are commonly used for React Native applications?
- **Jest** — the default test runner (bundled with React Native's default project setup), used for unit and component/snapshot testing.
- **React Native Testing Library** — analogous to React Testing Library for web, encouraging tests that interact with rendered output (by text, role, accessibility labels) rather than internal implementation details.
- **Detox** — a gray-box end-to-end testing framework specifically designed for React Native, running tests against a real (simulator/emulator or device) build of the app, synchronized with the app's native/JS execution to avoid flaky timing-based waits.
- **Maestro** — a newer, YAML-based, simpler-to-configure E2E testing tool gaining popularity as an alternative to Detox for mobile E2E testing.

### Q94. How does testing a React Native component with React Native Testing Library compare to testing a React web component?
Conceptually identical — render the component, query for elements the way a user would (by text or accessibility role/label, since there's no DOM/CSS selectors to rely on), and simulate interactions (`fireEvent.press`), asserting on the resulting rendered output — the same "test behavior, not implementation" philosophy applies directly.
```jsx
import { render, fireEvent, screen } from '@testing-library/react-native';

test('increments count on press', () => {
  render(<Counter />);
  fireEvent.press(screen.getByText('Increment'));
  expect(screen.getByText('Count: 1')).toBeTruthy();
});
```

### Q95. What is snapshot testing, and what are its trade-offs in a React Native context?
Snapshot testing captures a serialized representation of a rendered component's output and compares it against a previously saved "snapshot" on subsequent test runs, flagging unexpected changes. It's easy to set up but can become a low-value, easily-ignored rubber-stamp practice if developers habitually update snapshots without carefully reviewing the actual diff — most useful for catching truly unintentional structural changes rather than as a primary correctness-verification strategy.

### Q96. What is the difference between unit/component tests and E2E tests (Detox/Maestro) for React Native, and when do you need each?
Unit/component tests (Jest + RNTL) run fast, in a JS-only simulated environment, verifying individual component/logic correctness in isolation. E2E tests run against a **real compiled app** on a simulator/emulator/device, verifying entire user flows work correctly across the full JS+native stack (navigation, permissions, native module integration) — slower and more complex to set up/maintain, but catching a class of integration issues unit tests fundamentally cannot (e.g., native build/linking problems, real navigation transitions, actual device API behavior).

---

## 18. Build, Deployment & Over-the-Air Updates

### Q97. What is required to build and submit a React Native app to the Apple App Store?
A macOS machine with Xcode (or a cloud build service like EAS Build for teams without direct Mac access), an active Apple Developer Program account, proper code signing (certificates/provisioning profiles), a compiled/archived release build, and submission through App Store Connect (including required metadata, screenshots, and passing Apple's app review process).

### Q98. What is required to build and submit a React Native app to the Google Play Store?
A signed release build (an Android App Bundle, `.aab`, is Google Play's required/preferred format over a raw `.apk`), a Google Play Developer account, appropriate app signing configuration (Google Play App Signing is generally recommended, where Google manages the final signing key), and submission through the Google Play Console (including store listing details and passing Play's review process).

### Q99. What is EAS (Expo Application Services), and what does EAS Build/Submit provide?
Expo's cloud-based build and submission service, allowing developers to build native iOS/Android binaries **without needing local Xcode/Android Studio installations** (builds run on Expo's cloud infrastructure), and to submit those builds directly to the App Store/Play Store via CLI commands — significantly simplifying CI/CD and cross-platform build workflows, usable even for apps not otherwise using the rest of the Expo managed workflow's runtime APIs.

### Q100. What are Over-the-Air (OTA) updates, and what problem do they solve?
OTA updates allow the **JavaScript bundle and assets** of a React Native app to be updated and delivered directly to already-installed apps, **without** requiring a new binary submission/approval through the App Store or Play Store — enabling rapid bug fixes and feature updates for JS-only changes. Popular solutions: **Expo Updates** (integrated with the Expo/EAS ecosystem) and **CodePush** (originally Microsoft's offering, now community-maintained in various forms).

### Q101. What are the limitations of OTA updates?
OTA updates can only modify **JavaScript and asset** content — any change requiring new/modified **native code** (a new native module, a native dependency upgrade, changes to native permissions/configuration) still requires a full binary rebuild and standard app store submission/review. Additionally, both Apple and Google have policies restricting what OTA updates may be used for (they generally cannot be used to circumvent app review by delivering entirely different app functionality/content than what was reviewed), so teams must stay within these platform policy boundaries.

### Q102. What is CI/CD for React Native, and what does a typical pipeline include?
Automated pipelines (using tools like GitHub Actions, Bitrise, CircleCI, or Expo's EAS Build/Update) that handle: running tests/linting on each commit/PR, building native binaries for both platforms, running automated E2E tests against built binaries, and automating submission to app stores and/or pushing OTA updates — reducing manual release overhead and improving release consistency/reliability.

### Q103. What is code signing, and why is it particularly involved on iOS?
Code signing cryptographically verifies that an app binary comes from a known, registered developer/organization and hasn't been tampered with — required by both platforms, but iOS's system (involving certificates, provisioning profiles, and specific device/App ID/entitlement matching rules managed through the Apple Developer portal) is historically considered more complex to manage than Android's comparatively simpler keystore-based signing, which is a common source of build/deployment friction that services like EAS Build aim to abstract away.

---

## 19. Expo vs Bare React Native

### Q104. What is the difference between the Expo "managed workflow" and a "bare" React Native project?
The **managed workflow** (the default/typical way of using Expo today) provides a curated set of native modules/APIs accessible purely through JavaScript configuration (no native code editing required for most common needs), cloud-based builds via EAS, and simplified project setup/upgrades. A **bare** React Native project gives full, direct access to the native iOS/Android project files, allowing arbitrary native code/libraries to be added freely, at the cost of managing native tooling/build configuration more directly yourself.

### Q105. Can you still write custom native code in a modern Expo-managed project?
Yes — modern Expo supports **custom native code via Config Plugins** and the **Development Build** system (formerly "bare workflow" was required for this, but Expo's tooling has evolved to bridge this gap significantly): you can add native modules/dependencies not covered by Expo's SDK, generate a custom development client build including them, and continue developing with most of Expo's tooling/DX intact — substantially narrowing what used to be a hard managed-vs-bare divide.

### Q106. What is `expo prebuild` (formerly "ejecting")?
A command that generates the native `ios`/`android` project directories from an Expo-managed project's configuration (`app.json`/`app.config.js` and any config plugins), allowing direct native code edits if truly needed — modern Expo treats this as a normal, reversible step in the workflow (native directories can be regenerated from config) rather than the old one-way "eject" process that used to permanently commit a project to unmanaged native tooling.

### Q107. What are Expo Config Plugins?
JavaScript functions that programmatically modify the generated native project configuration (Info.plist entries, Android manifest permissions, native build settings) during the `prebuild`/build process — allowing third-party native dependencies (or custom native requirements) to be integrated into a managed Expo project's build without manually hand-editing native project files, since those files are regenerated from config rather than persistently maintained by hand.

### Q108. When would you choose bare React Native (without Expo) over using Expo?
Reasons to go bare are much narrower today than they used to be, given Expo's Development Build/Config Plugin system, but might include: needing extremely fine-grained, ongoing manual control over native project files/build configuration that doesn't fit well into the config-plugin model, working with a very specific/unusual native SDK integration not well-supported by existing plugins, or organizational/tooling constraints that favor a fully self-managed native build pipeline (e.g., specific enterprise CI infrastructure requirements).

### Q109. What is Expo Go, and what are its limitations?
Expo Go is a pre-built, generic app (downloadable from the App Store/Play Store) that can load and run an Expo project's JS bundle directly during development **without needing any native build step at all** — extremely convenient for the fastest possible iteration during early development. Its key limitation: it only includes the native modules bundled with Expo Go itself, so it **cannot** run projects with custom native code/dependencies outside Expo's SDK — such projects require a custom **Development Build** instead.

---

## 20. TypeScript with React Native

### Q110. How do you set up TypeScript in a React Native project?
Modern React Native project templates (via the CLI or `create-expo-app`) support TypeScript out of the box, using `.tsx` files and a provided `tsconfig.json` — no fundamentally different setup process from configuring TypeScript in any other React project, aside from React Native-specific type definitions (`@types/react-native` is largely unnecessary now since React Native ships its own TS types directly).

### Q111. How do you type navigation (e.g., React Navigation) in a TypeScript React Native app?
By defining a type mapping route names to their expected params, then using it to strongly type navigation prop/hook usage throughout the app:
```ts
type RootStackParamList = {
  Home: undefined;
  Details: { itemId: number; title: string };
};

type DetailsScreenProps = NativeStackScreenProps<RootStackParamList, 'Details'>;

function DetailsScreen({ route, navigation }: DetailsScreenProps) {
  const { itemId, title } = route.params; // fully typed
}
```

### Q112. How do you type a custom Native Module in TypeScript for use with Codegen/TurboModules?
By writing a **spec file** (a `.ts` file following TurboModules' required conventions — extending `TurboModule` and exporting a `getEnforcing<Spec>('ModuleName')` call) that defines the module's method signatures using TypeScript types, which Codegen then uses to generate the corresponding native interface code — ensuring the JS and native sides stay in sync and type-checked, rather than relying on manually-maintained, easily-mismatched interface definitions as in the legacy Native Modules system.

---

## 21. Miscellaneous & Best Practices

### Q113. What is the difference between "development mode" and "release mode" builds, beyond just performance?
Development builds include hot reloading/Fast Refresh, verbose error overlays (red screen), development-only warnings, and typically connect to Metro's dev server for the JS bundle (rather than bundling it directly into the app) — none of which are present (or desirable) in a release build, which bundles a minified, optimized JS bundle directly into the app package and strips development-only tooling/overhead entirely.

### Q114. What is "prop drilling," and does it manifest differently in React Native vs web?
Conceptually identical to React web — passing props through multiple layers of components that don't need them, just to reach a deeply nested consumer — addressed the same way (Context API, state management libraries, composition), since this is a React-level architectural concern, not something React Native's native-rendering layer changes.

### Q115. What are common React Native performance/architecture anti-patterns?
- Using `ScrollView` for long or dynamically-sized lists instead of `FlatList`/`FlashList`.
- Inline function/object props on frequently re-rendering components (especially list rows) without memoization.
- Not using `useNativeDriver`/Reanimated for animations that could otherwise stutter under JS thread load.
- Loading full-resolution images without resizing/caching, bloating memory usage.
- Requesting all permissions upfront at launch rather than contextually.
- Storing sensitive data in `AsyncStorage` instead of secure storage (Keychain/Keystore).
- Profiling/optimizing based on development-mode builds instead of release builds.
- Blocking the JS thread with heavy synchronous computation instead of offloading it (e.g., to a native module, a Web Worker equivalent via `react-native-worklets-core`, or breaking work into smaller async chunks).

### Q116. How do you optimize image loading and rendering in React Native?
Use appropriately sized/resized images (avoiding loading a huge source image just to display it small), leverage caching (many image libraries like `react-native-fast-image` — or the New Architecture's improved built-in `Image` component — provide better memory/disk caching than the bare-bones default), lazy-load images that are off-screen (naturally handled by `FlatList`/`FlashList` virtualization), and use appropriate image formats (WebP where supported) to reduce download/decode overhead.

### Q117. What is the general debugging/mental model shift needed when moving from web React development to React Native development?
Beyond the different core components/styling system, developers need to internalize: there's no DOM/browser devtools in the traditional sense (different debugging tools apply); performance issues can originate from either the JS thread or the native/UI thread (a distinction that doesn't exist on web); platform differences (iOS vs Android) must be actively considered rather than assumed away; and native build/dependency management (Xcode, Gradle, CocoaPods, autolinking) becomes a real, occasionally necessary part of the development workflow, unlike typical web development's browser-only deployment target.

### Q118. What are general React Native best practices for production applications?
- Adopt the New Architecture (Fabric/TurboModules/Hermes) for new projects — the default and generally the actively supported path going forward.
- Use `FlatList`/`FlashList` (never `ScrollView`) for any non-trivial list.
- Use Reanimated + Gesture Handler for any gesture-driven or complex animation work.
- Secure sensitive data via Keychain/Keystore-backed storage, never plain `AsyncStorage`.
- Request permissions contextually with clear justification, respecting each platform's conventions.
- Profile and optimize using release-mode builds only.
- Set up crash reporting (Sentry/Crashlytics) with proper native symbol upload before shipping.
- Establish a CI/CD pipeline covering tests, builds, and (where applicable) OTA update delivery.
- Keep React Native, Expo SDK, and native dependency versions reasonably current to avoid large, painful future upgrade jumps.
- Test on **real devices** (not just simulators/emulators) before release, since certain performance, permission, and hardware-API behaviors can differ meaningfully from simulated environments.

---

*This guide covers the core React Native interview surface area. For role-specific deep dives (e.g., writing production TurboModules/Fabric components from scratch, advanced Reanimated worklet patterns, or deep New Architecture migration specifics for large legacy codebases), further topic-specific preparation is recommended.*
