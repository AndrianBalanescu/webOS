# webOS TV App Development

## Project Overview

This project is a webOS TV application for LG ThinQ TV devices. webOS TV apps are web-based applications that run on LG's smart TV platform, allowing developers to create interactive experiences using web technologies (HTML, CSS, JavaScript) with access to TV-specific APIs and services.

### Target Platform
- **Platform**: LG webOS TV (ThinQ TV)
- **Development Framework**: webOS TV JavaScript Library (webOSTV.js)
- **App Type**: Web Application

### Current Project Structure

```
webOS/
├── appinfo.json          # App configuration and metadata
├── index.html            # Main application entry point
├── icon.png             # App icon (80x80px)
├── largeIcon.png        # Large app icon (130x130px)
└── webOSTVjs-1.2.10/    # webOS TV JavaScript library
    ├── webOSTV.js       # Production library
    ├── webOSTV-dev.js   # Development library with debugging
    └── LICENSE-2.0.txt  # License information
```

## webOS CLI Tools Documentation

The webOS CLI provides a comprehensive set of tools for developing, packaging, installing, and debugging webOS TV applications. All commands are prefixed with `ares-`.

### Key Commands

#### 1. `ares-generate` - Create App Templates

Generate new apps, services, or configuration files from templates.

**Common Templates:**
- `basic` - Basic web app template with "Hello World"
- `hosted_webapp` - Hosted web app with redirect URL
- `js_service` - JavaScript service template
- `webappinfo` - Generate appinfo.json file

**Examples:**
```bash
# Create a basic web app
ares-generate -t basic ./sampleApp

# Create a hosted web app
ares-generate -t hosted_webapp ./sampleApp

# Create a JS service
ares-generate -t js_service ./sampleService

# Generate appinfo.json file
ares-generate -t webappinfo ./sampleApp

# List all available templates
ares-generate --list
```

#### 2. `ares-package` - Package Application

Create an `.ipk` package file from your app directory.

**Usage:**
```bash
ares-package [OPTION...] APP_DIR
```

**Examples:**
```bash
# Package the app
ares-package ./webOS

# Package with verbose output
ares-package -v ./webOS
```

**Required Files:**
- `appinfo.json` (must exist)
- `icon.png` and `largeIcon.png` (recommended)

#### 3. `ares-install` - Install App on Device

Install a packaged app (`.ipk` file) on a webOS TV device.

**Usage:**
```bash
ares-install [OPTION...] PACKAGE_FILE
```

**Examples:**
```bash
# Install app on a device named "myTV"
ares-install --device myTV com.domain.app_1.0.0_all.ipk

# Install and launch immediately
ares-install --device myTV --launch com.domain.app_1.0.0_all.ipk
```

#### 4. `ares-launch` - Launch Installed App

Launch an already installed application on the device.

**Usage:**
```bash
ares-launch [OPTION...] APP_ID
```

**Examples:**
```bash
# Launch app by ID
ares-launch --device myTV com.domain.app

# Launch with parameters
ares-launch --device myTV --params '{"key":"value"}' com.domain.app
```

#### 5. `ares-inspect` - Debugging Tools

Launch Web Inspector or Node Inspector for debugging.

**Usage:**
```bash
ares-inspect [OPTION...] --app APP_ID
ares-inspect [OPTION...] --service SERVICE_ID
```

**Examples:**
```bash
# Launch Web Inspector for app
ares-inspect --device myTV --app com.domain.app

# Launch Node Inspector for JS service
ares-inspect --device myTV --service com.domain.app.sampleservice
```

#### 6. `ares-server` - Local Development Server

Run a local web server for testing files during development.

**Usage:**
```bash
ares-server [OPTION...] APP_DIR
```

**Examples:**
```bash
# Run server in source directory
ares-server ./webOS

# Run server and open browser
ares-server --open ./webOS
```

#### 7. `ares-novacom` - Device Communication

Low-level tool for device communication, port forwarding, and command execution.

**Usage:**
```bash
# Get private key from device (requires Developer Mode app)
ares-novacom --device myTV --getkey

# Port forwarding
ares-novacom --device emulator --forward --port 6622:3030

# Execute command on device
ares-novacom --device myTV --run "ps aux"
```

#### 8. `ares-device` - Device Information

Retrieve device system information and monitor resources.

**Usage:**
```bash
# List available devices
ares-device --device-list

# Get device system information
ares-device -d myTV -i

# Monitor resource usage
ares-device -d myTV -r

# Monitor specific app
ares-device -d myTV -r -id com.domain.app -t 3
```

### Device Setup

Before deploying apps, you need to set up your device:

1. **Enable Developer Mode** on your webOS TV
2. **Get private key** using `ares-novacom --device DEVICE_NAME --getkey`
3. **Setup device** using `ares-setup-device DEVICE_NAME`
4. **Verify connection** using `ares-device --device-list`

## Project Structure Details

### appinfo.json

The main configuration file that defines app metadata:

```json
{
  "id": "com.domain.app",        // Unique app identifier
  "version": "1.0.0",            // App version
  "vendor": "My Company",        // Vendor name
  "type": "web",                 // App type (web, native, etc.)
  "main": "index.html",          // Entry point HTML file
  "title": "new app",            // Display name
  "icon": "icon.png",            // App icon (80x80px)
  "largeIcon": "largeIcon.png"   // Large icon (130x130px)
}
```

### index.html

The main HTML file that serves as the app's entry point. It includes:
- HTML structure and styling
- webOSTV.js library for TV-specific APIs
- JavaScript code for app functionality

### webOSTV.js Library

The webOS TV JavaScript library provides:
- **Luna Service API** access (`webOS.service.request`)
- **TV-specific features** (back button, app lifecycle, etc.)
- **Device information** and capabilities
- **Media playback** controls

**Key API Pattern:**
```javascript
webOS.service.request("luna://com.palm.systemservice", {
    method: "clock/getTime",
    parameters: {},
    onSuccess: function (args) {
        console.log("UTC:", args.utc);
    },
    onFailure: function (args) {
        console.log("Failed:", args);
    }
});
```

## Development Workflow

### 1. Create/Setup Project

```bash
# Generate from template (if starting new)
ares-generate -t basic ./myApp

# Or use existing project structure
cd webOS
```

### 2. Develop Locally

```bash
# Use local server for testing
ares-server --open ./webOS
```

### 3. Package Application

```bash
# Create .ipk package
ares-package ./webOS
```

### 4. Install on Device

```bash
# Install the package
ares-install --device myTV com.domain.app_1.0.0_all.ipk
```

### 5. Launch Application

```bash
# Launch the app
ares-launch --device myTV com.domain.app
```

### 6. Debug Application

```bash
# Open Web Inspector
ares-inspect --device myTV --app com.domain.app
```

### Complete Workflow Example

```bash
# 1. Package
ares-package ./webOS

# 2. Install
ares-install --device myTV com.domain.app_1.0.0_all.ipk

# 3. Launch
ares-launch --device myTV com.domain.app

# 4. Debug (in separate terminal)
ares-inspect --device myTV --app com.domain.app
```

## Key Resources

### Official Documentation
- **CLI Developer Guide**: https://webostv.developer.lge.com/develop/tools/cli-dev-guide
- **webOS TV Developer Portal**: https://webostv.developer.lge.com/
- **API References**: https://webostv.developer.lge.com/develop/references

### Important Documentation Links
- **webOSTV.js Introduction**: https://webostv.developer.lge.com/develop/references/webostvjs-introduction
- **Luna Service API**: https://webostv.developer.lge.com/develop/references/luna-service-introduction
- **appinfo.json Reference**: https://webostv.developer.lge.com/develop/references/appinfo-json
- **App Resources**: https://webostv.developer.lge.com/develop/getting-started/app-resources

### Development Tools
- **webOS TV Simulator**: For testing without physical device
- **Resource Monitor**: Monitor CPU and memory usage
- **Web Inspector**: Chrome DevTools for webOS apps
- **Node Inspector**: Debugging for JS services

## Best Practices

1. **App ID Convention**: Use reverse domain notation (e.g., `com.yourcompany.appname`)
2. **Version Management**: Follow semantic versioning (major.minor.patch)
3. **Icon Requirements**: 
   - `icon.png`: 80x80px
   - `largeIcon.png`: 130x130px
4. **Testing**: Test on both simulator and physical device
5. **Debugging**: Use `ares-inspect` for Web Inspector access
6. **Local Development**: Use `ares-server` for rapid iteration
7. **Error Handling**: Always implement `onFailure` callbacks for Luna Service calls

## Common Development Patterns

### Luna Service Request Pattern

```javascript
webOS.service.request("luna://SERVICE_NAME", {
    method: "METHOD_NAME",
    parameters: {
        // Request parameters
    },
    onSuccess: function (response) {
        // Handle success
    },
    onFailure: function (error) {
        // Handle error
    }
});
```

### App Lifecycle

```javascript
// App ready
webOS.onSystemReady = function() {
    // Initialize app
};

// Back button handling
webOS.service.request("luna://com.webos.service.tv.input", {
    method: "register",
    parameters: {
        subscribe: true
    },
    onSuccess: function(response) {
        // Handle TV remote input
    }
});
```

## Notes for LLM Context

When working with this project:
- All CLI commands use the `ares-` prefix
- Device names are user-defined (e.g., "myTV", "tv", "emulator")
- App ID must match the `id` field in `appinfo.json`
- Package files are named: `{appId}_{version}_all.ipk`
- Always check device connection with `ares-device --device-list` before deployment
- Use `ares-server` for local testing before packaging
- Web Inspector requires the app to be running on the device

