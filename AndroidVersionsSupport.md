**Key Players:**

* **Android Studio Build:** Android Studio typically bundles a compatible AGP, Gradle, and JDK version. Upgrading Android Studio often prompts you to upgrade your project's AGP and Gradle versions.
* **Android Gradle Plugin (AGP)** is a Gradle plugin that Android Studio uses to build Android apps. AGP versions are tightly coupled with specific Gradle versions.
* **Gradle** is the build automation system. Different AGP versions require minimum and maximum Gradle versions.
* **Java (JDK)** is required to *run* Gradle and compile your Android project. Specific Gradle and AGP versions have minimum JDK requirements.
* **Android Version / API Level:** Your app's `targetSdk` and `compileSdk` in your `build.gradle` file correspond to an Android API level. While you can often use a newer AGP/Gradle/Java setup to target older Android versions, using older AGP/Gradle with newer Android API levels can lead to issues.

**General Compatibility Guide (Approximate, always refer to official Android Developer documentation for precise and up-to-date information):**

| Android API Level (Version) | Recommended AGP Version | Recommended Gradle Version | Required Java (JDK) to run Gradle | Notes/Typical Android Studio Version |
| :-------------------------- | :---------------------- | :------------------------- | :-------------------------------- | :----------------------------------- |
| **API 35 (Android 16 - Preview)** | 8.4.x - 8.x.x | 8.8+ | Java 17+ | Android Studio Koala / Preview |
| **API 34 (Android 15)** | 8.3.x - 8.4.x | 8.4+ | Java 17+ | Android Studio Iguana (2023.2.1) |
| **API 33 (Android 14)** | 8.x.x (e.g., 8.1.x, 8.2.x) | 8.0+ | Java 17+ | Android Studio Hedgehog (2023.1.1), Jellyfish (2023.3.1) |
| **API 32 (Android 13)** | 7.4.x - 8.0.x | 7.5+ | Java 11 (minimum for AGP 7.0+) | Android Studio Electric Eel (2022.1.1) |
| **API 31 (Android 12)** | 7.x.x (e.g., 7.1.x, 7.2.x, 7.3.x) | 7.2+ | Java 11 (minimum for AGP 7.0+) | Android Studio Dolphin (2021.3.1), Flamingo (2022.2.1) |
| **API 30 (Android 11)** | 4.x.x - 7.0.x | 6.7.1+ (for AGP 4.2), 7.0.2+ (for AGP 7.0) | Java 8/11 | Android Studio Arctic Fox (2020.3.1) |
| **API 29 (Android 10)** | 3.6.x - 4.1.x | 5.6.4+ (for AGP 3.6), 6.5+ (for AGP 4.1) | Java 8 | Android Studio 3.6 - 4.1 |
| **API 28 (Android 9 Pie)** | 3.2.x - 3.5.x | 4.6+ (for AGP 3.2), 5.4.1+ (for AGP 3.5) | Java 8 | Android Studio 3.2 - 3.5 |
| **API 21-27 (Lollipop - Oreo)** | 3.0.x - 3.1.x | 4.1+ (for AGP 3.0), 4.4+ (for AGP 3.1) | Java 8 | Android Studio 3.0 - 3.1 |

**Important Considerations:**

* **Minimum vs. Recommended:** The "Recommended" versions are what you typically use for new projects or when updating. You might be able to use slightly older versions, but it's generally not advised due to potential bugs or missing features.
* **Kotlin Gradle Plugin (KGP):** If you're using Kotlin in your Android project, the Kotlin Gradle Plugin also has its own compatibility requirements with Gradle and AGP. The Kotlin documentation often provides a detailed compatibility matrix for KGP, Gradle, and AGP.
* **`compileSdk` vs. `targetSdk`:**
    * `compileSdk`: The API level your app is compiled against. You should always compile with the latest stable API level.
    * `targetSdk`: The API level your app is designed to run on. This should ideally be the latest API level, but can be lower to support older devices.
* **Java Toolchains:** Gradle and AGP support Java toolchains, which allow you to specify the JDK version used for compilation and execution, independent of the JDK used to run Gradle itself. This is a recommended practice for consistency.
* **Android Studio Bundled JDK:** Android Studio often bundles its own JDK, which it uses to run Gradle by default. This generally simplifies setup as you don't always need to manage a separate `JAVA_HOME` for Gradle.

**How to check your project's versions:**

* **`build.gradle` (module level):**
    * `compileSdk`: `compileSdk = 34` (for example)
    * `targetSdk`: `targetSdk = 34`
    * AGP: `id("com.android.application") version "8.3.0"` (for example)
* **`gradle/wrapper/gradle-wrapper.properties`:**
    * Gradle distribution URL: `distributionUrl=https\://services.gradle.org/distributions/gradle-8.4-bin.zip` (indicates Gradle version 8.4)
* **Android Studio:**
    * **Android Studio Version:** Help > About (or Android Studio > About on macOS).
    * **JDK for Gradle:** File > Project Structure > SDK Location > Gradle Settings.
