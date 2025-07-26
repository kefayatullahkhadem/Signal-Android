# Android API Setup Guide - Step by Step

This guide documents the exact steps to configure Signal Android app to use a custom Signal server.

## Prerequisites

Before starting, you need:
1. Your custom Signal server running and accessible
2. Server IP addresses
3. Server public parameters from your Signal server configuration
4. Android Studio or command line build tools

## Step 1: Gather Server Information

### Required Information:
- **Server URLs**: API server, CDN server, WebSocket server
- **Server IP Address**: The IP address(es) of your server(s)
- **Server Parameters**: Public keys and certificates from your server config

### Example (from this setup):
```
API Server: https://api.testers.fun
CDN Server: https://signal.testers.fun
WebSocket: wss://ws.testers.fun
IP Address: 84.247.188.3
```

## Step 2: Update Server URLs

### File: `app/build.gradle.kts`

Locate the `defaultConfig` section (around line 190-240) and update these fields:

#### 2.1 Main Server URLs
```kotlin
// Original
buildConfigField("String", "SIGNAL_URL", "\"https://chat.signal.org\"")
buildConfigField("String", "STORAGE_URL", "\"https://storage.signal.org\"")
buildConfigField("String", "SIGNAL_CDN_URL", "\"https://cdn.signal.org\"")
buildConfigField("String", "SIGNAL_CDN2_URL", "\"https://cdn2.signal.org\"")
buildConfigField("String", "SIGNAL_CDN3_URL", "\"https://cdn3.signal.org\"")

// Changed to
buildConfigField("String", "SIGNAL_URL", "\"https://api.testers.fun\"")
buildConfigField("String", "STORAGE_URL", "\"https://api.testers.fun\"")
buildConfigField("String", "SIGNAL_CDN_URL", "\"https://signal.testers.fun\"")
buildConfigField("String", "SIGNAL_CDN2_URL", "\"https://signal.testers.fun\"")
buildConfigField("String", "SIGNAL_CDN3_URL", "\"https://signal.testers.fun\"")
```

#### 2.2 Additional Service URLs
```kotlin
// Changed these to point to your API server temporarily
buildConfigField("String", "SIGNAL_CDSI_URL", "\"https://api.testers.fun\"")
buildConfigField("String", "SIGNAL_SERVICE_STATUS_URL", "\"api.testers.fun\"")
buildConfigField("String", "SIGNAL_SVR2_URL", "\"https://api.testers.fun\"")
buildConfigField("String", "SIGNAL_SFU_URL", "\"https://api.testers.fun\"")
buildConfigField("String", "SIGNAL_STAGING_SFU_URL", "\"https://api.testers.fun\"")
```

#### 2.3 Content Proxy and Other URLs
```kotlin
buildConfigField("String", "CONTENT_PROXY_HOST", "\"api.testers.fun\"")
buildConfigField("String", "BADGE_STATIC_ROOT", "\"https://api.testers.fun/static/badges/\"")
buildConfigField("String", "SIGNAL_CAPTCHA_URL", "\"https://api.testers.fun/registration/generate.html\"")
buildConfigField("String", "RECAPTCHA_PROOF_URL", "\"https://api.testers.fun/challenge/generate.html\"")
```

#### 2.4 Internal URLs Array
```kotlin
buildConfigField("String[]", "SIGNAL_SFU_INTERNAL_URLS", 
    "new String[]{\"https://api.testers.fun\", \"https://api.testers.fun\", \"https://api.testers.fun\"}")
```

## Step 3: Update Server IP Addresses

### File: `app/static-ips.gradle.kts`

Replace ALL IP addresses with your server's IP:

```kotlin
// Original
rootProject.extra["service_ips"] = """new String[]{"13.248.212.111","76.223.92.165"}"""
rootProject.extra["storage_ips"] = """new String[]{"142.250.72.115"}"""
rootProject.extra["cdn_ips"] = """new String[]{"18.238.49.106","18.238.49.6","18.238.49.66","18.238.49.90"}"""
// ... etc

// Changed to (all pointing to single server)
rootProject.extra["service_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["storage_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["cdn_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["cdn2_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["cdn3_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["sfu_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["content_proxy_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["svr2_ips"] = """new String[]{"84.247.188.3"}"""
rootProject.extra["cdsi_ips"] = """new String[]{"84.247.188.3"}"""
```

## Step 4: Update Cryptographic Parameters

### File: `app/build.gradle.kts` (around line 220)

#### 4.1 Trust Root Certificate
```kotlin
// Original
buildConfigField("String", "UNIDENTIFIED_SENDER_TRUST_ROOT", 
    "\"BXu6QIKVz5MA8gstzfOgRQGqyLqOwNKHL6INkv3IHWMF\"")

// Changed to (from your server config)
buildConfigField("String", "UNIDENTIFIED_SENDER_TRUST_ROOT", "\"CgIIAQ==\"")
```

#### 4.2 Server Public Parameters
```kotlin
// Update with your server's zkgroup public parameters
buildConfigField("String", "ZKGROUP_SERVER_PUBLIC_PARAMS", 
    "\"ABCDEFGHIJKLMNOPQRSTUVWXYZ/0123456789+abcdefghijklmnopqrstuvwxyz...\"")

buildConfigField("String", "GENERIC_SERVER_PUBLIC_PARAMS", 
    "\"ABCDEFGHIJKLMNOPQRSTUVWXYZ/0123456789+abcdefghijklmnopqrstuvwxyz...\"")

buildConfigField("String", "BACKUP_SERVER_PUBLIC_PARAMS", 
    "\"ABCDEFGHIJKLMNOPQRSTUVWXYZ/0123456789+abcdefghijklmnopqrstuvwxyz...\"")
```

## Step 5: Configure Environment Settings

### File: `app/build.gradle.kts` (around line 230)

#### 5.1 Set Network Environment
```kotlin
// For testing/development
buildConfigField("org.signal.libsignal.net.Network.Environment", "LIBSIGNAL_NET_ENV", 
    "org.signal.libsignal.net.Network.Environment.STAGING")

// For production
buildConfigField("org.signal.libsignal.net.Network.Environment", "LIBSIGNAL_NET_ENV", 
    "org.signal.libsignal.net.Network.Environment.PRODUCTION")
```

#### 5.2 Enable Debug Logging (optional)
```kotlin
// Original
buildConfigField("int", "LIBSIGNAL_LOG_LEVEL", 
    "org.signal.libsignal.protocol.logging.SignalProtocolLogger.INFO")

// Changed to
buildConfigField("int", "LIBSIGNAL_LOG_LEVEL", 
    "org.signal.libsignal.protocol.logging.SignalProtocolLogger.DEBUG")
```

## Step 6: Build the App

### Clean and Build
```bash
# Clean previous builds
./gradlew clean

# Build staging debug variant (recommended for testing)
./gradlew app:assemblePlayStagingDebug

# Or build production debug
./gradlew app:assemblePlayProdDebug
```

### Find the APK
The APK will be in:
- Staging: `app/build/outputs/apk/playStaging/debug/`
- Production: `app/build/outputs/apk/playProd/debug/`

## Step 7: Install and Test

```bash
# Install via ADB
adb install app/build/outputs/apk/playStaging/debug/app-play-staging-debug-*.apk

# Monitor logs
adb logcat | grep -i signal
```

## Summary of Changes

1. **app/build.gradle.kts**:
   - Updated all Signal server URLs to custom server
   - Changed trust root certificate
   - Updated server public parameters
   - Set environment to STAGING
   - Enabled debug logging

2. **app/static-ips.gradle.kts**:
   - Replaced all IP addresses with custom server IP

## Important Notes

### Server Parameters
The server parameters (zkgroup, generic, backup) must match your server configuration. These are generated when you set up your Signal server and can be found in your server's configuration files.

### Finding Server Parameters
On your Signal server, check:
- `/path/to/signal-server/config/config.yml`
- `/path/to/signal-server/config/secrets.yml`
- Server logs during startup

### Environment Selection
- Use `STAGING` for development/testing
- Use `PRODUCTION` only for production deployments
- Staging environment has more relaxed security for easier testing

### Testing Checklist
1. ✅ Registration works
2. ✅ Can send/receive messages
3. ✅ Attachments upload/download
4. ✅ WebSocket stays connected
5. ✅ No certificate errors

## Troubleshooting

### Common Issues:
1. **Registration fails**: Check server public parameters
2. **Connection errors**: Verify IP addresses and URLs
3. **Certificate errors**: Update trust root certificate
4. **WebSocket disconnects**: Check WebSocket URL configuration

### Debug Commands:
```bash
# Check network connections
adb shell netstat -an | grep 84.247.188.3

# View Signal logs
adb logcat -s Signal

# Clear app data
adb shell pm clear org.thoughtcrime.securesms
```

## Future Updates

When updating to a new server:
1. Follow steps 1-4 with new server information
2. Clean build directory: `./gradlew clean`
3. Rebuild and test thoroughly
4. Update this guide with any new requirements