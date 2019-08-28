
## Pure SDK Android

## Changelog

### 1.2.28
- Removed the Awareness API dependency 
- Fixed issue with serialization of ArrayLists on older Android versions
- Moved to public maven repository

### 1.2.27
- Added scanning on interval without movement

### 1.2.26
- Fixed issue with age on cellInfo and wifi events

### 1.2.25
- Fixed issue with log not being cleared if reaching the entry limit

### 1.2.24
- Now ignores accuracy and bearing properties if not available

### 1.2.23
- Added missing Id4 for cell info

### 1.2.22
- Fixed timestamps in cell info

### 1.2.21
- CellInfo collection is now configurable

### 1.2.20
- Added CellInfo
- New metrics collected for Wifi

### 1.2.19
- Now reports if Play Services is missing permissions
- The SDK should now prevent Play Services from reporting apps using the SDK if any permission is missing

### 1.2.15
- Added optional data encryption

### 1.2.14
- Added reporting of LocationServicesEnabled and BluetoothEnabled status in config pings

### 1.2.13
- Fixed bug in config updates not respecting the cloud interval if more frequent than 10 hours.

### 1.2.12
- Fixed issue with config updates requested too often when a device was running low on resources and application was terminated by the OS

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
