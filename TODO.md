# webOS TV Development TODO & Reference

## Development Checklist

### Setup Tasks
- [ ] Install webOS CLI tools
- [ ] Enable Developer Mode on webOS TV device
- [ ] Setup device connection using `ares-setup-device`
- [ ] Verify device connection with `ares-device --device-list`
- [ ] Get private key using `ares-novacom --device DEVICE_NAME --getkey`
- [ ] Configure `appinfo.json` with correct app ID, version, and metadata
- [ ] Prepare app icons (icon.png: 80x80px, largeIcon.png: 130x130px)

### Development Tasks
- [ ] Create/update HTML structure in `index.html`
- [ ] Implement app functionality using webOSTV.js
- [ ] Test locally using `ares-server`
- [ ] Implement Luna Service API calls
- [ ] Add error handling for all service requests
- [ ] Test TV remote navigation
- [ ] Implement app lifecycle handlers (onSystemReady, etc.)
- [ ] Add proper styling for TV display (large screen, remote navigation)

### Testing Tasks
- [ ] Test on webOS TV Simulator
- [ ] Test on physical webOS TV device
- [ ] Verify app installation process
- [ ] Test app launch and exit
- [ ] Test all Luna Service API calls
- [ ] Verify remote control navigation
- [ ] Test app with Web Inspector (`ares-inspect`)
- [ ] Monitor resource usage with `ares-device -r`
- [ ] Test app updates (version increment)

### Deployment Tasks
- [ ] Update version in `appinfo.json`
- [ ] Package app with `ares-package`
- [ ] Install on target device with `ares-install`
- [ ] Launch and verify functionality
- [ ] Document any device-specific requirements
- [ ] Prepare for app store submission (if applicable)

## Quick Command Reference

### Project Generation
```bash
# List available templates
ares-generate --list

# Create basic web app
ares-generate -t basic ./appName

# Create hosted web app
ares-generate -t hosted_webapp ./appName

# Create JS service
ares-generate -t js_service ./serviceName

# Generate appinfo.json
ares-generate -t webappinfo ./appName
```

### Packaging & Installation
```bash
# Package app
ares-package ./webOS

# Install on device
ares-install --device DEVICE_NAME package.ipk

# Install and launch
ares-install --device DEVICE_NAME --launch package.ipk
```

### Launching & Running
```bash
# Launch app
ares-launch --device DEVICE_NAME com.domain.app

# Launch with parameters
ares-launch --device DEVICE_NAME --params '{"key":"value"}' com.domain.app

# Run local server
ares-server ./webOS

# Run server with browser
ares-server --open ./webOS
```

### Debugging
```bash
# Web Inspector for app
ares-inspect --device DEVICE_NAME --app com.domain.app

# Node Inspector for service
ares-inspect --device DEVICE_NAME --service com.domain.app.service

# List devices
ares-device --device-list

# Get device info
ares-device -d DEVICE_NAME -i

# Monitor resources
ares-device -d DEVICE_NAME -r

# Monitor specific app
ares-device -d DEVICE_NAME -r -id com.domain.app -t 3
```

### Device Management
```bash
# Setup device
ares-setup-device DEVICE_NAME

# Get private key
ares-novacom --device DEVICE_NAME --getkey

# Port forwarding
ares-novacom --device DEVICE_NAME --forward --port DEVICE_PORT:HOST_PORT

# Execute command on device
ares-novacom --device DEVICE_NAME --run "COMMAND"
```

## Workflow Templates

### New Feature Development

1. **Plan Feature**
   - Define requirements
   - Identify needed Luna Services
   - Design UI/UX for TV interface

2. **Implement Locally**
   ```bash
   # Start local server
   ares-server --open ./webOS
   ```
   - Write HTML/CSS/JavaScript
   - Test in browser (with limitations)

3. **Test on Device**
   ```bash
   # Package
   ares-package ./webOS
   
   # Install
   ares-install --device DEVICE_NAME package.ipk
   
   # Launch
   ares-launch --device DEVICE_NAME com.domain.app
   ```

4. **Debug if Needed**
   ```bash
   # Open Web Inspector
   ares-inspect --device DEVICE_NAME --app com.domain.app
   ```

5. **Iterate**
   - Fix issues
   - Re-package and re-install
   - Test again

### Testing Workflow

1. **Local Testing**
   ```bash
   ares-server ./webOS
   ```
   - Test basic functionality
   - Check console for errors

2. **Simulator Testing**
   - Launch webOS TV Simulator
   - Install and test app
   - Verify TV-specific features

3. **Device Testing**
   ```bash
   ares-package ./webOS
   ares-install --device DEVICE_NAME package.ipk
   ares-launch --device DEVICE_NAME com.domain.app
   ```
   - Test on physical device
   - Verify remote control navigation
   - Test all features

4. **Debugging Session**
   ```bash
   ares-inspect --device DEVICE_NAME --app com.domain.app
   ```
   - Use Chrome DevTools
   - Check console logs
   - Inspect network requests
   - Profile performance

5. **Resource Monitoring**
   ```bash
   ares-device -d DEVICE_NAME -r -id com.domain.app -t 2
   ```
   - Monitor CPU usage
   - Monitor memory usage
   - Check for leaks

### Debugging Workflow

1. **Identify Issue**
   - Reproduce the problem
   - Note error messages
   - Check device logs if available

2. **Enable Debugging**
   ```bash
   # Launch app with debugging
   ares-launch --device DEVICE_NAME com.domain.app
   
   # Open Web Inspector
   ares-inspect --device DEVICE_NAME --app com.domain.app
   ```

3. **Investigate**
   - Check console for errors
   - Inspect DOM elements
   - Check network tab for API calls
   - Use breakpoints in Sources tab

4. **Fix and Test**
   - Make code changes
   - Re-package and install
   - Test fix

5. **Verify**
   - Test on multiple devices if possible
   - Verify no regressions
   - Monitor resources

## Tool-Specific Notes

### webOSTV.js Library

**Location**: `webOSTVjs-1.2.10/webOSTV.js` (production) or `webOSTV-dev.js` (development)

**Key Features:**
- Luna Service API wrapper
- App lifecycle management
- TV remote control handling
- Device information access

**Common Patterns:**

```javascript
// Include library
<script src="webOSTVjs-1.2.10/webOSTV.js"></script>
<script src="webOSTVjs-1.2.10/webOSTV-dev.js"></script>

// Luna Service request
webOS.service.request("luna://com.palm.systemservice", {
    method: "clock/getTime",
    parameters: {},
    onSuccess: function (args) {
        console.log("Success:", args);
    },
    onFailure: function (args) {
        console.error("Failure:", args);
    }
});

// App ready handler
webOS.onSystemReady = function() {
    // Initialize app
};

// Back button handling
webOS.service.request("luna://com.webos.service.tv.input", {
    method: "register",
    parameters: { subscribe: true },
    onSuccess: function(response) {
        // Handle input events
    }
});
```

### Luna Service API Patterns

**Service Request Structure:**
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
        console.error("Error:", error);
    }
});
```

**Common Services:**
- `com.palm.systemservice` - System services (clock, etc.)
- `com.webos.service.tv.input` - TV remote input
- `com.webos.service.media` - Media playback
- `com.palm.applicationManager` - App management

**Best Practices:**
- Always implement `onFailure` callback
- Check response validity in `onSuccess`
- Use appropriate service methods
- Handle async nature of service calls

### App Configuration Guidelines

**appinfo.json Requirements:**
- `id`: Must be unique, use reverse domain notation
- `version`: Semantic versioning (major.minor.patch)
- `type`: "web" for web apps
- `main`: Entry point HTML file
- `title`: Display name (max 30 characters)
- `icon`: Required, 80x80px PNG
- `largeIcon`: Required, 130x130px PNG

**Version Management:**
- Increment version for each release
- Follow semantic versioning
- Update version before packaging

**Icon Guidelines:**
- Use PNG format
- Exact dimensions required
- High quality, clear design
- Test visibility on TV screen

### Development Tips

1. **Use Local Server First**
   - Faster iteration
   - Easier debugging
   - No packaging needed

2. **Package Only When Needed**
   - Packaging takes time
   - Use for final testing
   - Use for device deployment

3. **Keep Device Connected**
   - Faster install/launch cycles
   - Easier debugging
   - Better workflow

4. **Use Web Inspector**
   - Essential for debugging
   - Chrome DevTools interface
   - Real-time inspection

5. **Monitor Resources**
   - Check CPU usage
   - Monitor memory
   - Detect leaks early

6. **Test Remote Navigation**
   - TV apps use remote control
   - Test all navigation paths
   - Ensure focus management

## Common Issues & Solutions

### Device Connection Issues
- **Problem**: Device not found
- **Solution**: 
  - Check Developer Mode is enabled
  - Verify device name with `ares-device --device-list`
  - Re-run `ares-setup-device`

### Installation Failures
- **Problem**: Installation fails
- **Solution**:
  - Check app ID is unique
  - Verify appinfo.json is valid
  - Check device has enough storage

### Launch Issues
- **Problem**: App won't launch
- **Solution**:
  - Check app ID matches
  - Verify index.html exists
  - Check console for errors

### Debugging Connection
- **Problem**: Web Inspector won't connect
- **Solution**:
  - Ensure app is running
  - Check device connection
  - Try restarting app

## Notes for LLM Context

When working with this project:
- Always use `ares-` prefixed commands
- Device names are user-configurable
- App ID must be unique and match appinfo.json
- Package files follow pattern: `{appId}_{version}_all.ipk`
- Use local server (`ares-server`) for rapid development
- Package only when deploying to device
- Web Inspector requires app to be running
- Always test on actual device, not just simulator
- TV apps require focus management for remote navigation
- Large screen considerations (1920x1080 typical)

