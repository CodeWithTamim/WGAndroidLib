
# WireGuard Android Library

[![Release](https://jitpack.io/v/username/wireguard-android.svg)](https://jitpack.io/#username/wireguard-android)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Platform](https://img.shields.io/badge/platform-Android-green.svg)](https://developer.android.com)

Simplify the integration of **WireGuard VPN** in your Android applications with this library. This library provides a clean API to manage VPN connections, handle configurations, and monitor tunnel states.

---

## Features

- **Lightweight & Fast**: Minimal overhead with seamless integration.
- **Comprehensive API**: Start, stop, and monitor VPN connections effortlessly.
- **State Broadcasts**: Easily observe and react to connection state changes.
- **Cross-Language Support**: Examples provided in both Kotlin and Java.

---

## Installation

This library is available via [JitPack](https://jitpack.io). Add the repository and dependency to your project:

### Gradle Groovy
```gradle
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}

dependencies {
    implementation 'com.github.username:wireguard-android:Tag'
}
```

### Gradle Kotlin DSL
```kotlin
repositories {
    ...
    maven("https://jitpack.io")
}

dependencies {
    implementation("com.github.username:wireguard-android:Tag")
}
```

---

## Setup

### 1. Configure the Application Class

Create an `Application` class to configure a notification channel for VPN usage:

#### Kotlin Example
```kotlin
class TunnelApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        createNotificationChannel()
    }

    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val manager = NotificationManagerCompat.from(this)
            val channel = NotificationChannel(
                CHANNEL_ID,
                CHANNEL_NAME,
                NotificationManager.IMPORTANCE_HIGH
            )
            manager.createNotificationChannel(channel)
        }
    }
}
```

#### Java Example
```java
public class TunnelApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        createNotificationChannel();
    }

    private void createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationManager manager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            NotificationChannel channel = new NotificationChannel(
                CHANNEL_ID,
                CHANNEL_NAME,
                NotificationManager.IMPORTANCE_HIGH
            );
            manager.createNotificationChannel(channel);
        }
    }
}
```

### 2. Add Required Permissions

Add these permissions to your `AndroidManifest.xml` file:

```xml
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_SPECIAL_USE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
```

### 3. Register Services

Declare the required services in `AndroidManifest.xml`:

```xml
<service
    android:name="com.nasahacker.wireguard.service.TunnelService"
    android:exported="true"
    android:foregroundServiceType="specialUse"
    android:permission="android.permission.FOREGROUND_SERVICE" />

<service
    android:name="com.wireguard.android.backend.GoBackend$VpnService"
    android:exported="true"
    android:permission="android.permission.BIND_VPN_SERVICE">
    <intent-filter>
        <action android:name="android.net.VpnService" />
    </intent-filter>
</service>
```

---

## Usage

### Initialize the Service Manager

#### Kotlin Example
```kotlin
ServiceManager.init(this, R.drawable.notification_icon)
```

#### Java Example
```java
ServiceManager.init(this, R.drawable.notification_icon);
```

### Start and Stop the VPN

#### Prepare for VPN Connection

**Kotlin**
```kotlin
if (!ServiceManager.isPreparedForConnection(context)) {
    ServiceManager.prepareForConnection(activity)
}
```

**Java**
```java
if (!ServiceManager.isPreparedForConnection(context)) {
    ServiceManager.prepareForConnection(activity);
}
```

#### Start the VPN

**Kotlin**
```kotlin
val config = TunnelConfig(
    Interface("10.0.0.1/24", "privateKey", 51820),
    Peer("publicKey", listOf("0.0.0.0/0"), "endpoint:51820")
)
ServiceManager.startTunnel(context, config, blockedApps = listOf("com.example.app"))
```

**Java**
```java
TunnelConfig config = new TunnelConfig(
    new Interface("10.0.0.1/24", "privateKey", 51820),
    new Peer("publicKey", Arrays.asList("0.0.0.0/0"), "endpoint:51820")
);
ServiceManager.startTunnel(context, config, Arrays.asList("com.example.app"));
```

#### Stop the VPN

**Kotlin**
```kotlin
ServiceManager.stopTunnel(context)
```

**Java**
```java
ServiceManager.stopTunnel(context);
```

---

## Listen for Tunnel State Changes

Register a broadcast receiver to listen for state changes:

#### Kotlin Example
```kotlin
val receiver = object : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        val state = intent?.getStringExtra("TUNNEL_STATE")
        // Handle state change: CONNECTED, DISCONNECTED, CONNECTING
    }
}
context.registerReceiver(receiver, IntentFilter("TUNNEL_STATE_ACTION"))
```

#### Java Example
```java
BroadcastReceiver receiver = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        String state = intent.getStringExtra("TUNNEL_STATE");
        // Handle state change: CONNECTED, DISCONNECTED, CONNECTING
    }
};
context.registerReceiver(receiver, new IntentFilter("TUNNEL_STATE_ACTION"));
```

---

## Tunnel Configurations

Define configurations for the VPN connection using the `TunnelConfig` model.

**Kotlin Example**
```kotlin
val config = TunnelConfig(
    Interface("10.0.0.1/24", "privateKey", 51820),
    Peer("publicKey", listOf("0.0.0.0/0"), "endpoint:51820")
)
```

**Java Example**
```java
TunnelConfig config = new TunnelConfig(
    new Interface("10.0.0.1/24", "privateKey", 51820),
    new Peer("publicKey", Arrays.asList("0.0.0.0/0"), "endpoint:51820")
);
```

---

## Tunnel States

The library supports these tunnel states:
- **CONNECTED**: VPN is active and connected.
- **DISCONNECTED**: VPN is stopped.
- **CONNECTING**: VPN is in the process of connecting.

States are broadcasted by the service and can be used to update your UI.

---

## License

This project is licensed under the Apache 2.0 License. See the [LICENSE](LICENSE) file for details.

---

Developed by [Tamim Hossain](mailto:tamimh.dev@gmail.com).
