# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Signal Android Development

Signal Android is a secure messaging application built with Android native technologies. This is the official open-source Android client for the Signal messenger.

## Development Commands

### Building and Testing
- `./gradlew clean` - Clean all build outputs
- `./gradlew qa` - Run quality assurance checks (tests, lint, build) - **Run this before pushing**
- `./gradlew format` - Format code using ktlint
- `./gradlew app:assemblePlayProdRelease` - Build production release APK
- `./gradlew app:assemblePlayProdDebug` - Build debug APK
- `./gradlew app:testPlayProdReleaseUnitTest` - Run unit tests
- `./gradlew app:lintPlayProdRelease` - Run lint checks
- `./gradlew ktlintCheck` - Check code formatting
- `./gradlew ktlintFormat` - Auto-format code

### Build Variants
The project uses multiple build variants:
- **Flavors**: `play` (Google Play), `website` (direct download), `nightly` (development builds)
- **Environments**: `prod` (production), `staging`, `backup`
- **Build Types**: `debug`, `release`, `instrumentation`, `spinner`, `perf`, `benchmark`, `canary`

Common development variants:
- `playProdDebug` - Standard development build
- `playProdRelease` - Production release build
- `playStagingDebug` - Staging environment for testing

## Project Architecture

### Multi-Module Structure
- **app** (`Signal-Android`) - Main application module
- **libsignal-service** - Protocol and service layer for Signal communication
- **core-util** - Core utilities and common code
- **core-ui** - Shared UI components
- Feature modules: `contacts`, `donations`, `video`, `image-editor`, `device-transfer`, `qr`, `billing`
- **lintchecks** - Custom lint rules

### Key Technologies
- **Language**: Kotlin (primary) and Java
- **UI**: Android Views with Jetpack Compose components
- **Database**: SQLCipher for encrypted local storage
- **Networking**: Signal's custom protocol implementation
- **Security**: libsignal for cryptographic operations
- **Build**: Gradle with Kotlin DSL

### Important Directories
- `app/src/main/java/org/thoughtcrime/securesms/` - Main application code
- `app/src/main/res/` - Android resources
- `libsignal-service/src/main/java/` - Signal protocol implementation
- `app/src/main/protowire/` - Protocol buffer definitions
- `app/proguard/` - ProGuard rules for release builds

### Database Layer
Uses SQLCipher with custom ORM-like patterns:
- `SignalDatabase.kt` - Main database access point
- Individual table classes (e.g., `MessageTable.kt`, `RecipientTable.kt`)
- Repository pattern for data access

### Key Components
- **ConversationFragment** - Main chat interface
- **ApplicationContext** - Application-level dependency injection
- **MessageSender/MessageReceiver** - Protocol message handling
- **AttachmentTable/MediaTable** - Media and attachment storage
- **RecipientTable** - Contact and identity management

## Development Guidelines

### Code Style
- Follow [Code Style Guidelines](https://github.com/signalapp/Signal-Android/wiki/Code-Style-Guidelines)
- Use ktlint for formatting: `./gradlew format`
- Kotlin preferred for new code

### Security Considerations
- Never log sensitive information (messages, keys, phone numbers)
- Use Signal's secure storage utilities
- Follow cryptographic best practices from libsignal
- Be aware of metadata leakage

### Testing
- Write unit tests for new functionality
- Use Robolectric for Android unit tests
- Integration tests in `androidTest` directory
- Run `./gradlew qa` before submitting changes

### Build Requirements
- Android SDK with build tools specified in `app/build.gradle.kts`
- NDK for native components
- Java/Kotlin toolchain
- Git repository required for reproducible builds (git hash included in APK)

## Development Workflow

1. **Before making changes**: Run `./gradlew qa` to ensure clean starting state
2. **Development**: Use `playProdDebug` variant for testing
3. **Testing**: Write appropriate tests and run `./gradlew qa`
4. **Code formatting**: Run `./gradlew format`
5. **Final check**: Ensure `./gradlew qa` passes before submitting

## Important Notes

- This is a security-focused application - be extremely careful with any changes
- Protocol modifications require coordination across platforms
- Large changes should be discussed on the community forum first
- Sign the CLA before contributing
- Build system includes reproducible builds verification