
## Pure SDK Android

## Changelog

### 1.2.11
- Added undetermined check on location permission

### 1.2.10
- [BUG] Fixed issue where stopping a job could result in work being executed in the main thread
- [BUG] Better handling of situations where BluetoothLeScanner is unavailable
- More Permission check exceptions are warnings rather than errors

### 1.2.9
- [BUG] Fixed issue where wifi SSID was null
- [BUG] Fixed issue where batteryStatus was unavailable

### 1.2.8
- Exposed getClientId (see documentation)

### 1.2.7
- Added client side blacklist and whitelist locations to filter out locationdata for specific location

### 1.2.6
- Downgraded some errors to warnings
- Added option to manually handle init of the SDK

### 1.1.9
- [BUG] Fixed issue with BLE scanning job starting even when hardware BLE was missing

### 1.1.7
- Added config option to forceUpdate config

### 1.1.6
- [BUG] Fixed issue with MMC getting wrong results
- [BUG] Fixed issue where enableTracking was called before init was completed

### 1.1.5
- First public release
