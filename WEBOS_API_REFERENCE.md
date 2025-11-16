# webOS TV API & Tools Reference

## Understanding the Library Files

### webOSTV.js (Production Library)

**Location**: `webOSTVjs-1.2.10/webOSTV.js`

This is the **production** version of the webOS TV JavaScript library. It provides the core API for interacting with webOS TV services and features. The file is minified for production use.

**What it provides:**
- Creates the global `window.webOS` object
- Provides access to Luna Service API
- Device information and capabilities
- Platform detection
- System integration

### webOSTV-dev.js (Development Library)

**Location**: `webOSTVjs-1.2.10/webOSTV-dev.js`

This is the **development** version with additional debugging and development tools. It provides extra functionality for development and debugging.

**What it provides:**
- Creates the global `window.webOSDev` object
- Additional development utilities
- Enhanced debugging capabilities
- Development-specific APIs

**Usage in index.html:**
```html
<script src="webOSTVjs-1.2.10/webOSTV.js" charset="utf-8"></script>
<script src="webOSTVjs-1.2.10/webOSTV-dev.js" charset="utf-8"></script>
```

Both files are typically included during development. In production, you may only need `webOSTV.js`.

## webOSTV.js API Functions

Based on the library structure and documentation, here are the main APIs provided:

### 1. `webOS.service` - Luna Service API

The primary way to interact with webOS system services.

**Main Method: `webOS.service.request()`**

```javascript
webOS.service.request("luna://SERVICE_NAME", {
    method: "METHOD_NAME",
    parameters: {
        // Service-specific parameters
    },
    onSuccess: function(response) {
        // Handle successful response
    },
    onFailure: function(error) {
        // Handle error
    },
    onComplete: function(result) {
        // Always called after success or failure
    },
    subscribe: true  // Optional: subscribe to updates
});
```

**Example from your index.html:**
```javascript
webOS.service.request("luna://com.palm.systemservice", {
    method: "clock/getTime",
    parameters: {},
    onSuccess: function (args) {
        console.log("UTC:", args.utc);
    },
    onFailure: function (args) {
        console.log("Failed to getTime");
    }
});
```

### 2. `webOS.deviceInfo` - Device Information

Get detailed information about the device.

```javascript
webOS.deviceInfo(function(info) {
    console.log("Model:", info.modelName);
    console.log("Version:", info.version);
    console.log("Screen:", info.screenWidth + "x" + info.screenHeight);
    console.log("UHD:", info.uhd);
    console.log("OLED:", info.oled);
    console.log("HDR10:", info.hdr10);
    console.log("Dolby Vision:", info.dolbyVision);
    console.log("Brand:", info.brandName);
});
```

**Available Properties:**
- `modelName` - TV model name
- `version` - Firmware version
- `versionMajor`, `versionMinor`, `versionDot` - Version components
- `screenWidth`, `screenHeight` - Screen dimensions
- `uhd` - UHD support (boolean)
- `uhd8K` - 8K support (boolean)
- `oled` - OLED display (boolean)
- `hdr10` - HDR10 support (boolean)
- `dolbyVision` - Dolby Vision support (boolean)
- `dolbyAtmos` - Dolby Atmos support (boolean)
- `brandName` - TV brand name
- `manufacturer` - Manufacturer name
- `sdkVersion` - SDK version
- `ddrSize` - Memory size
- `tuner` - TV tuner availability (boolean)
- `platformBizType` - Platform type

### 3. `webOS.platform` - Platform Detection

Detect the platform type.

```javascript
if (webOS.platform.tv) {
    console.log("Running on TV");
} else if (webOS.platform.watch) {
    console.log("Running on Smart Watch");
} else if (webOS.platform.open) {
    console.log("Running on webOS Open");
} else if (webOS.platform.legacy) {
    console.log("Running on legacy webOS");
}
```

**Properties:**
- `tv` - TV platform (boolean)
- `watch` - Smart Watch platform (boolean)
- `open` - webOS Open platform (boolean)
- `legacy` - Legacy webOS platform (boolean)
- `chrome` - Chrome version number
- `unknown` - Unknown platform (boolean)

### 4. `webOS.systemInfo` - System Information

Get system-level information.

```javascript
var sysInfo = webOS.systemInfo();
console.log("Country:", sysInfo.country);
console.log("Smart Service Country:", sysInfo.smartServiceCountry);
console.log("Timezone:", sysInfo.timezone);
```

**Returns:**
- `country` - Device country code
- `smartServiceCountry` - Smart service country
- `timezone` - Timezone information

### 5. `webOS.keyboard` - Keyboard Management

Check keyboard visibility.

```javascript
if (webOS.keyboard.isShowing()) {
    console.log("Keyboard is visible");
}
```

### 6. `webOS.platformBack` - Back Button

Handle platform back button.

```javascript
webOS.platformBack();  // Trigger back action
```

### 7. `webOS.fetchAppId()` - Get App ID

Get the current app's ID.

```javascript
var appId = webOS.fetchAppId();
console.log("App ID:", appId);
```

### 8. `webOS.fetchAppInfo()` - Get App Info

Get app information from appinfo.json.

```javascript
webOS.fetchAppInfo(function(appInfo) {
    console.log("App ID:", appInfo.id);
    console.log("Version:", appInfo.version);
    console.log("Title:", appInfo.title);
});
```

### 9. `webOS.fetchAppRootPath()` - Get App Root

Get the root path of the app.

```javascript
var rootPath = webOS.fetchAppRootPath();
console.log("App root:", rootPath);
```

### 10. `webOS.libVersion` - Library Version

Get the webOSTV.js library version.

```javascript
console.log("Library version:", webOS.libVersion);
```

### 11. `webOS.onSystemReady` - System Ready Callback

Callback when the system is ready.

```javascript
webOS.onSystemReady = function() {
    console.log("System is ready");
    // Initialize your app here
};
```

## webOSDev API Functions (Development Library)

### 1. `webOSDev.launch()` - Launch Applications

Launch other applications.

```javascript
webOSDev.launch({
    id: "com.webos.app.browser",
    params: {
        target: "https://example.com"
    },
    onSuccess: function() {
        console.log("App launched");
    },
    onFailure: function(error) {
        console.error("Launch failed:", error);
    }
});
```

### 2. `webOSDev.launchParams()` - Get Launch Parameters

Get parameters passed when app was launched.

```javascript
var params = webOSDev.launchParams();
console.log("Launch params:", params);
```

### 3. `webOSDev.connection` - Network Connection

Check network connection status.

```javascript
webOSDev.connection.getStatus({
    subscribe: true,
    onSuccess: function(status) {
        console.log("Connected:", status.isInternetConnectionAvailable);
        console.log("Wired:", status.wired);
        console.log("WiFi:", status.wifi);
    },
    onFailure: function(error) {
        console.error("Connection check failed:", error);
    }
});
```

### 4. `webOSDev.DRM` - DRM Support

Digital Rights Management support.

```javascript
var drmAgent = webOSDev.drmAgent("widevine");
drmAgent.load({
    onSuccess: function() {
        console.log("DRM loaded");
    },
    onFailure: function(error) {
        console.error("DRM load failed:", error);
    }
});
```

### 5. `webOSDev.LGUDID()` - Get LG UDID

Get unique device ID.

```javascript
webOSDev.LGUDID({
    onSuccess: function(result) {
        console.log("UDID:", result.id);
    },
    onFailure: function(error) {
        console.error("Failed to get UDID:", error);
    }
});
```

## Common Luna Services

Here are commonly used Luna Services you can call with `webOS.service.request()`:

### System Services

**Clock Service:**
```javascript
webOS.service.request("luna://com.palm.systemservice", {
    method: "clock/getTime",
    parameters: {},
    onSuccess: function(response) {
        console.log("Time:", response.utc);
    }
});
```

**Application Manager:**
```javascript
webOS.service.request("luna://com.webos.applicationManager", {
    method: "launch",
    parameters: {
        id: "com.webos.app.browser",
        params: {}
    },
    onSuccess: function(response) {
        console.log("App launched");
    }
});
```

**TV Input Service:**
```javascript
webOS.service.request("luna://com.webos.service.tv.input", {
    method: "register",
    parameters: {
        subscribe: true
    },
    onSuccess: function(response) {
        // Handle remote control input
    }
});
```

**Settings Service:**
```javascript
webOS.service.request("luna://com.webos.settingsservice", {
    method: "getSystemSettings",
    parameters: {
        category: "sound",
        keys: ["soundOutput", "soundOutputDigital"]
    },
    onSuccess: function(response) {
        console.log("Sound output:", response.settings.soundOutput);
    }
});
```

**Media Service:**
```javascript
webOS.service.request("luna://com.webos.service.media", {
    method: "play",
    parameters: {
        uri: "http://example.com/video.mp4"
    },
    onSuccess: function(response) {
        console.log("Media playing");
    }
});
```

## ares CLI Commands Overview

The `ares` CLI provides command-line tools for webOS development:

### Development Commands

1. **`ares-generate`** - Create projects from templates
   - Generate web apps, services, config files
   - Templates: `basic`, `hosted_webapp`, `js_service`, `webappinfo`

2. **`ares-package`** - Package apps into `.ipk` files
   - Creates installable packages
   - Validates app structure

3. **`ares-install`** - Install apps on devices
   - Installs `.ipk` packages
   - Can launch after installation

4. **`ares-launch`** - Launch installed apps
   - Starts apps on target device
   - Can pass launch parameters

5. **`ares-inspect`** - Debugging tools
   - Web Inspector for web apps
   - Node Inspector for JS services

6. **`ares-server`** - Local development server
   - Test apps in browser
   - Rapid development iteration

### Device Management Commands

7. **`ares-setup-device`** - Configure devices
   - Add/remove device connections
   - Setup development environment

8. **`ares-device`** - Device information
   - List connected devices
   - Get system information
   - Monitor resource usage

9. **`ares-novacom`** - Low-level device communication
   - Get private keys
   - Port forwarding
   - Execute shell commands

10. **`ares-shell`** - Execute shell commands
    - Run commands on device
    - Advanced debugging

11. **`ares-push`** - Transfer files to device
    - Upload files to device
    - Deploy resources

12. **`ares-pull`** - Retrieve files from device
    - Download files from device
    - Get logs and data

13. **`ares-log`** - View application logs
    - Show app/service logs
    - Save logs to file

## How They Work Together

1. **Development Flow:**
   ```
   ares-generate → Create project
   ares-server → Test locally
   webOSTV.js → Use APIs in code
   ares-package → Create package
   ares-install → Install on device
   ares-launch → Run app
   ares-inspect → Debug
   ```

2. **API Usage:**
   - Include `webOSTV.js` in HTML
   - Use `webOS.service.request()` to call Luna Services
   - Use `webOS.deviceInfo()` to get device capabilities
   - Use `webOSDev` APIs for development features

3. **Service Communication:**
   - All system services accessed via Luna Service API
   - Services use `luna://` protocol
   - Asynchronous callbacks for responses

## Resources

- **Official Documentation**: https://webostv.developer.lge.com/
- **webOSTV.js Reference**: https://webostv.developer.lge.com/develop/references/webostvjs-introduction
- **Luna Service API**: https://webostv.developer.lge.com/develop/references/luna-service-introduction
- **CLI Developer Guide**: https://webostv.developer.lge.com/develop/tools/cli-dev-guide

## Version Information

- **webOSTV.js Version**: 1.2.10
- **Library Location**: `webOSTVjs-1.2.10/`
- **License**: Apache-2.0

