# API Setup Guide for Custom Signal Server

This document lists all the APIs, server URLs, certificates, and configurations that need to be changed to use a custom Signal server with the Signal Android app.

## Server URLs and Endpoints

### Primary Server URLs (app/build.gradle.kts)

#### Production Environment
- **SIGNAL_URL**: `https://chat.signal.org` → Change to your server URL
- **STORAGE_URL**: `https://storage.signal.org` → Change to your storage server URL
- **SIGNAL_CDN_URL**: `https://cdn.signal.org` → Change to your CDN URL
- **SIGNAL_CDN2_URL**: `https://cdn2.signal.org` → Change to your CDN2 URL
- **SIGNAL_CDN3_URL**: `https://cdn3.signal.org` → Change to your CDN3 URL
- **SIGNAL_CDSI_URL**: `https://cdsi.signal.org` → Change to your CDSI server URL
- **SIGNAL_SERVICE_STATUS_URL**: `uptime.signal.org` → Change to your status URL
- **SIGNAL_SVR2_URL**: `https://svr2.signal.org` → Change to your SVR2 server URL
- **SIGNAL_SFU_URL**: `https://sfu.voip.signal.org` → Change to your SFU URL for calls
- **SIGNAL_STAGING_SFU_URL**: `https://sfu.staging.voip.signal.org` → Change to your staging SFU URL

#### Staging Environment (if using staging)
- **SIGNAL_URL**: `https://chat.staging.signal.org`
- **STORAGE_URL**: `https://storage-staging.signal.org`
- **SIGNAL_CDN_URL**: `https://cdn-staging.signal.org`
- **SIGNAL_CDN2_URL**: `https://cdn2-staging.signal.org`
- **SIGNAL_CDN3_URL**: `https://cdn3-staging.signal.org`
- **SIGNAL_CDSI_URL**: `https://cdsi.staging.signal.org`
- **SIGNAL_SVR2_URL**: `https://svr2.staging.signal.org`

### Additional URLs
- **CONTENT_PROXY_HOST**: `contentproxy.signal.org` → Change to your content proxy
- **CONTENT_PROXY_PORT**: `443` → Change if using different port
- **BADGE_STATIC_ROOT**: `https://updates2.signal.org/static/badges/` → Change to your badge server
- **APK_UPDATE_MANIFEST_URL**: `https://updates.signal.org/android/latest.json` → Change for self-updates

### Captcha URLs
- **SIGNAL_CAPTCHA_URL**: `https://signalcaptchas.org/registration/generate.html`
- **RECAPTCHA_PROOF_URL**: `https://signalcaptchas.org/challenge/generate.html`

### Internal SFU URLs
- **SIGNAL_SFU_INTERNAL_URLS**: Array containing test/staging/development SFU URLs

## Server IP Addresses (app/static-ips.gradle.kts)

These hardcoded IPs need to be changed to your server's IPs:

- **service_ips**: `["13.248.212.111","76.223.92.165"]`
- **storage_ips**: `["142.250.72.115"]`
- **cdn_ips**: `["18.238.49.106","18.238.49.6","18.238.49.66","18.238.49.90"]`
- **cdn2_ips**: `["104.18.10.47","104.18.11.47"]`
- **cdn3_ips**: `["104.18.10.47","104.18.11.47"]`
- **sfu_ips**: `["34.117.136.13"]`
- **content_proxy_ips**: `["107.178.250.75"]`
- **svr2_ips**: `["20.9.45.98"]`
- **cdsi_ips**: `["40.122.45.194"]`

## Cryptographic Keys and Parameters

### Trust Roots and Public Keys
- **UNIDENTIFIED_SENDER_TRUST_ROOT**: Base64 encoded trust root certificate
  - Production: `BXu6QIKVz5MA8gstzfOgRQGqyLqOwNKHL6INkv3IHWMF`
  - Staging: `BbqY1DzohE4NUZoVF+L18oUPrK3kILllLEJh2UnPSsEx`

### Server Public Parameters (Base64 encoded)
- **ZKGROUP_SERVER_PUBLIC_PARAMS**: Server's zkgroup public parameters (very long base64 string)
- **GENERIC_SERVER_PUBLIC_PARAMS**: Generic server public parameters
- **BACKUP_SERVER_PUBLIC_PARAMS**: Backup server public parameters

### SVR2 Enclaves (SGX attestation)
- **SVR2_MRENCLAVE**: Current enclave measurement
- **SVR2_MRENCLAVE_LEGACY**: Legacy enclave measurement
- **SVR2_MRENCLAVE_LEGACY_LEGACY**: Older legacy enclave measurement

## Environment Configuration

### Network Environment
- **LIBSIGNAL_NET_ENV**: 
  - Production: `org.signal.libsignal.net.Network.Environment.PRODUCTION`
  - Staging: `org.signal.libsignal.net.Network.Environment.STAGING`

### User Agent
- **SIGNAL_AGENT**: `OWA` (Open Whisper Android) - May need to change for server compatibility

## Third-Party Services

### Payment Processing
- **STRIPE_BASE_URL**: `https://api.stripe.com/v1`
- **STRIPE_PUBLISHABLE_KEY**: 
  - Production: `pk_live_6cmGZopuTsV8novGgJJW9JpC00vLIgtQ1D`
  - Staging: `pk_test_sngOd8FnXNkpce9nPXawKrJD00kIDngZkD`

### Other APIs
- **GIPHY_API_KEY**: `3o6ZsYH6U6Eri53TXy` - For GIF search
- **Google Maps API Key**: `AIzaSyCSx9xea86GwDKGznCAULE9Y5a8b-TfN9U` - For location sharing

## MobileCoin Configuration

- **MOBILE_COIN_ENVIRONMENT**: `mainnet` or `testnet`
- Additional MobileCoin certificates in:
  - `app/src/main/java/org/thoughtcrime/securesms/payments/MobileCoinMainNetConfig.java`
  - `app/src/main/java/org/thoughtcrime/securesms/payments/MobileCoinTestNetConfig.java`

## Implementation Steps

1. **Modify build.gradle.kts**:
   - Update all URL buildConfigField entries
   - Update all public parameter strings
   - Update trust roots and enclaves

2. **Modify static-ips.gradle.kts**:
   - Replace all IP arrays with your server IPs

3. **Certificate Handling**:
   - Generate new trust root certificates
   - Generate new server public parameters using Signal server tools
   - Update SGX enclave measurements if using SGX

4. **Environment Selection**:
   - Choose appropriate LIBSIGNAL_NET_ENV
   - Set proper build variant (prod/staging)

5. **Disable Features (Optional)**:
   - Remove payment integration if not needed
   - Remove GIF search if not using Giphy
   - Remove maps if not using location sharing

## Important Notes

1. **Server Compatibility**: Your custom server must implement the Signal Server protocol correctly
2. **Certificate Pinning**: The app uses certificate pinning, so proper certificates are crucial
3. **Build Variants**: Different configurations for play/website/nightly distributions
4. **Testing**: Use staging environment first before production
5. **Updates**: Consider how app updates will work with custom APK_UPDATE_MANIFEST_URL

## Security Considerations

- Never commit private keys or sensitive parameters to version control
- Ensure all certificates are properly generated and secured
- Test thoroughly in staging before production deployment
- Monitor server logs for any connection issues