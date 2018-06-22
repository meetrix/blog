---
title: "Jitsi Apk Build"
permalink: /jitsi/custormization/
excerpt: "Jitsi meet android apk custormization"
header:
  overlay_image: /assets/images/webrtc/05-apk-build-jitsi/05-apk-build-jitsi.png
  teaser: /assets/images/webrtc/05-apk-build-jitsi/05-apk-build-jitsi.png
  overlay_filter: 0.5
last_modified_at: 2018-05-05T15:58:49-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Supun
---

jitsi meet android app custormization

## Change Following Files  for custormize you android app

domain : meet.meetrix.io

1. config.js<br/>
`hosts:{
    domain: 'meet.meetrix.io',
    muc: 'conference.meet.meetrix.io',
    bosh: '//meet.meetrix.io/http-bind',
  }`
2. webpack.config.js<br/>
  `const devServerProxyTarget
    = process.env.WEBPACK_DEV_SERVER_PROXY_TARGET || 'https://meet.meetrix.io';`

3. react/features/app/components/AbstractApp.js<br/>
  `const DEFAULT_URL = 'https://meet.meetrix.io';`

4. react/features/welcome/components/WelcomePageSideBar.native.js<br/>
  `const PRIVACY_URL = 'https://meetrix.com/privacy';
  const SEND_FEEDBACK_URL = 'mailto:support@meetrix.com';
  const TERMS_URL = 'https://meetrix.com/terms';`

5. android/app/src/main/AndroidManifest.xml<br/>
  `<data android:host="meet.meetrix.io" android:scheme="https" />`

6. android/app/build.gradle<br/>
  `defaultConfig {
        applicationId 'meetrix.io'
  }`
7. android/app/src/main/java/org/jitsi/meet/MainActivity.java<br/>
  `package com.meetrix.meet;`

8. android/app/src/main/res <br/>
  `change ic_lancher image with your logo.png`
  
9. android/app/src/main/res/values/strings.xml<br/>
 ```xml
   <resources>
     <string name="app_name">Meetrix Meet</string>
   </resources>
 ```
10. android/app/src/main/AndroidManifest.xml<br/>
  ```xml
    <manifest
      xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.meetrix.meet">
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher">
        <intent-filter>
          <data android:host="meet.meetrix.io" android:scheme="https" />
          <data android:host="meetrix.io" android:scheme="https" />
        </intent-filter>
        <intent-filter>
          <data android:scheme="com.meetrix.meet" />
        </intent-filter>
      </activity>
    </application>
  ```
---
## react native environment setup
  `https://facebook.github.io/react-native/docs/getting-started.html`
## react native app run
  `react-native run-android`
## connect with development server
  `https://facebook.github.io/react-native/docs/running-on-device.html#method-1-using-adb-reverse-recommended`
## react native android app signing
  `https://facebook.github.io/react-native/docs/signed-apk-android.html`











