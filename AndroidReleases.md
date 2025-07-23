
## Android 15 (API 34/35 - Latest Preview/Beta)

*Please note: Android 15 is currently in development (Developer Previews and Betas). Features are subject to change before the stable release.*

**New Features & APIs:**

  * **Privacy Sandbox on Android:** Continued work on a new privacy-preserving advertising platform.
  * **Health Connect updates:** Enhanced support for health and fitness data.
  * **Satellite Connectivity:** APIs to detect satellite availability and use satellite connectivity for messaging.
  * **Media Processing Foreground Service Type:** A new foreground service type specifically for media processing, with its own lifecycle and privileges.
  * **Safer Intents and Activity Launches:** More restrictions on how apps can launch activities from the background or use pending intents, enhancing security.
  * **Restrictions on `BOOT_COMPLETED` Broadcast Receivers:** Limitations on which foreground services can be launched directly from a `BOOT_COMPLETED` receiver.
  * **OpenJDK API changes:** Updates to align with newer OpenJDK releases.
  * **Predictive Back Gestures:** Enhanced system animations for the back gesture, allowing users to peek at the destination before completing the gesture. (This started in Android 14 but is being refined).

**Behavior Changes (for apps targeting API 35):**

  * **Foreground Service Changes:** Stricter rules and timeout behaviors for foreground services, particularly `dataSync`. New types like `mediaProcessing` are introduced.
  * **Global Do Not Disturb Mode Changes:** Apps have more limited ability to modify the global state of Do Not Disturb mode.
  * **Window Inset Changes & Edge-to-Edge Enforcement:** Further refinement of how apps handle system bars (status bar, navigation bar) to ensure content extends edge-to-edge. `setNavigationBarColor` and `R.attr#navigationBarColor` are deprecated for gesture navigation.

**Deprecations:**

  * `setNavigationBarColor` and `R.attr#navigationBarColor` for gesture navigation.
  * Certain implicit intent behaviors are further restricted, potentially leading to errors if not updated to explicit intents where necessary.

**Code Snippet Example (Foreground Service Type):**

```java
// Android 15 and higher require specifying foreground service types in the manifest
// in AndroidManifest.xml:
// <service android:name=".MyForegroundService"
//          android:foregroundServiceType="mediaProcessing" />

// In your service class:
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.UPSIDE_DOWN_CAKE_MR0) { // API 35 (Android 15)
        Notification.Builder builder = new Notification.Builder(this, CHANNEL_ID)
                .setContentTitle("Processing Media")
                .setContentText("Your media is being processed.")
                .setSmallIcon(R.drawable.ic_notification);

        // Start the service as a foreground service with the specified type
        startForeground(NOTIFICATION_ID, builder.build(), Service.FOREGROUND_SERVICE_TYPE_MEDIA_PROCESSING);
    } else {
        // Fallback for older Android versions
        startForeground(NOTIFICATION_ID, new Notification.Builder(this, CHANNEL_ID).build());
    }
    return START_STICKY;
}
```

-----

## Android 14 (API 34)

**New Features & APIs:**

  * **Partial Photo and Video Access (Selected Photos Access):** Users can grant apps access to specific photos/videos instead of their entire library.
  * **Per-App Language Preferences:** Users can set preferred languages for individual apps, overriding system language.
  * **Credibility of implicit intents:** Stricter rules for implicit intents, they are only delivered to `exported` components.
  * **Foreground Service Types:** Apps must declare foreground service types in the manifest (e.g., `location`, `mediaPlayback`, `camera`).
  * **Health Connect Integration:** System-level integration for health data.
  * **Ultra HDR Image Format:** Support for richer images with high dynamic range.
  * **Predictive Back Gestures:** Enhanced system animations for the back gesture, allowing users to peek at the destination before completing the gesture.
  * **Improvements for Large Screens and Foldables:** Continued optimizations for various device form factors.

**Behavior Changes (for apps targeting API 34):**

  * **Foreground Service Types Required:** If your app uses foreground services, you *must* declare their types in the manifest. Failure to do so will result in a security exception.
  * **Runtime Registered Broadcast Receivers Export Behavior:** Context-registered receivers must explicitly declare `RECEIVER_EXPORTED` or `RECEIVER_NOT_EXPORTED`.
  * **Restrictions on Starting Activities from the Background:** Further limitations on when an app can launch activities from the background.
  * **BLUETOOTH\_CONNECT Permission Enforcement:** Stricter enforcement when calling `BluetoothAdapter.getProfileConnectionState()`.
  * **Implicit Intents Restrictions:** Implicit intents are only delivered to exported components. Mutable pending intents require specifying a component or package.
  * **`USE_FULL_SCREEN_INTENT` Permission:** Restricted to calling and alarm apps by default for new installs on Google Play.

**Deprecations:**

  * `TileService#startActivityAndCollapse(Intent)`: Use `TileService#startActivityAndCollapse(PendingIntent)` instead.
  * Older TLS versions might be phased out, TLS 1.3 is enabled by default.

**Code Snippet Example (Foreground Service Type in Manifest):**

```xml
<manifest ...>
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PLAYBACK" />
    <application ...>
        <service android:name=".MyMediaPlaybackService"
                 android:foregroundServiceType="mediaPlayback" />
    </application>
</manifest>
```

**Code Snippet Example (Partial Media Access):**

```java
// For apps targeting API 34+
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.UPSIDE_DOWN_CAKE) { // API 34
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_MEDIA_VISUAL_USER_SELECTED) == PackageManager.PERMISSION_GRANTED) {
        // App has partial access to selected photos
        // Proceed to use MediaStore APIs, which will respect the selection
    } else {
        // Request the new permission
        ActivityCompat.requestPermissions(this,
                new String[]{Manifest.permission.READ_MEDIA_VISUAL_USER_SELECTED},
                REQUEST_CODE_READ_MEDIA_VISUAL_USER_SELECTED);
    }
} else {
    // For older Android versions, request READ_EXTERNAL_STORAGE
    // or use Storage Access Framework
}
```

-----

## Android 13 (API 33)

**New Features & APIs:**

  * **Notification Runtime Permission:** Apps must request the `POST_NOTIFICATIONS` permission from the user to send notifications.
  * **Themed App Icons:** Icons can be tinted to match the user's chosen wallpaper colors.
  * **Per-App Language Preferences:** (Also introduced as a behavior change in API 33).
  * **Improved Clipboard Privacy:** System automatically redacts sensitive content from clipboard previews.
  * **Bluetooth LE Audio Support:** Improved audio quality and power efficiency for Bluetooth devices.
  * **MIDI 2.0 Support:** Enhanced support for MIDI hardware.
  * **HDR Video Capture:** Apps can capture and preview HDR video.
  * **Task Manager in Notification Drawer:** Users can stop foreground services directly from the notification shade.

**Behavior Changes (for apps targeting API 33):**

  * **Notification Permission:** This is a major change. If your app targets API 33+, it *must* request the `POST_NOTIFICATIONS` permission.
  * **Clipboard Content Redaction:** Be aware that sensitive user data copied to the clipboard might not be visible in clipboard previews.
  * **`BLUETOOTH_SCAN`, `BLUETOOTH_ADVERTISE`, `BLUETOOTH_CONNECT` permissions:** Granular Bluetooth permissions introduced. Old `BLUETOOTH` and `BLUETOOTH_ADMIN` are deprecated.
  * **Media Controls derived from `PlaybackState`:** Media controls in the system UI are now derived from `PlaybackState` actions, not `MediaStyle` notifications.
  * **WebView Theme Auto-Application:** `WebView` now automatically applies the app's theme based on `isLightTheme` attribute. `setForceDark()` is deprecated.

**Deprecations:**

  * `BluetoothAdapter#enable()` and `BluetoothAdapter#disable()`: These methods are deprecated and always return `false` for apps targeting API 33+. You should use `BluetoothManager` to request enabling/disabling Bluetooth via system dialog.
  * `android:sharedUserId`: Deprecated. Apps should migrate away from this.

**Code Snippet Example (Notification Runtime Permission):**

```java
// In AndroidManifest.xml:
<uses-permission android:name="android.permission.POST_NOTIFICATIONS"/>

// In your Activity/Fragment where you want to show notifications:
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) { // API 33
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.POST_NOTIFICATIONS) == PackageManager.PERMISSION_GRANTED) {
        // Notifications are allowed, proceed to show notification
        showNotification();
    } else if (shouldShowRequestPermissionRationale(Manifest.permission.POST_NOTIFICATIONS)) {
        // Explain why you need the permission
        // e.g., show an AlertDialog
    } else {
        // Request the permission
        ActivityCompat.requestPermissions(this,
                new String[]{Manifest.permission.POST_NOTIFICATIONS},
                NOTIFICATION_PERMISSION_REQUEST_CODE);
    }
} else {
    // For older Android versions, no runtime permission needed for notifications
    showNotification();
}
```

-----

## Android 12 (API 31)

**New Features & APIs:**

  * **Material You (Dynamic Color):** Major UI redesign allowing system colors to be extracted from the user's wallpaper and applied to the system UI and compatible apps.
  * **Splash Screens API:** Standardized splash screen for all apps, customizable by developers.
  * **Rich Content Insertion:** New APIs to allow apps to receive rich content (images, videos, audio) from any source, including clipboard, keyboard, and drag-and-drop.
  * **Rounded Corners & Camera Cutouts:** APIs to query for display cutout and rounded corner information to adjust UI.
  * **App Hiberantion:** If a user hasn't interacted with an app for several months, the system automatically revokes permissions and stops the app from running in the background.
  * **Mic & Camera Indicators:** System UI indicates when microphone or camera is in use.
  * **Mic & Camera Toggles:** Quick Settings tiles to disable microphone and camera access system-wide.
  * **Approximate Location:** Users can grant approximate location instead of precise.

**Behavior Changes (for apps targeting API 31):**

  * **Custom Notifications Redesign:** Custom notifications are now limited to a standard template for consistency. `Notification.Builder`'s `setCustomContentView()` and related methods will be templated.
  * **Android App Links Verification Changes:** Stricter verification for App Links, requiring `BROWSABLE` category and `https` scheme.
  * **Picture-in-Picture (PiP) Improvements:** Enhanced behavior and animations for PiP mode.
  * **Toast Redesign:** Toasts are limited to two lines of text and show the app icon. Custom toast views are deprecated.
  * **Foreground Service Launch Restrictions:** Apps cannot start foreground services while in the background *unless* they are excluded from battery optimization.
  * **PendingIntent Mutability:** `PendingIntent`s must explicitly declare their mutability (`FLAG_IMMUTABLE` or `FLAG_MUTABLE`).
  * **Notification Trampolines Restrictions:** Starting activities from notification trampolines is restricted.
  * **Bluetooth Permissions (`BLUETOOTH_SCAN`, `BLUETOOTH_ADVERTISE`, `BLUETOOTH_CONNECT`):** These specific permissions replace `BLUETOOTH` and `BLUETOOTH_ADMIN` for certain Bluetooth operations.
  * **App Hiberantion:** If your app's `targetSdk` is 31 or higher, it will be subject to app hibernation.

**Deprecations:**

  * Custom Toast views (`Toast.setView()`).
  * Older Bluetooth permissions (implicitly replaced by new granular ones).

**Code Snippet Example (Splash Screen - Theme-based):**

```xml
<style name="Theme.App.SplashScreen" parent="Theme.SplashScreen">
    <item name="windowSplashScreenBackground">@color/my_splash_background</item>
    <item name="windowSplashScreenAnimatedIcon">@drawable/my_splash_animation</item>
    <item name="windowSplashScreenAnimationDuration">1000</item>
    <item name="postSplashScreenTheme">@style/Theme.MyApp</item>
</style>

<application
    android:theme="@style/Theme.App.SplashScreen"
    ...>
    <activity
        android:name=".MainActivity"
        android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
</application>
```

**Code Snippet Example (Approximate Location):**

```java
// In AndroidManifest.xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

// In your Activity/Fragment:
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) { // API 31
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
        // Fine location is granted
    } else if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
        // Coarse location is granted
        // Your app should handle approximate location data
    } else {
        // Request both, user can choose fine or approximate
        ActivityCompat.requestPermissions(this,
                new String[]{Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION},
                LOCATION_PERMISSION_REQUEST_CODE);
    }
} else {
    // For older versions, request ACCESS_FINE_LOCATION if needed
}
```

-----

## Android 11 (API 30)

**New Features & APIs:**

  * **Scoped Storage Enforcement:** A major privacy change, restricting direct file system access. Apps primarily access their own app-specific directories, and use MediaStore or Storage Access Framework (SAF) for shared media and documents.
  * **One-Time Permissions:** Users can grant temporary access to location, microphone, and camera.
  * **Auto-Reset Permissions:** If an app isn't used for a few months, its granted runtime permissions are automatically revoked.
  * **Background Location Access Restriction:** Requires a separate runtime permission request for background location access.
  * **Chat Bubbles API:** System-level support for floating conversation bubbles.
  * **Media Controls:** Redesigned and persistent media controls in Quick Settings.
  * **Wireless Debugging:** Debug apps over Wi-Fi without a USB cable.
  * **Device Controls API:** Provides a standardized way to access and control connected devices (e.g., smart home) from the power menu.

**Behavior Changes (for apps targeting API 30):**

  * **Scoped Storage is Mandatory:** `requestLegacyExternalStorage` manifest flag is ignored. Apps must adapt to scoped storage principles.
  * **Permissions Changes:**
      * One-time permissions become standard.
      * Permissions auto-reset affects all apps (regardless of target SDK) but is most significant for API 30+ apps.
      * Repeatedly denying a permission implies "Don't ask again."
      * Background location now requires a separate runtime permission.
  * **Custom Toast View Deprecation:** Custom toast views (using `setView()`) are entirely deprecated. Only standard text toasts are reliably displayed.
  * **Foreground Service Launch Restrictions:** Further restrictions on launching foreground services from the background.

**Deprecations:**

  * `requestLegacyExternalStorage` manifest flag.
  * Custom Toast views.

**Code Snippet Example (Scoped Storage - Accessing MediaStore):**

```java
// To read images from MediaStore (for apps targeting API 30+)
fun getImagesFromGallery(context: Context): List<Uri> {
    val imageUris = mutableListOf<Uri>()
    val collection = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
        MediaStore.Images.Media.getContentUri(MediaStore.VOLUME_EXTERNAL)
    } else {
        MediaStore.Images.Media.EXTERNAL_CONTENT_URI
    }

    val projection = arrayOf(MediaStore.Images.Media._ID, MediaStore.Images.Media.DISPLAY_NAME)
    val sortOrder = "${MediaStore.Images.Media.DISPLAY_NAME} ASC"

    context.contentResolver.query(collection, projection, null, null, sortOrder)?.use { cursor ->
        val idColumn = cursor.getColumnIndexOrThrow(MediaStore.Images.Media._ID)
        while (cursor.moveToNext()) {
            val id = cursor.getLong(idColumn)
            val contentUri = ContentUris.withAppendedId(collection, id)
            imageUris.add(contentUri)
        }
    }
    return imageUris
}
```

-----

## Android 10 (API 29)

**New Features & APIs:**

  * **Dark Theme (System-wide):** Users can enable a system-wide dark theme, and apps should support it.
  * **Gesture Navigation:** Full-screen gesture navigation replaces the traditional three-button navigation bar. Apps need to extend content edge-to-edge.
  * **Scoped Storage (Introduced, not enforced for API 29):** New storage model for enhanced privacy. Apps can opt-out for API 29 using `requestLegacyExternalStorage`.
  * **Background Location Access Restrictions:** Apps cannot get location when in the background unless the user explicitly grants "Allow all the time" access.
  * **Restricted Access to Non-SDK Interfaces:** Further tightening of restrictions on using internal APIs.
  * **Improved Sharing Shortcuts:** New `SharingShortcuts` API to provide more relevant share targets.
  * **Settings Panels:** Allow apps to show settings from within their context without leaving the app.
  * **Improved Biometrics:** Unified biometric authentication dialog.
  * **Foldables Support:** Enhanced support for foldable devices.

**Behavior Changes (for apps targeting API 29):**

  * **Scoped Storage (Opt-out available):** Apps targeting API 29 still get legacy external storage by default, but it's *highly recommended* to start adapting to scoped storage and set `requestLegacyExternalStorage="true"` temporarily.
  * **Background Location Access:** If your app needs background location, you must declare the `ACCESS_BACKGROUND_LOCATION` permission in the manifest and request it at runtime.
  * **Restrictions on Starting Activities from Background:** Apps can no longer start activities from the background unless specific criteria are met (e.g., activity is already on top of the stack).
  * **MAC Address Randomization:** Device MAC address is randomized by default for Wi-Fi connections.
  * **Camera and Connectivity Callbacks:** Some camera and connectivity callbacks might be delivered on different threads.

**Deprecations:**

  * **`ACCESS_COARSE_LOCATION` and `ACCESS_FINE_LOCATION`:** While not fully deprecated, the introduction of `ACCESS_BACKGROUND_LOCATION` changes their behavior regarding background access.
  * **`android.preference` library:** Replaced by `androidx.preference`.
  * **Android Beam (NFC):** Deprecated.

**Code Snippet Example (Dark Theme - `values-night`):**

```xml
<style name="Theme.MyApp" parent="Theme.MaterialComponents.DayNight.NoActionBar">
    <item name="colorPrimary">@color/my_light_primary</item>
    <item name="colorSecondary">@color/my_light_secondary</item>
    <item name="android:textColor">@color/my_light_text</item>
</style>

<style name="Theme.MyApp" parent="Theme.MaterialComponents.DayNight.NoActionBar">
    <item name="colorPrimary">@color/my_dark_primary</item>
    <item name="colorSecondary">@color/my_dark_secondary</item>
    <item name="android:textColor">@color/my_dark_text</item>
</style>
```

-----

## Android 9 Pie (API 28)

**New Features & APIs:**

  * **Display Cutout (Notch) Support:** APIs to detect and manage display cutouts.
  * **Adaptive Icons:** Icons now include a background and foreground layer, allowing the system to apply various masks and visual effects.
  * **Notification Enhancements (MessagingStyle):** Improved support for chat apps, displaying avatars and inline replies for messages. Notification Channels are further refined.
  * **Multi-Camera API:** Allows access to streams from two or more physical cameras simultaneously.
  * **Indoor Positioning with Wi-Fi RTT (Round-Trip-Time):** Enables accurate indoor positioning.
  * **Performance Improvements:** ART optimizations for faster app startup and execution.
  * **Neural Networks API 1.1:** Expands supported operations.

**Behavior Changes (for apps targeting API 28):**

  * **Background Restrictions on Idle Apps:** Apps in the background have severe restrictions on network access and service execution.
  * **Power Management (App Standby Buckets):** The system dynamically prioritizes apps based on usage patterns into different "buckets" (Active, Working Set, Frequent, Rare, Never), influencing background resource limits.
  * **Camera Access Restrictions:** Apps running in the background cannot access the camera.
  * **Non-SDK Interface Restrictions:** Stronger enforcement of restrictions on using internal non-SDK APIs. Accessing these can cause crashes.
  * **Foreground Service Requirement for Location/Mic/Camera:** If an app accesses location, microphone, or camera while in the background, it must launch a foreground service with a visible notification.
  * **Build.SERIAL Deprecation:** `Build.SERIAL` is deprecated and always returns `UNKNOWN`. Use `Build.getSerial()` with `READ_PHONE_STATE` permission.

**Deprecations:**

  * `Build.SERIAL`
  * `HttpClient` (Apache) in Android 6.0 and higher (moved to `org.apache.http.legacy` library in later Android versions, but still deprecated).
  * Implicit `VIEW` intents for web URIs without a scheme/host.

**Code Snippet Example (Adaptive Icon - XML):**

```xml
<adaptive-icon xmlns:android="http://schemas.android.com/apk/res/android">
    <background android:drawable="@drawable/ic_launcher_background"/>
    <foreground android:drawable="@drawable/ic_launcher_foreground"/>
</adaptive-icon>
```

**Code Snippet Example (MessagingStyle Notification):**

```java
// Example Person object (Android P+)
Person sender = new Person.Builder()
        .setName("John Doe")
        .setIcon(Icon.createWithResource(context, R.drawable.profile_pic_john))
        .build();

// Example message (Android P+)
Notification.MessagingStyle.Message message = new Notification.MessagingStyle.Message(
        "Hey, how are you?", // text
        System.currentTimeMillis(), // timestamp
        sender // sender
);

// Build the notification
NotificationCompat.Builder builder = new NotificationCompat.Builder(context, CHANNEL_ID)
        .setStyle(new NotificationCompat.MessagingStyle(sender) // Current user for MessagingStyle
                .addMessage(message))
        .setSmallIcon(R.drawable.ic_notification)
        .setContentTitle("New Message")
        .setAutoCancel(true);

// Show the notification
notificationManager.notify(NOTIFICATION_ID, builder.build());
```

-----

## Android 8 Oreo (API 26 & 27)

**New Features & APIs:**

  * **Notification Channels (API 26):** A fundamental change to how notifications are managed. Users can control specific types of notifications from an app.
  * **Picture-in-Picture (PiP) Mode:** Support for floating video windows.
  * **Autofill Framework:** System-level support for autofill services.
  * **Background Execution Limits:** Significant restrictions on background services and implicit broadcasts.
  * **Adaptive Icons:** (Introduced initially, refined in Pie).
  * **Font in XML:** Custom fonts can be used directly in XML layouts.
  * **Downloadable Fonts:** Fonts can be requested from a provider instead of bundling.
  * **findViewById() returns Type Parameters:** Improved type safety for view lookups.
  * **Bluetooth 5 Support:** Faster speeds, longer range.

**Behavior Changes (for apps targeting API 26):**

  * **Notification Channels Mandatory:** If your app targets API 26+, you *must* implement notification channels for *all* your notifications. Notifications without a channel will not appear.
  * **Background Service Limitations:** Apps cannot create background services while in the background. Instead, use `startForegroundService()` to initiate a foreground service (which must then call `startForeground()` within 5 seconds).
  * **Implicit Broadcast Limitations:** Apps can no longer register broadcast receivers for most implicit broadcasts in their manifest. They must register them at runtime using `Context.registerReceiver()`.
  * **Increased Minimum `targetSdkVersion`:** Google Play requires new apps and updates to target at least API 26 (later raised).
  * **App Process State Changes:** More aggressive killing of background processes to save battery.

**Deprecations:**

  * **Implicit Broadcasts in Manifests:** Most implicitly broadcast intents.
  * **`IntentService`:** While not fully deprecated, its functionality is largely superseded by JobScheduler and WorkManager due to background execution limits. Consider migrating to `WorkManager`.

**Code Snippet Example (Notification Channel):**

```java
// Create a NotificationChannel (should be done once, e.g., in Application class or on app startup)
private void createNotificationChannel() {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { // API 26
        String channelId = "my_app_channel_id";
        CharSequence channelName = "My App Notifications";
        String channelDescription = "Notifications for important app events.";
        int importance = NotificationManager.IMPORTANCE_DEFAULT;

        NotificationChannel channel = new NotificationChannel(channelId, channelName, importance);
        channel.setDescription(channelDescription);

        // Register the channel with the system; you can't change the importance
        // or other notification behaviors after this
        NotificationManager notificationManager = getSystemService(NotificationManager.class);
        notificationManager.createNotificationChannel(channel);
    }
}

// To show a notification (using the channel)
private void showNotification() {
    String channelId = "my_app_channel_id"; // Must match the ID used when creating the channel
    NotificationCompat.Builder builder = new NotificationCompat.Builder(this, channelId)
            .setSmallIcon(R.drawable.ic_notification)
            .setContentTitle("My Notification")
            .setContentText("This is a notification from my app.")
            .setPriority(NotificationCompat.PRIORITY_DEFAULT) // Priority only matters on older Android versions
            .setAutoCancel(true);

    NotificationManagerCompat notificationManager = NotificationManagerCompat.from(this);
    notificationManager.notify(1, builder.build());
}
```

-----

## Android 7 Nougat (API 24 & 25)

**New Features & APIs:**

  * **Multi-Window Support:** Users can run two apps side-by-side or one above the other.
  * **Direct Reply Notifications:** Users can reply to messages directly from the notification shade.
  * **Bundled Notifications:** Multiple notifications from a single app can be grouped together.
  * **Improved Doze Mode:** Enhanced battery saving when the device is stationary and the screen is off.
  * **Data Saver Mode:** System-level setting to restrict background data usage.
  * **JIT/AOT Compilation:** Just-In-Time and Ahead-Of-Time compilation improvements for faster app installation and runtime performance.
  * **Vulkan API:** New 3D rendering API for high-performance graphics.

**Behavior Changes (for apps targeting API 24):**

  * **File URI Exposure:** Apps cannot expose `file://` URIs outside their package. They must use `FileProvider` and `content://` URIs. This is a critical security change.
  * **Direct Boot:** Apps can perform limited operations before the user unlocks the device for the first time.
  * **Doze and App Standby:** Stricter battery optimizations for apps in the background.
  * **Optimized Power Use for Location:** Better location batching for battery efficiency.
  * **Background Optimizations:** Further restrictions on background broadcasts and services.

**Deprecations:**

  * **`File://` URIs for inter-app sharing:** Replaced by `FileProvider` and `content://` URIs.

**Code Snippet Example (FileProvider for sharing):**

```xml
<application ...>
    <provider
        android:name="androidx.core.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/file_paths" />
    </provider>
</application>

<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="my_images" path="Android/data/com.example.myapp/files/Pictures" />
</paths>

File imagePath = new File(context.getFilesDir(), "images");
File newFile = new File(imagePath, "default_image.jpg");
Uri contentUri = FileProvider.getUriForFile(context,
        BuildConfig.APPLICATION_ID + ".fileprovider", newFile);

Intent shareIntent = new Intent(Intent.ACTION_SEND);
shareIntent.setType("image/jpeg");
shareIntent.putExtra(Intent.EXTRA_STREAM, contentUri);
shareIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); // Grant temp permission
context.startActivity(Intent.createChooser(shareIntent, "Share image via"));
```

-----

## Android 6 Marshmallow (API 23)

**New Features & APIs:**

  * **Runtime Permissions:** Users grant permissions at runtime, not just at install time.
  * **Doze and App Standby:** Major power-saving features. Doze puts the device into a deeper sleep state when stationary and screen is off. App Standby defers background activity for unused apps.
  * **Fingerprint Authentication API:** Standardized API for fingerprint scanners.
  * **Direct Share:** Apps can register to provide direct share targets in the system share sheet.
  * **Adoptable Storage:** SD cards can be "adopted" as internal storage.
  * **Chrome Custom Tabs:** Allows apps to launch a customizable Chrome browser tab.

**Behavior Changes (for apps targeting API 23):**

  * **Runtime Permissions Mandatory:** Apps must request dangerous permissions at runtime. This is the biggest change.
  * **Doze and App Standby Impacts:** Apps need to adapt to these power-saving modes for background tasks. Use `JobScheduler` and `GCMNetworkManager` (now Firebase JobDispatcher/WorkManager) for deferred tasks.
  * **`ACCESS_COARSE_LOCATION` and Wi-Fi/Bluetooth Scanning:** Requires `ACCESS_COARSE_LOCATION` for Wi-Fi and Bluetooth scanning.

**Deprecations:**

  * `Apache HTTP Client`: Deprecated. Use `HttpURLConnection` or third-party libraries like OkHttp.
  * Some `AlarmManager` methods (use `setAndAllowWhileIdle()` and `setExactAndAllowWhileIdle()` for alarms during Doze).

**Code Snippet Example (Runtime Permissions):**

```java
// In AndroidManifest.xml:
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

// In your Activity/Fragment:
private static final int PERMISSION_REQUEST_CODE = 123;

private void requestReadStoragePermission() {
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_EXTERNAL_STORAGE)
            != PackageManager.PERMISSION_GRANTED) {
        // Permission is not granted
        if (ActivityCompat.shouldShowRequestPermissionRationale(this,
                Manifest.permission.READ_EXTERNAL_STORAGE)) {
            // Show an explanation to the user
            // e.g., an AlertDialog explaining why the permission is needed
        } else {
            // No explanation needed, request the permission directly
            ActivityCompat.requestPermissions(this,
                    new String[]{Manifest.permission.READ_EXTERNAL_STORAGE},
                    PERMISSION_REQUEST_CODE);
        }
    } else {
        // Permission has already been granted
        readStorage();
    }
}

@Override
public void onRequestPermissionsResult(int requestCode,
                                       String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    if (requestCode == PERMISSION_REQUEST_CODE) {
        if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            // Permission granted
            readStorage();
        } else {
            // Permission denied
            // Handle the denial, e.g., disable related functionality
        }
    }
}
```

-----

## Android 5 Lollipop (API 21 & 22)

**New Features & APIs:**

  * **Material Design:** A comprehensive visual and motion design language.
  * **ART Runtime:** Replaced Dalvik as the default Android Runtime, bringing ahead-of-time (AOT) compilation for faster app startup and performance.
  * **Enhanced Notifications:** Lock screen notifications, heads-up notifications, and notification prioritization.
  * **JobScheduler API:** A flexible API for scheduling background tasks.
  * **Camera2 API:** Granular control over camera settings, supporting RAW image capture.
  * **Screen Pinning:** Allows users to lock a device to a single app.
  * **Android for Work:** Separate work profiles for business data.
  * **`RecyclerView` and `CardView`:** New UI widgets for efficient list and card displays.

**Behavior Changes (for apps targeting API 21):**

  * **ART Default Runtime:** Apps must be compatible with ART (most are, but some relying on JNI quirks might have issues).
  * **Power Efficiency (Project Volta):** More aggressive battery optimizations, encouraging use of `JobScheduler`.
  * **Accessibility Service Changes:** Restrictions on how accessibility services can draw over other apps.

**Deprecations:**

  * `AsyncTask` (not officially deprecated until later, but `JobScheduler` and later `WorkManager` are preferred for complex background tasks).
  * Explicitly mentioning `Dalvik` in code or build scripts.

**Code Snippet Example (JobScheduler - conceptual):**

```java
// Define your JobService
public class MyJobService extends JobService {
    @Override
    public boolean onStartJob(JobParameters params) {
        // Do your background work here (e.g., fetch data, upload)
        // This runs on the main thread, so offload heavy work to a background thread
        new Thread(() -> {
            // ... long-running task ...
            jobFinished(params, false); // false = don't reschedule if failed
        }).start();
        return true; // true = job is still running, needs to call jobFinished()
    }

    @Override
    public boolean onStopJob(JobParameters params) {
        return true; // true = reschedule if interrupted
    }
}

// Schedule the job (e.g., in an Activity/Service)
ComponentName serviceComponent = new ComponentName(context, MyJobService.class);
JobInfo.Builder builder = new JobInfo.Builder(JOB_ID, serviceComponent)
        .setRequiredNetworkType(JobInfo.NETWORK_TYPE_UNMETERED) // Only run on Wi-Fi
        .setPersisted(true); // Persist across reboots

JobScheduler jobScheduler = (JobScheduler) context.getSystemService(Context.JOB_SCHEDULER_SERVICE);
jobScheduler.schedule(builder.build());
```

-----

## Android 4.4 KitKat (API 19)

**New Features & APIs:**

  * **Immersive Mode:** Full-screen experience hiding system bars for media.
  * **Print Framework:** System-level printing capabilities.
  * **Storage Access Framework (SAF):** A new way for users to pick files from various content providers (cloud, local storage).
  * **SMS Provider:** Unified API for managing SMS/MMS.
  * **Sensors (Step Detector/Counter):** Hardware-level step detection for fitness apps.
  * **Transition Framework:** Enables scene transitions.

**Behavior Changes (for apps targeting API 19):**

  * **External Storage Access Changes:** Apps can only write to their own app-specific directory on external storage by default. Direct writing to arbitrary locations on external storage requires user interaction via SAF.
  * **AlarmManager Strictness:** `set()` and `setRepeating()` methods on `AlarmManager` are less precise for battery optimization. Use `setExact()` for precise alarms.

**Deprecations:**

  * `android.R.drawable.stat_sys_warning`: Deprecated as a system icon.
  * Some `AlarmManager` methods (use `setExact()`).

-----

**General Themes Across Android Releases:**

  * **Privacy and Security:** This is a continuous theme. Each release introduces stricter controls over data access, permissions, and background execution to protect user privacy and device security. This is often the most challenging area for developers to adapt to.
  * **Battery Optimization:** Google consistently introduces new power-saving features (Doze, App Standby, Background Execution Limits, foreground service requirements) to extend battery life. This requires developers to rethink how they perform background tasks.
  * **User Interface Modernization:** From Material Design to Material You, Android's UI has evolved to be more modern, consistent, and customizable.
  * **Performance Improvements:** Underlying runtime enhancements (ART, JIT/AOT), graphics APIs (Vulkan), and optimizations for memory usage.
  * **Large Screens/Foldables:** Increasing focus on adapting apps to various screen sizes and form factors.
  * **Non-SDK Interface Restrictions:** A gradual tightening of restrictions on developers using internal, non-public APIs, forcing them to use official APIs for long-term compatibility.

**Illustrations (Conceptual):**

  * **Notification Channels:** Imagine a settings screen for an app. Before Android 8, you'd have one toggle for "Notifications: On/Off." After Android 8, you'd see categories like "Promotional Notifications," "New Message Notifications," "Order Updates," and the user can toggle each category individually.
  * **Adaptive Icons:** Think of a generic app icon. On one phone, it might be a circle. On another, it's a square with rounded corners. On a third, it's a "squircle." The adaptive icon framework allows the system to apply these different masks to your single icon asset.
  * **Scoped Storage:** Picture your phone's storage. Before Android 11, apps could potentially see and modify files anywhere in the "Downloads" or "Pictures" folders. After Android 11, each app mostly sees its own sandbox, and to access shared media (like photos), it needs to go through the MediaStore, and for documents, it needs the user to explicitly select them via a file picker.
