# Solara

A native iOS application for tracking and visualizing celestial bodies using augmented reality and interactive sky mapping. Solara helps you locate the Sun, Moon, and planets in real-time by overlaying their positions on your camera feed or viewing them on a 3D overhead map.

![Platform](https://img.shields.io/badge/platform-iOS%2016%2B-blue)
![Swift](https://img.shields.io/badge/Swift-5.9-orange)
![License](https://img.shields.io/badge/license-MIT-brightgreen)

## Features

- **Augmented Reality Sky View** — Point your iPhone at the sky to see celestial body positions overlaid on your live camera feed with animated arc paths and waypoint markers
- **3D Overhead Map** — Bird's-eye visualization showing complete celestial trajectories with compass orientation and altitude indicators
- **Timeline Scrubbing** — Drag through day, week, and month views to see where celestial bodies were or will be at any point in time
- **Celestial Search** — Quickly locate the Sun, Moon, Mercury, Venus, Mars, Jupiter, and Saturn with real-time altitude, azimuth, and rise/set data
- **GPS & Manual Location** — Uses Core Location for automatic positioning or accepts manual coordinate entry for any location on Earth
- **Fully Offline** — All astronomical calculations run entirely on-device using optimized algorithms derived from Jean Meeus' "Astronomical Algorithms"

## Requirements

- iOS 16.0 or later
- iPhone with A12 chip or newer (for AR features)
- Xcode 15.0 or later
- macOS Sonoma 14.0 or later (for building)
- CocoaPods 1.14 or later

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/solara.git
cd solara
```

### 2. Install Dependencies

```bash
npm install
npx expo prebuild --platform ios
cd ios && pod install && cd ..
```

### 3. Configure the Backend

The app communicates with a lightweight Node.js API server for data persistence. Start the backend:

```bash
npm run server:dev
```

The API server runs on `http://localhost:5000`.

### 4. Open in Xcode

```bash
open ios/Solara.xcworkspace
```

### 5. Build and Run

1. Select your target device or simulator in Xcode
2. Set the development team under **Signing & Capabilities**
3. Press **Cmd + R** to build and run

> **Note:** The AR camera overlay requires a physical device. The iOS Simulator does not support camera access. Use a real iPhone for the full AR experience.

## Architecture

```
Solara/
├── App/
│   ├── AppDelegate.swift              # Application lifecycle
│   ├── SceneDelegate.swift            # Scene management
│   └── App.swift                      # Root view controller
├── Screens/
│   ├── ARView/
│   │   └── ARViewScreen.swift         # AR camera overlay with celestial markers
│   ├── MapView/
│   │   └── MapViewScreen.swift        # 3D overhead sky map
│   ├── Search/
│   │   └── SearchScreen.swift         # Celestial body search and filtering
│   └── Settings/
│       └── SettingsScreen.swift       # Location, preferences, profile
├── Components/
│   ├── CelestialArcOverlay.swift      # SVG arc path rendering
│   ├── CelestialListItem.swift        # Search result row
│   ├── FloatingControls.swift         # Translucent AR control panel
│   ├── TimelineSlider.swift           # Interactive time scrubber
│   ├── TimeRangeSelector.swift        # Day/Week/Month segmented control
│   └── TranslucentPanel.swift         # Frosted glass UI panel
├── Core/
│   ├── CelestialEngine.swift          # Astronomical position calculations
│   ├── LocationManager.swift          # Core Location wrapper
│   ├── SensorManager.swift            # Device motion and compass
│   └── StorageManager.swift           # UserDefaults persistence
├── Navigation/
│   ├── MainTabController.swift        # Bottom tab bar controller
│   └── RootNavigationController.swift # Navigation stack
├── Theme/
│   └── Theme.swift                    # Colors, typography, spacing constants
├── Networking/
│   ├── APIClient.swift                # URLSession API client
│   └── Endpoints.swift                # API route definitions
├── Resources/
│   ├── Assets.xcassets/               # App icons, colors, images
│   ├── LaunchScreen.storyboard        # Launch screen
│   └── Info.plist                     # App configuration
└── Backend/
    ├── server/                        # Express.js API server
    │   ├── index.ts                   # Server entry point
    │   ├── routes.ts                  # API routes
    │   └── storage.ts                 # Data persistence
    └── shared/
        └── schema.ts                  # Database schema
```

## Celestial Calculation Engine

The core astronomy engine (`CelestialEngine.swift`) computes celestial positions entirely on-device:

| Calculation | Method |
|-------------|--------|
| Solar position | Low-precision solar coordinates using Julian date |
| Lunar position | Simplified lunar theory with periodic terms |
| Planetary positions | Keplerian orbital elements with perturbation corrections |
| Coordinate transform | Equatorial to horizontal (altitude/azimuth) conversion |
| Rise/Set times | Iterative approximation using standard atmospheric refraction |
| Arc trajectories | Multi-point path interpolation across configurable time ranges |

Accuracy is within 0.5 degrees for the Sun and Moon, and within 1 degree for planets — sufficient for visual sky tracking and photography planning.

## Design

The interface uses a deep space blue palette optimized for outdoor night viewing to preserve dark adaptation:

| Element | Color | Hex |
|---------|-------|-----|
| Background | Deep Navy | `#001F54` |
| Surfaces | Dark Indigo | `#0A1128` |
| Accent | Amber Gold | `#F7B801` |
| Markers | Celestial Blue | `#4DA6FF` |
| Primary Text | Soft White | `#E8E8E8` |
| Secondary Text | Muted Lavender | `#A0A0B0` |

UI panels use `UIVisualEffectView` with `.systemUltraThinMaterialDark` for translucent frosted glass overlays that don't obstruct the sky view.

## Permissions

The app requests the following entitlements:

| Permission | Key | Purpose |
|------------|-----|---------|
| Camera | `NSCameraUsageDescription` | AR sky overlay with live camera feed |
| Location (When In Use) | `NSLocationWhenInUseUsageDescription` | GPS-based celestial position calculations |

Both permissions are requested at runtime with clear explanations. The app degrades gracefully if permissions are denied — manual coordinate entry is available as a fallback for location, and the 3D map view works without camera access.

## Configuration

### Info.plist

| Key | Value |
|-----|-------|
| `CFBundleIdentifier` | `com.solara.app` |
| `CFBundleDisplayName` | Solara |
| `UIRequiredDeviceCapabilities` | `armv7`, `location-services` |
| `UISupportedInterfaceOrientations` | Portrait only |
| `UIStatusBarStyle` | `UIStatusBarStyleLightContent` |

### Build Settings

| Setting | Value |
|---------|-------|
| Deployment Target | iOS 16.0 |
| Swift Language Version | 5.9 |
| Architecture | arm64 |
| Code Signing | Automatic |

## API Server

The companion backend handles data persistence and user preferences:

```
POST   /api/locations          Create saved location
GET    /api/locations          List saved locations
DELETE /api/locations/:id      Remove saved location
GET    /api/settings           Get user preferences
PUT    /api/settings           Update user preferences
```

### Running the Backend

```bash
# Development
npm run server:dev

# Production
npm run server:build
npm run server:prod
```

The server requires Node.js 20+ and optionally connects to PostgreSQL for persistent storage.

## Testing

### Unit Tests

```bash
xcodebuild test \
  -workspace ios/Solara.xcworkspace \
  -scheme Solara \
  -destination 'platform=iOS Simulator,name=iPhone 16'
```

### On-Device Testing

For AR features, deploy to a physical device:

1. Connect your iPhone via USB
2. Select your device in the Xcode toolbar
3. Build and run (Cmd + R)
4. Grant camera and location permissions when prompted
5. Point the device at the sky to see celestial overlays

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## Acknowledgments

- Astronomical algorithms adapted from Jean Meeus, *Astronomical Algorithms* (2nd Edition)
- Design inspired by Star Walk 2 and Sky Guide
- Night-optimized color palette based on astronomical dark adaptation research
