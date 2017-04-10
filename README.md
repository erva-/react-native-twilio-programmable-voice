# react-native-twilio-programmable-voice
React Native wrapper for Twilio Programmable Voice SDK

Before starting, we recommend you get familiar with [Twilio Programmable Voice SDK](https://www.twilio.com/docs/api/voice-sdk).
It's easier to integrate this module into your react-native app if you follow the Quickstart tutorial from Twilio, because it makes very clear which setup steps are required.

## Installation
```
npm install react-native-twilio-programmable-voice --save
```

## iOS Installation

This current version uses the CallKit method only.


## Android Installation

Setup GCM or FCM

You must download the file `google-services.json` from the Firebase console.
It contains keys and settings for all your applications under Firebase. This library obtains the resource `senderID` for registering for remote GCM from that file.

**NOTE: To use a specific `play-service-gcm` version, update the `compile` instruction in your App's `android/app/build.gradle` (replace `10.2.0` with the version you prefer):**
```gradle
...

buildscript {
  ...
  dependencies {
    classpath 'com.google.gms:google-services:3.0.0'
  }
}

...

dependencies {
    ...

    compile project(':react-native-twilio-programmable-voice')
    compile ('com.google.android.gms:play-services-gcm:10.2.0') {
        force = true;
    }
}

// this plugin looks for google-services.json in your project
apply plugin: 'com.google.gms.google-services'
```

In your `AndroidManifest.xml`
```xml
    .....
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <permission
        android:name="${applicationId}.permission.C2D_MESSAGE"
        android:protectionLevel="signature" />
    <uses-permission android:name="${applicationId}.permission.C2D_MESSAGE" />
    <uses-permission android:name="android.permission.VIBRATE" />

    <application ....>
        <receiver
            android:name="com.google.android.gms.gcm.GcmReceiver"
            android:exported="true"
            android:permission="com.google.android.c2dm.permission.SEND" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <category android:name="${applicationId}" />
            </intent-filter>
        </receiver>

        <service android:name="com.hoxfon.react.TwilioVoice.gcm.GCMRegistrationService" />

        <service
            android:name="com.hoxfon.react.TwilioVoice.gcm.VoiceGCMListenerService"
            android:exported="false" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            </intent-filter>
        </service>

        <service
            android:name="com.hoxfon.react.TwilioVoice.gcm.VoiceInstanceIDListenerService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID" />
            </intent-filter>
        </service>
     .....

```

In `android/settings.gradle`
```gradle
...

include ':react-native-twilio-programmable-voice'
project(':react-native-twilio-programmable-voice').projectDir = file('../node_modules/react-native-twilio-programmable-voice/android')
```

Register module (in `MainApplication.java`)

```java
import com.hoxfon.react.TwilioVoice.TwilioVoicePackage;  // <--- Import Package

public class MainApplication extends Application implements ReactApplication {

    private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
        @Override
        protected boolean getUseDeveloperSupport() {
            return BuildConfig.DEBUG;
        }

        @Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                new MainReactPackage(),
                new TwilioVoicePackage() // <---- Add the Package
            );
        }
    };
    ....
}
```

## Usage

```javascript
import TwilioVoice from 'react-native-twilio-programmable-voice'
...

// initialise the Programmable Voice SDK passing an access token obtained from the server.

async function initTelephony() {
    try {
        const accessToken = await getAccessTokenFromServer()
        const success = await TwilioVoice.initWithToken(accessToken)  
    } catch (err) {
        console.err(err)
    }
}


// add listeners
TwilioVoice.addEventListener('deviceReady', deviceReadyHandler)
TwilioVoice.addEventListener('deviceNotReady', deviceNotReadyHandler)
TwilioVoice.addEventListener('deviceDidReceiveIncoming', deviceDidReceiveIncomingHandler)
TwilioVoice.addEventListener('connectionDidConnect', connectionDidConnectHandler)
TwilioVoice.addEventListener('connectionDidDisconnect', connectionDidDisconnectHandler)

...

// start a call
TwilioVoice.connect({To: '+61234567890'})

// hangupq
TwilioVoice.disconnect()

// accept an incoming call
TwilioVoice.accept()

// reject an incoming call
TwilioVoice.reject()

// ignore an incoming call
TwilioVoice.ignore()

// mute or unmute the call
// mutedValue must be a boolean
TwilioVoice.setMuted(mutedValue)

TwilioVoice.sendDigits(digits)

TwilioVoice.requestPermission(GCM_sender_id)

// should be called after the app is initialised
// to catch incoming call when the app was in the background
TwilioVoice.getIncomingCall()
    .then(incomingCall => {
        if (incomingCall){
            _deviceDidReceiveIncoming(incomingCall)
        }
    })

```


## Credits

[voice-quickstart-android](https://github.com/twilio/voice-quickstart-android)

[react-native-push-notification](https://github.com/zo0r/react-native-push-notification)


## License

MIT
