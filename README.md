<div align="center">

<img src="https://ucarecdn.com/ab502fed-46f6-4db0-866a-42d82b5d296d/UntitledDesign3.png" width="250" alt="remix-pwa"/>

# Remix PWA

**PWA integration & support for Remix**

[![stars](https://img.shields.io/github/stars/ShafSpecs/remix-pwa)](https://github.com/ShafSpecs/remix-pwa/stargazers)
[![issues](https://img.shields.io/github/issues/ShafSpecs/remix-pwa)](https://github.com/ShafSpecs/remix-pwa/issues)
[![License](https://img.shields.io/github/license/ShafSpecs/remix-pwa)](https://github.com/ShafSpecs/remix-pwa/blob/main/LICENSE.md)

</div>

Remix PWA is a lightweight, standalone npm package that adds full Progressive Web App support to Remix 💿.

## Features

- Integrates Progressive Web App (PWA) features into Remix including offline support, caching, installability on Native devices and more.
- Automatic caching for `build` files and static files.
- Implements a network-first strategy for loader & action calls (i.e Gets a fresh request each time when online, and proceeds to an older fallback when offline)
- Safely handles uncached loader calls without affecting other sections of the site (i.e Throws user to nearest Error boundary without disrupting Service Workers)
- PWA client-side utilities that comes bundled with your App to give you more options and freedom while building the PWA of tomorrow.
- Server-side utilities to simplify a lot of PWA backend for you. Developing would be more exciting!

**v1.1 is finally here 🎉🎉!! Check out the [release](https://github.com/ShafSpecs/remix-pwa/releases/tag/v1.1) changelog for a summary of the journey so far.** 

## Table Of Content

- [Getting Started](#getting-started)
  - [Why Use remix-pwa?](#why-use-remix-pwa)
  - [Installation](#installation)
  - [Upgrading Guide](#upgrade-guide)
- [Setting Up your PWA](#setting-up-remix-for-pwa)
- [Deployment](#deployment)
- [API Documentation](#api-documentation)
  - [Client APIs](#client-apis)
    - [Type Annonations and Return Object](#type-annonations)
    - [Check Connection Status of User](#check-connectivity)
    - [Clipboard API](#copy-text-to-clipboard)
    - [WakeLock API](#wakelock-api)
    - [App Notification Badges](#app-badges)
    - [FullScreen Toggle Utility](#fullscreen-toggle)
    - [Notification API](#client-notification-api)
    - [Document Visibility](#visibility-state)
    - [Copy Images to Clipboard](#copy-images-to-clipboard)
    - [Web Share API](#web-share-api)
      - [File Web Share API](#file-web-share-api)
      - [Link Web Share API](#link-web-share-api)
      - [Miscellaneous Web Share API](#misc-web-share-api)
  - [Server APIs](#server-api)
    - [Push Notification API](#push-notification-api)
- [Installation Manual Guide](#installation-manual-guide)
  - [Language](#language)
  - [Caching Strategy](#caching-strategy)
  - [PWA Features](#pwa-features)
  - [`app` Location](#app-location)
  - [Installing Dependencies](#installing-dependencies)
- [Going Deeper](#going-deeper)
  - [Customizing your PWA `manifest` file](#customizing-your-pwa-manifest)
  - [Upgrading your PWA manifest](#upgrading-your-pwa-manifest)
- [Remix PWA Roadmap](#roadmap)
- [Contributing Doc](#contributing)
- [Help Support](#support)
- [Authors](#authors)
- [License](#license)  

## Getting Started

### Why Use `remix-pwa`

remix-pwa is a quick and easy way to get standardized APIs to interact with service worker in your Remix application. 

remix-pwa will:

- Generate a route to store your applications manifest data
- Generate a route to handle service worker subscription
- Generate standardized messaging protocol between service workers and your application.

### Installation

To integrate PWA features into your Remix App with `remix-pwa`, run the following command:

```sh
npx remix-pwa@latest
```

During installation, you would be required to answer a few questions:

- The language you are using for the Remix project (TypeScript or JavaScript)
- The caching strategy you are using. (`Precaching` or `Just-In-Time Caching`)
- What services of Remix PWA you need. (*It is finally here* 🥳)
- The location of your Remix `app` directory.
- Do you want to install Remix PWA dependencies? (Default: `yes`)

> *The yarn issue has been fixed*

> *`remix-pwa` causes errors if comments are present in your root file, make sure to remove them before installing!*

Refer to [this](#installation-manual-guide) section for a detailed explanation of the above questions.

> **`remix-pwa` now includes command flags!** *Run*
> ```sh
> npx remix-pwa@latest -h
> ```
> *to view the commands* 

### Upgrade Guide

To upgrade to a newer version of `remix-pwa`, simply run this command
```sh
# Use npx to integrate PWA features without modifying your dependencies
npx remix-pwa@latest
```
and you can continue with your PWA

## Setting up Remix for PWA

After installing `remix-pwa`, link the `manifest` file in order to get installability feature of PWA as well as app characteristics and other features. To do that, simply add the following block of code to the head in your `root` file above the `<Links />` tag:

```jsx
<link rel="manifest" href="/resources/manifest.webmanifest" />
```

To run your app, simply run the command:

```sh
npm run dev
```

And voila! You are now ready to use your PWA! 

If you want to lay your hands on demo icons and favicons for your PWA, `remix-pwa` got you covered with sample icons. Simply delete the `favicon.ico`
in your `public` folder and add the [following links](https://github.com/ShafSpecs/remix-pwa/blob/main/examples/pwa-links.ts#L9) to your `root` file, above the `<Links />` tag.

## Deployment

To build and deploy your Remix PWA App, simply run the command:

```sh
npm run build
```

at build time and then, you can host it on any hosting providers you prefer.

# API Documentation

## Client APIs

> Client APIs are a set of asynchronous functions that are to be run on the client **only** and never on the server. 

They can be triggered by DOM events (click, hover, keypress, etc.) like other functions, but in order to trigger window events that happen at the start of a page lifecycle, e.g page "load" event, it is **highly recommended** to use these functions in a React's `useEffect` hook.

#### <u>Type annotations</u>:

Almost all Client APIs return a promise object (type `ResponseObject`) that consists of two properties: `status` and `message`. The `status` key is a string that would either be "success" or "bad". `remix-pwa` is set up by default, with error-catching procedures for these APIs. You can still set up your custom responses (to display a particular UI for example, if the particular API isn't supported in the user's browser) in case of an error or a successful request with the `status` response. The `message` key is a default message string that accompanies the status in case of a pass or fail.

```ts
interface ResponseObject {
  status: "success" | "bad",
  message: string;
}
```

### Check Connectivity
#### `checkConnectivity(online: () => void, offline: () => void): Promise<ResponseObject>`

This function is used to check whether a user is online or offline and execute a function accordingly. It could be used to update a state,
display a particular UI or send a particular response to the server.

```ts
import { checkConnectivity } from "~/utils/client/pwa-utils.client";

const online = () => {
  //..Do something for online state
}

const offline = () => {
  //..Do something for offline state
}

useEffect(() => {
  // The `console.log` method returns an object with a status of "success" if online and a pass message or a status of "bad" and a fail message if offline
  checkConnectivity(online, offline).then(data => console.log(data)) 
}, [])
```

### Copy text to Clipboard
#### `copyText(text: string) => Promise<ResponseObject>`

The Clipboard API is a method used to access the clipboard of a device, native or web, and write to it. This function can be triggered by DOM events, i.e "click", "hover", etc. or window events i.e "load", "scroll", etc. 

```tsx
import { copyText } from "~/utils/client/pwa-utils.client";

<button onClick={() => copyText("Test String")}>Copy to Clipboard</button>
```

### WakeLock API
#### `WakeLock() => Promise<ResponseObject>`

> **🚨 This is still an experimental feature! Some browsers like FireFox would not work with this feature! 🚨**

The WakeLock API is a function that when fired, is used to keep the screen of a device on at all times even when idle. It is usually fired when an app is started or when a particular route that needs screen-time is loaded (e.g A video app that has a `watch-video` route)

```tsx
import { WakeLock } from "~/utils/client/pwa-utils.client";

useEffect(() => {
  WakeLock() // triggers the Wakelock api
}, [])
```

### App Badges
#### `addBadge(numberCount: number) => Promise<ResponseObject>`

#### `removeBadge() => Promise<ResponseObject>`

The badge API is used by installed web apps to set an application-wide badge, shown in an "operating-system-specific" place associated with the application (such as the shelf or home screen or taskbar).

The badge API makes it easy to silently notify the user that there is new activity that might require their attention, or to indicate a small amount of information, such as an unread count (e.g unread messages).

The `addBadge` function takes a number as an argument (displays the number of notification) while the `removeBadge` function doesn't take any argument.

```tsx
import { addBadge, removeBadge } from "~/utils/client/pwa-utils.client";

// used to clear away all notification badges
removeBadge()

//used to add a badge to the installed App
addBadge(3); // sets a new notification badge with 3 indicated notifications 
```

### FullScreen Toggle
#### `EnableFullScreenMode() => Promise<ResponseObject>`

#### `ExitFullScreenMode() => Promise<ResponseObject>`

The Full Screen feature is an additional utility you can integrate into your app while building your PWA, `EnableFullScreenMode()` enables an App to cover the entire breadth and width of the screen at the tap of a button and the `ExitFullScreenMode()` exits full-screen mode. They both don't take any arguments and can be invoked like any other normal function.

```tsx
import { EnableFullScreenMode, ExitFullScreenMode } from "~/utils/client/pwa-utils.client";

// Enable full-screen at the click of a button
<button onClick={EnableFullScreenMode}>Enable Full-Screen mode</button>

//Exit full-screen mode
<button onClick={ExitFullScreenMode}>Exit Full-Screen Mode</button>
```

### (Client) Notification API
#### `SendNotification(title: string, option: NotificationOptions) => Promise<ResponseObject>`

```ts
// Interface `NotificationOptions`
interface NotificationOptions {
  body: string | "Notification body";
  badge?: string;
  icon?: string;
  silent: boolean | false;
  image?: string
}
```

The `SendNotification` API is a client-only function driven only by the [Notifications API](https://developer.mozilla.org/en-US/docs/Web/API/Notification), it is different from the Push API which is another API handled and executed by the server (arriving to `remix-pwa` soon). The `SendNotification` function is executed by the client and takes in two arguments, one is the title of the notification and that's the top header (Title) of the notification your user would see. The second option is an object that would contain additional options for the API.

The first key for the `NotificationOptions` argument is the `body` and that is a required argument. The body is the main content of your notification that would contain the details of what you want to pass to the user. The `badge` argument is an optional argument and it's the image URL string of the Notification badge, and it's what the user would see when there is no space for the Notification content to show. It is recommended to use a 96px by 96px square image for the badge. The next argument is the `icon` argument which is the image that would be displayed alongside your Notification. The `image` parameter is a string argument (*url of your image*) and is used to display an image along with your notification. The final argument is the silent parameter and it's a boolean argument (**true** or **false**) that is <u>required</u>, it is used to determine whether a notification should be sent silently regardless of the device's settings, it is by default set to false.

The Notification API can take values from the server (e.g `loader`) or from the client but it must be called and executed on the client side. We are working on adding the Push API that allows you to execute a Notification API together with the Push API on the server side in response to anything (for example, when a message is sent to a user in a messaging App).

> This API is fully stable and is setup completely for your use cases including Notification permissions, however, we are working on adding more API options so that you can have the maximum custom experience!

```tsx
import { SendNotification } from "~/utils/client/pwa-utils.client";

const options = {
  body: "Hello, take a break and drink some water! 💧", // required!
  badge: "/icons/notification-badge.png", // not required
  icon: "/icons/app-icon.png", // not required
  silent: false, // required
  image: "/images/Nature.png" // NEW! Not required
}

let minutes = 30

// executed in several ways
setTimeout(() => {
  SendNotification("Workout App", options)
}, minutes * 60 * 1000)

// another method of execution
<button onClick={() => SendNotification("Exercise Tracker App", options)}>Take a break!</button>
```

### Visibility State
#### `Visibility (isVisible: () => void, notVisible: () => void): Promise<ResponseObject>`

This utility is used to get whether a document is visible or is minimized (or in the background, etc). It takes two functions as it's parameter, one for a visible state, and the other for an invisible state. A common use case for this is to stop a process (e.g video playing, downloading process, etc) when the app is minimized or to run a process *only* when the App is minimized.

```tsx
import { Visibility } from "~/utils/client/pwa-utils.client";

const documentVisible = () => {
  //..do something
}

const documentInvisible = () => {
  //..do something else
}

const state = document.visibilityState

// Monitor visibility and keep firing the function on visibility change
useEffect(() => {
  Visibiliy(documentVisible, documentInvisible)
}, [state])
```

### Copy Images to Clipboard
#### `copyImage(url: string) => Promise<ResponseObject>`

This is a modified version of the Copy Text to Clipboard API. It is used to easily copy images to your device's clipboard, it starts by taking the url of the image (e.g Passed through the `src` of an image tag) then fetching the image behind-scenes and finally copying the `blob` to your device's clipboard.

```tsx
import { copyImage } from "~/utils/client/pwa-utils.client";

// Getting an image frame via React's ref hook.
const frameRef = useRef<HTMLImageElement>(null!)

// Accessing the `src` attribute and copying it to the clipboard.
<button onClick={() => copyImage(frameRef.current.src)}>📋 Copy Image to Clipboard</button>
```

### Web Share API

### File Web Share API
#### `WebShareFile(title: string, data: any[], text: string) => Promise<ResponseObject>`

The File Web Share API is an asynchronous method used to share files from your PWA to other apps. It takes in 3 arguments, `title` seen as the title of your shared response, `text` which is the body of your shared message (*You can set a default value in `app/utils/client/pwa-utils.client.ts`*) and finally the `data` which an array that contains the files to share. It returns a `ResponseObject` promise.

```tsx
import { WebShareFile } from "~/utils/client/pwa-utils.client";

const files = ["File1.txt", "File2.svg"]

const SendFile = async () => {
  await WebShareFile("My Remix PWA", files, "A text file and an SVG file")
  
  // Do something or Trigger something else
}
```

### Link Web Share API
#### ` WebShareLink(url: string, title: string, text: string) => Promise<ResponseObject>`

This is a modified Web Share method used to serve links from your file (e.g A blog app) to other supported apps on your device. It is similar to the `File Web Share API` except it accepts a url instead of files.

```tsx
import { WebShareLink } from "~/utils/client/pwa-utils.client";

const data = useLoaderData()

<button onClick={() => WebShareLink(data.param, data.title, data.description)}>Share Post</button>
```

### Misc. Web Share API
#### ` WebShare(data: any): Promise<ResponseObject>`

The Miscellaneous Web Share Api is intended to be a simple Web Share Api that should be used if you intend to send something a bit more loose and simple compared to files & URLs. 

> *I would advise against using this API except when the other two don't meet your data criteria. As this is loosely typed and ~~could~~ cause errors when used with heavy, technical data.*

```tsx
import { WebShare } from "~/utils/client/pwa-utils.client";

<button onClick={() => WebShare("Hello, I am a volatile API!")}>Share loose data</button>
```

[(Back to Top)](#table-of-content)

## Server API

### Push Notification API
#### `PushNotification(content: PushObject, delay: number = 0) => Promise<void>`

```ts
interface PushObject {
  title: string;
  body: string;
  icon?: string;
  badge?: string;
  dir?: string;
  image?: string;
  silent?: boolean;
}
```

> **This is a server API. All Server APIs are to be run in the server (i.e Loaders, Actions & `.server` files)** 

Push Notification API is finally here (*with some updates*)! Push Notification API is used to by the server to generate Notifications for the user. It is used when you want to push a notification that is triggered by events from outside the App (e.g A message is sent to your user from another user's app, in a messaging app). It takes in quite a lot of arguments, a content argument which is an object containing methods to style your notification. The *title* property is the title of your Notification, the *body* is for the main text of the notification and the delay is an optional argument used to set the delay (*in seconds*) between the event happening and the server triggering the notification. It has a default value of zero.

> *To reference the other properties of `PushObject`, check out the [properties section](https://developer.mozilla.org/en-US/docs/Web/API/Notification) of this MDN doc.*

Setting up and using `remix-pwa` first-ever server utility requires some steps to be functional, let's break them down:

1. After installing `remix-pwa`, if you intend to use the Push API, run the command:
```sh
npx web-push generate-vapid-keys
```

You would get two keys in your console, a PRIVATE key and a PUBLIC key. Keep your PRIVATE key safe!

2. Create a `.env` file, and save your keys using the variable name `VAPID_PUBLIC_KEY` for the public key and `VAPID_PRIVATE_KEY` for the private key.

Add the following line of code in `entry.server.ts`:
```ts
import "dotenv/config"
```

> *Don't forget to add the `.env` file to your `.gitignore` file*

3. You can finally use the basic Web Push API in your server!

```tsx
import { PushNotification } from "~/utils/server/pwa-utils.server.ts"

export const action: ActionFunction = async ({ request }) => {
  // Get request and perform other operations
  
  await PushNotification({
    title: "Remix PWA",
    body: "A server generated text body."
  }, 1)
}
```

[(Back to Top)](#table-of-content)

> **⚠ Hang On tight! We are working on bringing more awesome features to you amazing folks. ⚠**

## Installation Manual Guide

Ahh! What a mouthful for a section topic 🤭. This section is to explain the various prompts that comes up during `remix-pwa` installation. 

> *Or you can alternatively run `npx remix-pwa@latest -h` to view help* 

### Language

This is quite self-explanatory. It is the language you used for your Remix project, possible options are:
- TypeScript
- JavaScript

### Caching Strategy

This is a new one 🎉🎉! There are two possible options:
- Just-In-Time Caching
- Precaching

**Just-In-Time Caching:**

Also known as `jit`. This is the default caching strategy remix-pwa has been using for a while. It is a good choice for most projects. It caches only pages and responses the user has navigated to, and updates the cache when the page information changes and the user is online. This would be preferred when building a large, dynamic application with notable parameterized routes.

**Precaching Strategy:**

This is the new one 🥁! It is a caching strategy that is used when you want to cache all the pages and responses of your application on first load. It is a good choice for small, static applications. It caches all the pages and responses of your application, and updates the cache when the page information changes and the user is online. This would be preferred when building a small, static application with few parameterized routes. ⚠️ *Does not cache parameterized routes!* ⚠️

Thanks to [mokhtar](https://.github.com/m5r) for his contribution!

### PWA Features

Another new feature and a way to install the minimal things you need automatically. Remix PWA comes with five major features:
- Service Workers
- Web Manifest
- Push API Services
- Client Utilities (APIs to help build a PWA)
- Development Icons

**Service Workers** are the standard and a vital part of every Progressive Web Application. Handles the caching and a lot more in Remix PWA.

**Web Manifests** are the main file that is used to generate the Progressive Web App. It is a JSON file that provides information and characteristics for your application. *Needed if you want to deploy on App Stores*.

**Push API** is a server API to integrate Push Notifications into your application. It was made standalone feature due to the amount of work needed to setup successfully and not all apps need it.

**Client Utilities** are a set of APIs that help with getting that native feel of your PWA quickly. They consist of functions to toggle fullscreen, manage App badges, copy images, send files and much more.

**Development Icons** are a set of Remix icons you can use to quickly get icons for your PWA. *They need to be linked though! Get the `link` from [here](https://github.com/ShafSpecs/remix-pwa/blob/main/examples/pwa-links.ts#L9)*

### `app` location

This is the location where your `app` folder is located. By default, remix uses the `/app` directory in your project root. But lots of users change the location of the app root folder (e.g. `/src/app`). This now allows app directory of all kinds to be used.
**Note that the directory input must not have a trailing or leading slash!!** (e.g. `app` for `/app` and `src/app` for `/src/app/`)

### Installing Dependencies

This one is straightforward. Do you want to install Remix PWA dependencies right now and continue developing your app or do you want to skip it and continue your work with lots of errors due to missing dependencies? 

P.S. This section was buggy when used with yarn. The issue has been successfully patched

### Using The Push Server API? (See also Server API)

Setting up and using `remix-pwa`'s server APIs requires some steps to be functional, let's break them down:

1. After installing `remix-pwa`, if you intend to use the Push API, run the command:
```sh
npx web-push generate-vapid-keys
```

You would get two keys in your console, a PRIVATE key and a PUBLIC key. Keep your PRIVATE key safe!

2. Create a `.env` file, and save your keys using the variable name `VAPID_PUBLIC_KEY` for the public key and `VAPID_PRIVATE_KEY`for the private key.

Add the following line of code in `entry.server.ts`:
```ts
import "dotenv/config"
```

> *Don't forget to add the `.env` file to your `.gitignore` file*

3. You can finally use the basic Web Push API in your server!

### Issues With The CLI Installation

There is currently an issue you may run into with the CLI, if you have comments in your root.tsx file you may get an error when running: 

```sh
  npx remix-pwa@latest
```
Simply removing the comments and reattempting installation seems to fix any such errors.

[(Back to Top)](#table-of-content)

## Going Deeper

Want to upgrade your PWA utilities? Or need a bit of tips and hints on how to customize your PWA? Want to make a production-ready PWA with Remix? This is your section! Let's get down to the nitty-gritty of `remix-pwa`.

### Customizing your PWA `manifest`

One thing you might have noticed is that your manifest.json file is stored as a route and not a public, static file. This is due to an advantage of Remix and Remix PWA. By making our manifest a route, we allow developers to customise a PWA based on a user-to-user preference! 

Allow me to break that down. If you open your `manifest` file located in `app/routes/resources`, you would notice that the manifest is stored inside a loader function that returns the manifest as a json object. Now if you wanted to customise the manifest based on user preference instead of a static file, how would we do it? Let's look at a simple  scenario to answer that question:

We have a database hosted on an external platform and we are using [Prisma](https://prisma.io) as our database ORM, we save our user's color preferences in a table called `User` and we want to give the PWA the same feel as the website. The last thing we want to do is give our user's default color scheme for the app and custom schemes for the site. That's poor UX. 

To solve this issue, we can simply import our `PrismaClient` into our manifest route's loader and interact with our db from there. Allowing us to get the preferences and set them as our PWA's theme and background color (for more info on themes and background-color, refer to [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/Manifest))

```ts
// A mock implementation
export let loader: LoaderFunction = async () => {
  const preference = await prismaClient().user.findUnique({
    where: {
      username: "example@gmail.com"
    },
    include: {
      themePreference,
      bgPreference
    }
  })
  
  return json(
    {
      background_color: preference.bgPreference,
      theme_color: preference.themePreference,
      // ..Rest of the manifest
    }
  )
}
```

We got our user, we got their preference, we make them happy!

### Upgrading your PWA `manifest`

Our manifest is quite simple and default, the `name` of the app is something that you would change, same as the theme color and other json fields depending on your taste and App's needs. But how about we add more to our manifest instead?

In v0.6.0, we created a new field in our manifest known as `shortcuts` and they do exactly what they say. They allow us to navigate to certain, specified pages (*routes*) directly from outside our App. I won't cover the fields used in the `shortcuts` and what they are meant to do, I would however, drop [this](https://dev.to/azure/09-creating-application-shortcuts-m0i) wonderful article by Microsoft Azure that explains the `shortcut` aspect of the manifest well.

Another thing you would love to edit is the `icons` field in the manifest, we used default Remix icons for those but you would not want to. Instead replace the icons in `/icons` folder and specify their sizes. You could use [Sketch](https://sketch.com) or [Figma](https://figma.com) to design and resize icons. You could also delete the default `favicon.ico` that comes with Remix.

> This section is till WIP 🚧

[(Back to Top)](#table-of-content)

## Roadmap

Want to see proposed features and bug fixes? Or do you want to propose an idea/bug fix for `remix-pwa` and want to view the current roadmap? Check out `remix-pwa` [Roadmap](./ROADMAP.md) and see what lies in wait for us!

## Contributing

Thank you for your interest in contributing 🙂. The contribution guidelines and process of submitting Pull Requests are available in the [CONTRIBUTING.md](./CONTRIBUTING.md). Awaiting your PR 😉!

## Support 

If you want to get help on an issue or have a question, you could either [open an issue](https://github.com/ShafSpecs/remix-pwa/issues/new/choose) or you could ask your questions in the [Official Remix's Discord Server](https://discord.gg/TTVwU2wZca) where there are a lot of helpful people to help you out.

### Extra Resources For Understanding PWAs

[Mozilla: Introduction to PWAs](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Introduction)

## Authors

- Abdur-Rahman (aka [@ShafSpecs](https://github.com/ShafSpecs))

- Douglas Muhone ([theeomm](https://github.com/theeomm))

- Mokhtar ([mokhtar](https://github.com/m5r))

- Tom ([pumpitbetter](https://github.com/pumpitbetter))

- Brock Donahue ([Brocktho](https://github.com/Brocktho/))

- Afzal Ansari ([dev-afzalansari](https://github.com/dev-afzalansari))

- Special thanks to [jacob-ebey](https://github.com/jacob-ebey) for his contribution and help with the creation of `remix-pwa`!

## License

This project is licensed under the MIT License - see the [LICENSE.md](./LICENSE.md) file for details.
