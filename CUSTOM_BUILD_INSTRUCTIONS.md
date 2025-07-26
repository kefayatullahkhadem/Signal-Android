# Custom Signal Server Build Instructions

## Configuration Completed ✅

I've successfully configured your Signal Android app to work with your custom server at `testers.fun`. Here's what was changed:

### 1. Server URLs Updated
- **API Server**: `https://api.testers.fun`
- **Storage Server**: `https://api.testers.fun`
- **CDN**: `https://signal.testers.fun`
- **WebSocket**: Handled by API server
- All other services point to your API server temporarily

### 2. IP Addresses Updated
All services now point to your server IP: `84.247.188.3`

### 3. Environment Settings
- Set to **STAGING** mode for easier testing
- Debug logging enabled
- Using your server's public parameters

## Build Instructions

### Prerequisites
1. **Android Studio** installed
2. **Git** repository cloned
3. **Android SDK** with required build tools

### Building the APK

1. **Open Terminal** in the Signal-Android directory

2. **Clean previous builds**:
   ```bash
   ./gradlew clean
   ```

3. **Build Debug APK** (recommended for testing):
   ```bash
   ./gradlew app:assemblePlayStagingDebug
   ```

4. **Find your APK**:
   ```
   app/build/outputs/apk/playStaging/debug/app-play-staging-debug-*.apk
   ```

### Alternative Build Variants

For different testing scenarios:

- **Staging Debug** (Recommended):
  ```bash
  ./gradlew app:assemblePlayStagingDebug
  ```

- **Production Debug** (if needed):
  ```bash
  ./gradlew app:assemblePlayProdDebug
  ```

## Testing Steps

1. **Install the APK** on your Android device:
   ```bash
   adb install app/build/outputs/apk/playStaging/debug/app-play-staging-debug-*.apk
   ```

2. **Registration**:
   - Open the app
   - Enter a phone number
   - Complete registration (captcha disabled for testing)

3. **Basic Messaging**:
   - Add contacts
   - Send text messages
   - Test message delivery

## Important Notes

### ⚠️ Placeholder Parameters
The server public parameters (zkgroup, generic, backup) are currently using placeholder values. For production use, you need to:

1. **Generate real parameters** from your Signal server
2. **Replace the placeholder values** in `app/build.gradle.kts`
3. **Update the trust root certificate** with your server's certificate

### Current Limitations
- Voice/Video calls won't work (SFU not configured)
- Contact discovery limited (CDSI not configured)
- Backup/Restore may not work properly
- Some features depend on proper server parameters

## Troubleshooting

### If registration fails:
1. Check server logs for errors
2. Verify all services are running
3. Check network connectivity to `api.testers.fun`

### If messages don't send:
1. Verify WebSocket connection at `wss://ws.testers.fun`
2. Check server-side message queue
3. Review debug logs in Android Studio

### Debug Logging
To view detailed logs:
```bash
adb logcat | grep -i signal
```

## Next Steps

1. **Test basic messaging** first
2. **Monitor server logs** for any errors
3. **Generate proper server parameters** for production
4. **Configure additional services** as needed:
   - CDSI for contact discovery
   - SFU for calls
   - SVR2 for secure value recovery

## Quick Reference

- **Server**: `api.testers.fun` (84.247.188.3)
- **CDN**: `signal.testers.fun` (84.247.188.3)
- **Environment**: STAGING
- **Build Variant**: `playStaging`
- **APK Location**: `app/build/outputs/apk/playStaging/debug/`

Good luck with your testing! 🚀