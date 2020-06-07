---
pagename: Getting Started
redirect_from:
  - consumer-experience-voice-video-android-prerequisites.html
  - voice-and-video-for-android-sdk-beta-getting-started-prerequisites.html
  - consumer-experience-voice-video-android-installing-the-sdk.html
  - voice-and-video-for-android-sdk-beta-getting-started-installing-the-sdk.html
  - consumer-experience-voice-video-android-integrate-the-sdk.html
  - voice-and-video-for-android-sdk-beta-getting-started-integration-guide.html
  - voice-and-video-for-android-sdk-beta-getting-started.html
sitesection: Documents
categoryname: "Messaging Channels"
documentname: Mobile App Messaging SDK for Android
subfoldername: Voice & Video for Android SDK (BETA)
permalink: mobile-app-messaging-sdk-for-android-voice-and-video-for-android-sdk-beta-getting-started.html
indicator: messaging
---

<div class="important">
<h2>Deprecation Notice</h2>

the CoApp product is deprecated and will be discontinued from February 28th, 2020 on.
</div>

### Prerequisites

LivePerson Voice & Video is an SDK (_Software Development Kit_) for the **Google Android** platform. In order to integrate with us, you need to have an app of your own to which you have full source code access. Basic programming knowledge is required.

#### Dependencies

The SDK requires our LivePerson [**Messaging SDK**](android-overview.html) integrated into your app. Your consumers will always engage in a messaging conversation first, before your agents choose to escalate the conversation to a *Voice*, *Video* or *CoBrowse* session.

Please make sure your app meets the following minimal requirements:

- Minimal supported API level is 21 (Lollipop, Android 5.0).
- Recommended minimal API level is 23 (Marshmallow, Android 6.0).
- In-app CoBrwosing is only supported in API levels >= 21 (Lollipop) due to [MediaProjection API](https://developer.android.com/reference/android/media/projection/MediaProjection.html).
- Java 8 Source and target compatibility.

#### Supported Android Versions

| Android API level | Support |  Limitations |
| ------------- |:-------------:|:-------------|
| 20 or less | not supported  | - |
| 21 + | supported | fully supported |

#### Supported Devices

In general, all Android phones starting from API level 21 are supported. However, due to vendor-specific customizations to the Android OS, we provide certified support for selected devices. Please see the support chart (last revision: May 2017) below:

| Brand | Model |  v5.X |  v6.X |  v7.X |
| ------------- |:-------------:|:-------------|:-------------|:-------------|
| Samsung | Galaxy S5 | Certified (internally) | Certified (internally) | |
|  | Galaxy A3 | Certified (internally) | Certified (internally) | |
|  | Galaxy Nexus | Supported | Supported | |
|  | Galaxy S7 | Supported | Supported | |
|  | Galaxy S8 | Supported | Supported | |
| Nexus | Nexus | Supported | Supported | |
|  | Nexus 4 | Supported | Supported | |
|  | Nexus 5 | Supported | Supported | |
|  | Nexus 5X | Supported | Supported | |
| Huawei | Y5 | Supported | Supported | |
|  | P9 Lite | Certified (internally) | Certified (internally) | |
| Google | Pixel | Supported | Supported | Supported |
| Google | Pixel 2 | Supported | Supported | Supported |

#### Supported Programming Languages

Only **native applications** written in **Java** or **Kotlin** are currently supported.

**Note:** Cross-platform apps using native wrappers (e.g. Cordova) can be made to integrate with voice & video support with some additional setup effort. Remote-app control however is only possible on native UI components (like generated by _React Native_ or _Titanium_). **Neither are currently officially supported.**

### Installing the SDK

#### Setting up Android Studio

In order to use the Voice & Video SDK within your project, you need to have the LP-Messaging-SDK for Android preconfigured and set up in your project (minimum required version is [**v2.0**](https://github.com/LP-Messaging/Android-Messaging-SDK/tree/v2.0.0)). Please take a look at the [Official Documentation](android-overview.html) for further information.

#### Setting up with Gradle (recommended)

The CoApp SDK is published in [LivePerson Bintray](https://bintray.com/liveperson-mobile/coapp).
Currently the SDK is not published in JCenter. You can add the CoApp repository to list of repositories in
your `build.gradle`

```gradle
    repositories {
        maven {
            url 'https://bintray.com/liveperson-mobile/coapp/lpcoappsdk'
        }
    }
```

To add the SDK to your project, just add the following dependency to your `build.gradle`:

```gradle
    implementation 'com.liveperson.coapp:coapp:LATEST@aar'
```

#### Manual Installation

If you do not want to use dependency management you can add the desired Android SDK archive `*.aar` file manually. Just add the `coapp-release.aar` to the project's `libs` folder and adjust your Gradle build file like shown below:

```gradle
...
android {
    ...
    repositories {
        ...
        flatDir {
            dirs 'libs' // i.e. if LPCoapp-SDK-Android-0.1.0-release.aar resides in <your-project>/libs
        }
    }
}

dependencies {
    ...
    compile(name:'LPCoapp-SDK-Android-0.1.0-release', ext:'aar')
    // add 3rd-party dependency for Dexter as well
    compile 'com.karumi:dexter:2.3.1'
}
```

**Hint:** If you have trouble integrating the SDK in your project you should take a look at our [Sample App](consumer-experience-voice-video-android-sample-app.html) project which is already preconfigured and ready to go.

#### Next steps

After the SDK is added as a dependency to your app's project, you can now proceed with Integration to see how to use the Voice & Video SDK in your code, enable Push Notifications, start a call, and more.

### Integrate the SDK

After adding the SDK as a dependency to your app, you now need to call it from within your codebase.

#### Step 1 - Adjust the AndroidManifest.xml

Add the following permissions to your `AndroidManifest.xml` to enable Voice, Video and In-app CoBrowse capabilities:

```XML
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature android:name="android.hardware.camera" />
    <uses-feature android:name="android.hardware.camera.autofocus" />
```
**Note:** Starting with Android 6.0 (API level 23), runtime permissions need to be granted while the application is running. The SDK will prompt for the following permissions when initiating a call:

* Manifest.permission.CAMERA;
* Manifest.permission.RECORD_AUDIO;
* Manifest.permission.CAPTURE_VIDEO_OUTPUT;
* [MediaProjectionManager.createScreenCaptureIntent()](https://developer.android.com/reference/android/media/projection/MediaProjectionManager.html#createScreenCaptureIntent());

#### Step 2 - Hook into your application

To initialize the SDK, you need to add it to your application's context. This is done by registering the `LPCoApp()`-Manager instance to the app's Activity lifecycle. If you don't have an `Application` class yet, just create one and adjust it like this:

```Java
package com.liveperson.sampleapplication;
import android.app.Application;
import com.liveperson.coapp.LPCoApp; // the LPCoApp()-Manager

public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        // Register the SDK to an activity lifecycle, that's it!

        LPCoApp coapp = LPCoApp.shared(getApplicationContext());
        registerActivityLifecycleCallbacks(coapp);
    }
}
```

This is all that is needed to enable our SDK in your app. You can now proceed by adding Push Notification capability, apply branding, etc.

#### Additional Configurations

##### Listen to FCM/GCM Push Notifications

If you want to initiate calls via [Push Notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) you need to add the LPCoApp-Notification-Handler to your FCM/GCM Messaging Receiver (**Note:** if you haven't set up Messaging SDK with Push-Notifications please see [Mobile App Messaging SDK Push Notifications](android-push-notifications.html) and [Register your App](consumer-experience-voice-video-android-register-app.html) for further instructions).

Add the following lines to your Messaging Receiver Service:

```Java
public class MyMessagingService extends FirebaseMessagingService {

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        LPCoApp.handlePush(this, remoteMessage, null);
        ...
    }
    ...
```
**Bonus**: If you want to add a [TaskStack](https://developer.android.com/reference/android/support/v4/app/TaskStackBuilder.html) to the Notification (i.e. if you want Call-View to return to a certain Activity when closed), you can do so by implementing a `getTaskStack()` method, i.e.:

```Java
    private TaskStackBuilder getTaskStack() {
        TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
        // The task stack should contain i.e. the MainActivity
        stackBuilder.addParentStack(MainActivity.class);
        return stackBuilder;
    }
```
and call it with:

```Java
    LPCoApp.handlePush(this, remoteMessage, getTaskStack());
```