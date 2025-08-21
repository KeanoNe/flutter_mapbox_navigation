# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Flutter Commands
- `flutter pub get` - Install dependencies
- `flutter test` - Run all tests
- `flutter analyze` - Run static analysis with very_good_analysis rules
- `flutter pub deps` - Show dependency tree

### Testing
- `flutter test` - Run unit tests
- `flutter test test/flutter_mapbox_navigation_test.dart` - Run specific test file

### Example App
- `cd example && flutter run` - Run the example app
- `cd example && flutter build apk` - Build Android APK
- `cd example && flutter build ios` - Build iOS app

### Android Development
- Requires `MAPBOX_DOWNLOADS_TOKEN` in `gradle.properties` with `downloads:read` scope
- Build with: `cd example/android && ./gradlew build`
- MainActivity must extend `FlutterFragmentActivity` (not `FlutterActivity`)

### iOS Development
- Requires Mapbox access token in `Info.plist` as `MBXAccessToken`
- Requires `.netrc` file in home directory with Mapbox credentials
- Build with: `cd example/ios && xcodebuild`

## Architecture

### Plugin Structure
This is a Flutter plugin providing Mapbox Navigation SDK integration for iOS and Android:

**Core Plugin (`lib/`):**
- `flutter_mapbox_navigation.dart` - Main API exports
- `src/flutter_mapbox_navigation.dart` - Core `MapBoxNavigation` singleton class
- `src/flutter_mapbox_navigation_platform_interface.dart` - Platform interface abstraction
- `src/flutter_mapbox_navigation_method_channel.dart` - Method channel implementation
- `src/embedded/` - Embedded navigation view components (controller + view)
- `src/models/` - Data models (WayPoint, RouteEvent, etc.)

**Platform Implementations:**
- **Android**: `android/src/main/kotlin/com/eopeter/fluttermapboxnavigation/`
  - `FlutterMapboxNavigationPlugin.kt` - Main plugin entry point
  - `activity/NavigationActivity.kt` - Full-screen navigation activity
  - `factory/EmbeddedNavigationViewFactory.kt` - Embedded view factory
  - `models/` - Platform-specific data models
- **iOS**: `ios/Classes/`
  - `FlutterMapboxNavigationPlugin.swift` - Main plugin entry point
  - `NavigationFactory.swift` - Navigation factory base class
  - `models/` - Platform-specific data models

### Key Components

**MapBoxNavigation Singleton**: Central API providing methods for:
- `startNavigation()` - Full-screen turn-by-turn navigation
- `startFreeDrive()` - Passive navigation without destination
- `finishNavigation()` - Stop navigation
- `addWayPoints()` - Add stops to ongoing navigation
- `registerRouteEventListener()` - Listen for navigation events

**Embedded Navigation View**: Widget for embedding navigation in Flutter apps:
- `MapBoxNavigationView` widget
- `MapBoxNavigationViewController` for programmatic control
- Platform views for iOS/Android integration

**Event System**: Real-time navigation events via EventChannel:
- Route building/built/failed events
- Navigation progress updates
- Arrival notifications
- Turn instructions

### Configuration Requirements

**Mapbox Access Tokens**:
- Public token in platform-specific files (`Info.plist` for iOS, `mapbox_access_token.xml` for Android)
- Downloads token in `gradle.properties` for Android SDK access
- `.netrc` configuration for iOS development

**Permissions**:
- Location permissions required on both platforms
- Background modes for continuous navigation (iOS)
- Must handle runtime permission requests

### Navigation Modes
- Driving with traffic (default)
- Driving without traffic
- Walking
- Cycling

### Supported Features
- Turn-by-turn navigation with voice instructions
- Alternative routes
- Route simulation for testing
- Free-drive mode (passive navigation)
- Embedded navigation views
- Multi-waypoint routing (up to 25 waypoints)
- Custom map styles (day/night)
- Multiple languages and units