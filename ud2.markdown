---
layout: page
title: UD2 Broadcast Recevier
permalink: /ud2/
---

# Broadcast Receiver

## Introduction
----------------

Android apps can send or receive broadcast messages from the Android system and other Android apps.

These broadcasts are sent when an event of interest occurs. For example, the Android system sends broadcasts when different system events occur, such as when the system starts up or when the device starts to charge. Apps can also send custom broadcasts, for example to notify other apps of something they might be interested in (like when some new data was downloaded)

Apps can register to receive specific broadcasts. When a broadcast is sent, the system automatically redirects broadcasts to apps that have subscribed to receive that particular type of broadcast.

Broadcasts can generally be used as a messaging system between apps and outside of the normal user flow. However, you should be careful not to abuse the opportunity to respond to broadcasts and run background tasks that can contribute to slow system performance.

### About system emissions

The system automatically sends broadcasts when various system events occur, such as when the system turns airplane mode on or off. System broadcasts are sent to all apps that have subscribed to receive the event.

The broadcast message itself is wrapped in an **Intent** object whose action string identifies the event that occurred (for example, android.intent.action.AIRPLANE_MODE )

The intent can also contain additional information included in its additional field. For example, the airplane mode intent includes an additional boolean value indicating whether or not the mode is on.

For a complete list of system broadcast actions, see the **BROADCAST_ACTIONS.TXT** file in the Android SDK inside **\platforms\VERSION\data**.

Each issue action has an associated constant field. For example, the value of the **ACTION_AIRPLANE_MODE_CHANGED** constant is **android.intent.action.AIRPLANE_MODE**. Documentation of each issue action is available in its associated constant field.

### Changes in system emissions

As the Android platform evolves, the behavior of system broadcasts changes periodically. Please note the following changes if your app targets Android 7.0 (API level 24) or higher, or is installed on devices running Android 7.0 or higher.

## How to receive broadcasts

Apps can receive broadcasts in two ways:
1. through listeners declared in the manifest
2. through listeners registered in the context


> Note: If your app targets API level 26 or higher, you cannot use the manifest to declare a listener for implicit broadcasts (broadcasts that are not specifically targeted to your app), except for some implicit broadcasts that are exempt from that restriction. In most cases, you can use scheduled tasks instead.



To declare a broadcast receiver in the manifest, perform the following steps

1. Specify the **&lt;receiver>** element in your app's manifest. **AndroidManifest.xml**
   
```xml
....
<receiver android:name=".MyBroadcastReceiver"  android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
    </intent-filter>
</receiver>
....
```
Intent filters specify the broadcast actions that your listener subscribes to.

1. Create the **BroadcastReceiver** subclass, and then implement **onReceive(Context, Intent)**. The broadcast receiver in the following example logs and displays the content of the broadcast:

```kotlin
private const val TAG = "MyBroadcastReceiver"

class MyBroadcastReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        StringBuilder().apply {
            append("Action: ${intent.action}\n")
            append("URI: ${intent.toUri(Intent.URI_INTENT_SCHEME)}\n")
            toString().also { log ->
                Log.d(TAG, log)
                Toast.makeText(context, log, Toast.LENGTH_LONG).show()
            }
        }
    }
}
```

The system package manager registers the receiver when the app is installed. The receiver then becomes an entry point to your standalone app, meaning that the system can launch the app and deliver the stream if the app is not running.

The system then creates a new **BroadcastReceiver** component object to handle each broadcast it receives. This object is valid only during the call to onReceive(Context, Intent). Once this method returns your code, the system considers the component to be no longer active.

### Receivers registered in the context

To register a listener with a context, perform the following steps:

1. Create an instance of **BroadcastReceiver**.
```kotlin
val br: BroadcastReceiver = MyBroadcastReceiver()
```
2. Create an **IntentFilter** and register the receiver by calling **registerReceiver(BroadcastReceiver, IntentFilter)**

```kotlin
val filter = IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION).apply {
    addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED)
}
registerReceiver(br, filter)
```

>  Note: To register to receive local broadcasts, call **LocalBroadcastManager**.registerReceiver(BroadcastReceiver, IntentFilter) instead.

Receivers registered to the context receive broadcasts as long as their registration context is valid. For example, if you register in an Activity context, you will receive broadcasts as long as the activity is not removed. If you register with the **application context**, you will receive broadcasts while the app is running.

3. To stop receiving broadcasts, call **unregisterReceiver(android.content.BroadcastReceiver)**. Be sure to unregister the receiver when you no longer need it or the context is no longer valid

## How to submit broadcasts
------

Android offers three ways for apps to send broadcasts:

1. The ***sendOrderedBroadcast(Intent, String)*** method sends broadcasts to one receiver at a time. Because one listener is executed at a time, the listener can either propagate a result to the next, or it can abort broadcast altogether so that it is not transmitted to others. The order in which listeners are executed can be controlled with the android:priority attribute of the matching intent filter; receivers with the same priority will be executed in random order.

2. The ***sendBroadcast(Intent)*** method sends broadcasts to all receivers in an unspecified order, which is called a normal broadcast. This method is more efficient, but it means that the receivers cannot read the results of other receivers, propagate the data received from the broadcast, or abort the broadcast.
3. The ***LocalBroadcastManager.sendBroadcast*** method sends broadcasts to receivers that are in the same app as the sender. If you don't need to send broadcasts between apps, use local broadcasts. The implementation is much more efficient (inter-process communication is not required) and you don't have to worry about any security issues related to other apps that may receive or send broadcasts.
   
The following code snippet shows how to send a broadcast by creating an intent and calling sendBroadcast(Intent).

```kotlin
Intent().also { intent ->
    intent.setAction("com.example.broadcast.MY_NOTIFICATION")
    intent.putExtra("data", "Notice me senpai!")
    sendBroadcast(intent)
}
```

The broadcast message is wrapped in an **Intent** object. The intent's action string must provide the app's Java package name syntax and also uniquely identify the emitting event. You can attach additional information to the intent with ***putExtra(String, Bundle)***. You can also limit a broadcast to a set of apps in the same organization by calling setPackage(String) on ​​the intent

## How to restrict emissions with permissions
---------------------

Permissions allow you to restrict broadcasts to a set of apps that have specific permissions. You can apply restrictions on both the sender and receiver of a broadcast.

### How to Submit Emissions with permissions

When you call ***sendBroadcast(Intent, String)*** or ***sendOrderedBroadcast(Intent, String, BroadcastReceiver, Handler, int, String, Bundle)***, you can specify a permission parameter. Only receivers who requested that permission with the tag in their manifest (and who were later granted the permission if it's dangerous) can receive the broadcast. For example, the following code sends a broadcast:

```kotlin
sendBroadcast(Intent("com.example.NOTIFY"), Manifest.permission.SEND_SMS)
```

```xml
<uses-permission android:name="android.permission.SEND_SMS" />
```

You can specify an existing system permission like **SEND_SMS** or define a custom permission with the **&lt;permission>** element.

### How to Receive Emissions with Permissions


If you specify a permissions parameter when registering a broadcast receiver (either with registerReceiver(BroadcastReceiver, IntentFilter, String, Handler) or in the **&lt;receiver>** tag in your manifest), only broadcasters that requested the permission with the **&lt;uses tag -permission>** in your manifest (and those subsequently granted the permission if it's dangerous) can send an intent to the receiver.

For example, suppose your receiving app has a receiver declared in the manifest as shown below:

```xml
<receiver android:name=".MyBroadcastReceiver"
            android:permission="android.permission.SEND_SMS">
    <intent-filter>
        <action android:name="android.intent.action.AIRPLANE_MODE"/>
    </intent-filter>
</receiver>
```

Or the receiving app has a registered listener in the context, as follows:

```kotlin
var filter = IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED)
registerReceiver(receiver, filter, Manifest.permission.SEND_SMS, null )
```

Then, in order to send broadcasts to those receivers, the sending app must request permission as follows:

```xml
<uses-permission android:name="android.permission.SEND_SMS"/>
```

[Here](https://github.com/sdram58/PMDM2021/tree/master/BroadcastReceiver) you have the Broadcast Example