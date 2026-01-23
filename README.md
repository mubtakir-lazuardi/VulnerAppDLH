# VulnerApp for Droid-LLM-Hunter

**VulnerAppDLH** is a deliberately insecure Android application designed as a **training ground** and **testbed** for the [Droid-LLM-Hunter](https://github.com/roomkangali/droid-llm-hunter) security analysis tool. It implements a wide range of common Android vulnerabilities based on the **OWASP Mobile Top 10** and **MASVS** standards, serving as a perfect target to demonstrate DLH's detection capabilities.

<div align="center">

<p>
    <img src="https://img.shields.io/badge/Android-Kotlin-green.svg" alt="Android Kotlin">
    <img src="https://img.shields.io/badge/Security-Vulnerable-red.svg" alt="Vulnerable">
    <img src="https://img.shields.io/badge/Tool-Droid_LLM_Hunter-blue.svg" alt="Tool">
</p>

</div>

<p align="center">
  <img src="vulnappdlh.png" width="350">
</p>

---

## 📋 Table of Contents

- [💀 Implemented Vulnerabilities](#-implemented-vulnerabilities)
- [🛠️ Build & Installation](#️-build--installation)
- [🚀 Scanning with Droid-LLM-Hunter](#-scanning-with-droid-llm-hunter)
- [🗺️ Development Roadmap](#%EF%B8%8F-development-roadmap)
- [📝 Technical Details](#-technical-details)

---

## 💀 Implemented Vulnerabilities

VulnerApp contains the following intentional security flaws, each isolated in its own Activity for clear demonstration:

### 1. Hardcoded Secrets & Insecure Storage
*   **Component:** `SecretsActivity`
*   **Vulnerability:**
    *   Hardcoded AWS Access Keys and API Tokens in source code.
    *   Storing sensitive user credentials (plaintext) in `SharedPreferences`.
    *   Logging sensitive tokens to `Logcat`.
*   **MASVS:** MASVS-STORAGE-1, MASVS-CODE-2

### 2. SQL Injection
*   **Component:** `SQLInjectionActivity`
*   **Vulnerability:**
    *   Constructs SQL queries using raw string concatenation with user input.
    *   Allows attackers to manipulate database queries (e.g., `' OR '1'='1`).
*   **MASVS:** MASVS-CODE-4

### 3. WebView Exploits
*   **Component:** `WebViewActivity`
*   **Vulnerability:**
    *   **Cross-Site Scripting (XSS):** JavaScript enabled (`setJavaScriptEnabled(true)`).
    *   **Local File Access:** Access to file system allowed (`setAllowUniversalAccessFromFileURLs(true)`).
    *   **JavascriptInterface:** Exposes sensitive native methods (`getSecrets`) to web content.
    *   **Deep Link:** Vulnerable to loading malicious URLs via `dlh://webview`.
*   **MASVS:** MASVS-PLATFORM-2, MASVS-CODE-4

### 4. GraphQL Injection
*   **Component:** `GraphQLInjectionActivity`
*   **Vulnerability:**
    *   Constructs GraphQL queries by concatenating user strings directly into the query body.
    *   Susceptible to query injection/mutation attacks.
*   **MASVS:** MASVS-CODE-4

### 5. Insecure File Operations
*   **Component:** `InsecureFileActivity`
*   **Vulnerability:**
    *   **World-Readable Files:** Creates files with `MODE_WORLD_READABLE` (deprecated but dangerous).
    *   **Path Traversal:** allows reading arbitrary files via unchecked user input filenames.
*   **MASVS:** MASVS-STORAGE-2

### 6. Weak Cryptography & Auth Bypass
*   **Component:** `CryptoActivity`
*   **Vulnerability:**
    *   **Weak Randomness:** Uses `java.util.Random` for Session Tokens (predictable).
    *   **Auth Bypass:** Simulates biometric authentication that sets a simple boolean flag in memory.
*   **MASVS:** MASVS-CRYPTO-1, MASVS-AUTH-1

### 7. IPC & Exported Components
*   **Component:** `UnprotectedExportedActivity`, `SpoofableReceiver`
*   **Vulnerability:**
    *   **Deep Link Hijacking:** `vulnerapp://*` accepts any host, prone to intent interception.
    *   **Intent Spoofing:** Exported Broadcast Receiver accepts actions from any app without permission checks.
*   **MASVS:** MASVS-PLATFORM-1

---

## 🛠️ Build & Installation

You need **Android Studio** or **Gradle** installed.

### 1. Build the APK
Use the wrapper script or local gradle to build the debug APK:

```bash
git clone https://github.com/roomkangali/VulnerAppDLH.git
cd VulnerAppDLH
./gradlew assembleDebug
```

### 2. Locate the APK
The output APK will be located at:
`app/build/outputs/apk/debug/app-debug.apk`

### 3. Install on Device/Emulator
```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```

---

## 🚀 Scanning with Droid-LLM-Hunter

To analyze this app using Droid-LLM-Hunter (DLH) and verify the vulnerabilities:

### 1. Preparation
Ensure you are in the root directory of `droid-llm-hunter`.

### 2. Run the Scan
Execute the scanner against the built APK:

```bash
python3 dlh.py scan VulnerAppDLH.apk
```

### 3. View Results
DLH will generate a report in the `output/` directory (e.g., `VulnerAppDLH.apk_results.json`). You should see findings for:
*   `Hardcoded Secrets`
*   `SQL Injection`
*   `GraphQL Injection`
*   `WebView XSS`
*   `Insecure Storage`
*   etc.

---

## 🗺️ Development Roadmap

### Future Updates
*   **Dynamic Rule Updates:** This application will be updated with new vulnerability scenarios as new detection rules are added to Droid-LLM-Hunter (DLH).

### Deferred/High-Risk Features
*   **Jetpack Compose Security (`jetpack_compose_security`)** 🔴
    *   **Status:** High Effort / High Risk (Deferred)
    *   **Reasoning:** The current project relies on the standard XML ("Legacy View") system. Migrating or adding Jetpack Compose support requires:
        *   Significant changes to `build.gradle` (enabling `compose true`).
        *   Adding heavy dependencies (Compose UI, Material3, Compiler Extensions).
        
---

## 📝 Technical Details

*   **Package Name:** `com.dlh.vulnerapp`
*   **Min SDK:** 24
*   **Target SDK:** 34
*   **Language:** Kotlin
*   **Architecture:** MVVM (Simplified for demonstration)


> **Disclaimer:** This application is **INTENTIONALLY VULNERABLE**. Do not install it on a production device containing sensitive data. Do not use portions of this code in real applications.
