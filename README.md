
## Pure SDK Android

### Requirements
The Android SDK is available for applications targeting API level 14 and above. Please note that BLE scanning is only available for users running on API level 18 and later.

### Dependencies
The Android SDK uses the Google Awareness API (play-services-awareness) to gather location data. The dependency is included in the package (11.6.0), but may be overriden by your application if already using a different version of the Play Services. Please note that version 9.6.0 or later is required.

This dependency is part of Google Play Services (com.google.android.gms), but only the awareness artifact is included in the SDK.

*Please note that we only use the Awareness API for detecting device state for battery optimizations in addition to Location and Timefences and never call the Places API or any other Google service that might incur costs. No API keys are needed during the implementation of the SDK*

### Installation

#### Adding to your project

Add the following line to your app's `build.gradle`:

```groovy
repositories {
    maven {
        credentials {
                username "<USERNAME>"
                password "<PASSWORD>"
        }
        url 'https://puresdk.azurewebsites.net/artifacts/'
    }
}
```

*The USERNAME and PASSWORD will be provided by Unacast.*

Add the below line to your app's `build.gradle` inside the `dependencies` section:

```groovy
implementation 'com.pure:sdk:1.2.12'
```

The above reference will get the currently latest stable release of the SDK ([Changelog](Changelog/)). It is also possible to reference 'com.pure:sdk:1+' to always get the latest release build, but it's recommended to target a specific release. The major version will only change if there's a breaking change in the API. So as long the major version is the same as previously targeted, you should not need to alter any code when upgrading. Notice of any updates to the SDK will be sent through mail once you're a registered developer.

If you need to downgrade the play-services-awareness version, use *force*:

```groovy
    implementation ('com.google.android.gms:play-services-awareness:9.6.0')
    {
        force = true
    }
```

#### Proguard
If you are using proguard, make sure to add this line to your proguard definition

```java
-keepclassmembers class com.pure.internal.models.** { *; }
```

##### Download

It's also possible to download the SDK. Head to http://puresdk.azurewebsites.net/ and login with your USERNAME and PASSWORD and download the *internal* artifact together with the *sdk* artifact

### How to use it
The SDK is initialized automatically on launch by default, but it will not gather any data unless you choose to start tracking movement.
Starting tracking of the users movement is done by the following snippet:

```java
Pure.startTracking()
```

It is recommended to give the user a way to opt out of the tracking at a later stage (e.g a check box under app settings or profile settings). This is done by calling:

```java
Pure.stopTracking()
```

*The SDK stores the previous state, so you don't have to call Pure.startTracking() every time the app launches.*

On Android 6.0 and later, user permissions are required to be able to collect location data. Even if startTracking() is called, it won't be able to gather any data for these devices. To request location authorization on Android 6.0 and above, you need to do so programatically. This is done by using standard runtime permission code requesting ACCESS_FINE_LOCATION. An example implementation is provided here:

```java
public static final int MY_PERMISSIONS_REQUEST_LOCATION = 99;

public boolean checkLocationPermission() {
    if (ContextCompat.checkSelfPermission(this,
            Manifest.permission.ACCESS_FINE_LOCATION)
            != PackageManager.PERMISSION_GRANTED) {

        // Should we show an explanation?
        if (ActivityCompat.shouldShowRequestPermissionRationale(this,
                Manifest.permission.ACCESS_FINE_LOCATION)) {

            // Show an explanation to the user
            new AlertDialog.Builder(this)
                    .setTitle(R.string.title_location_permission)
                    .setMessage(R.string.text_location_permission)
                    .setPositiveButton(R.string.ok, new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialogInterface, int i) {
                            //Prompt the user once explanation has been shown
                            ActivityCompat.requestPermissions(MainActivity.this,
                                    new String[]{Manifest.permission.ACCESS_FINE_LOCATION},
                                    MY_PERMISSIONS_REQUEST_LOCATION);
                        }
                    })
                    .create()
                    .show();


        } else {
            // No explanation needed, we can request the permission.
            ActivityCompat.requestPermissions(this,
                    new String[]{Manifest.permission.ACCESS_FINE_LOCATION},
                    MY_PERMISSIONS_REQUEST_LOCATION);
        }
        return false;
    } else {
        return true;
    }
}

@Override
public void onRequestPermissionsResult(int requestCode,
                                       String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_LOCATION: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                    && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                // permission was granted
                if (ContextCompat.checkSelfPermission(this,
                        Manifest.permission.ACCESS_FINE_LOCATION)
                        == PackageManager.PERMISSION_GRANTED) {

                    //Request location updates:
                    Pure.startTracking();
                }

            } else {

                // permission denied. Stop tracking if it was started for some reason.
                Pure.stopTracking();
            }
            return;
        }

    }
}
```

**Please note that for tracking to start immediately after location permission is accepted, call Pure.startTracking() as soon as the permission is granted.**

### Onboarding

It's important to ask for these permissions at the right time to get as many users as possible to opt in and to give a better user experience. Users should be presented with a reason for requesting location permission before triggering the actual OS permission dialog and you should also explain this in the terms together with how a user can opt out from tracking later on. Please contact Unacast if you need any language regarding this. There could also be scenarios where you want to trigger onboarding only for citizens of certain countries. Checking the country code and time zone of the device, could be a good way to do this.

### Check if tracking is enabled
It's recommended to have a switch under settings in your app where the user can enable and disable tracking. The current status can be checked by *Pure.getIsTracking()*

### Configuration
All configurations used by the SDK is provided by an external endpoint. This makes it possible to change the configuration without releasing a new version of the application. It also makes it possible to use different configurations based on things like device type, android version, etc.

#### Advanced
The SDK relies on scanning jobs running in the background. As these needs an application unique jobid, it's possible to change the range being used in case of collision with your own application. This is done by adding the following in your AndroidManifest.xml:

```xml
<metadata android:name="com.pure.sdk.JobIdStartId">47483640</metadata>
```
The default start index is 47483640 and the SDK reserves 20 ids starting from the default.


## Permissions
The SDK used the following permissions to collect and report data:

```xml
<!-- Access the approximate location of the device  -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<!-- Access an accurate location of the device  -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<!-- To allow reading bluetooth data  -->
<uses-permission android:name="android.permission.BLUETOOTH" />
<!-- To allow requesting bluetooth scans  -->
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<!-- To get information about the network state of the device  -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!-- To get events when nearby wifis changes  -->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<!-- To be able to trigger wifi scans -->
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<!-- To send any gathered data -->
<uses-permission android:name="android.permission.INTERNET" />
<!-- To detect the current user activty (still, on foot, etc) -->
<uses-permission android:name="com.google.android.gms.permission.ACTIVITY_RECOGNITION" />
<!-- To preserve job status after reboot -->
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"></uses-permission>

```

#### Advanced
As these permissions are automatically merged into your androidmanifest.xml, you need to remove them in your manifest if you for some reason wants to limit the number of permissions. This can be done by the following syntax:

```xml
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" tools:node="remove" />
```
*Note that you need to add the tools namespace (xmlns:tools="http://schemas.android.com/tools") in the manifest element to get this option*


##### Getting your clientId
If you need to get hold of the clientId, you can do so with the following snippet

```java

                Pure.getClientId(new PureCallback<String>() {
                    @Override
                    public void onCallback(String clientId) {
                        Log.d(TAG, clientId);
                    }
                });

```

#### Initialization
The SDK is initialized automatically by default. If you need to override this behaviour, you can do this by adding the following metadata to your AndroidManifest:

```xml

    <meta-data android:name="com.pure.sdk.AutoInit" android:value="false" />

```

To then do your manual init of the SDK, add the following code:

```java

                Pure.init(getApplicationContext(), new PureCallback<PureResult>() {
                    
                    @Override
                    public void onCallback(PureResult pureResult) {

                        if(pureResult.getResultCode() == PureResult.PURE_INIT_SUCCESS)
                        {
                            Log.d(TAG, "Successfully initialized Pure");
                        }
                    }
                });

```

## What kinds of data does the SDK collect
With the default configuration, the SDK will collect information about nearby wifis, geo location and BLE devices (iBeacon, Eddystone and RawBle). These data comes with information about the device, battery levels, etc.

The following is an example of an geo location event. All of these data are sent with each event regardless of type. So when an WIFI event is reported, so are device info and geo location info. 

```json
{
"connection": {
        "bssid": "10:3a:cb:f5:00:2a",
        "cellType": "4G",
        "level": -59,
        "mcc": 242,
        "mnc": 12,
        "ssid": "pure-guest",
        "timestamp": "2018-02-01T11:49:31+00:00",
        "type": "WIFI"
      },
      "device": {
        "activity": true,
        "adid": "1e90927e-8e19-1348-b7e0-f36732db49bc",
        "adidLimited": false,
        "appId": "com.pure.example",
        "appVersion": "1.1",
        "location": "always",
        "manufacturer": "Sony",
        "mcc": 242,
        "mnc": 12,
        "model": "F8331",
        "os": "android",
        "osSdkVersion": "24",
        "osVersion": "7.0",
        "sdkVersion": "1.0",
        "timestamp": "2018-02-01T11:49:31+00:00",
        "trackingEnabled": true
      },
      "event": {
        "altitude": 23.2,
        "bearing": 0,
        "hacc": 21.9,
        "lat": 59.9135209,
        "lon": 10.7478901,
        "provider": "SNAPSHOT",
        "speed": 0,
        "timestamp": "2018-02-01T11:49:31+00:00",
        "updated": "2018-02-01T11:48:31+00:00",
        "vacc": 12.5,
        "type": "LOCATION"
      },
      "location": {
        "altitude": 0,
        "bearing": 0,
        "hacc": 21.9,
        "lat": 59.9135209,
        "lon": 10.7478901,
        "provider": "SNAPSHOT",
        "speed": 0,
        "timestamp": "2018-02-01T11:49:31+00:00",
        "updated": "2018-02-01T11:48:31+00:00",
        "vacc": null,
        "type": "LOCATION"
      },
      "state": {
        "activity": "Still",
        "battery": 1,
        "charging": false,
        "deviceInfo": "1.1, , Sony, F8331, 24",
        "foreground": false,
        "powersave": false,
        "timestamp": "2018-02-01T11:49:31+00:00"
      }
}
```

## Metadata and events
The SDK provides methods to send additional *metadata* about the user or device in addition to *events*. The difference between *metadata* and *events*, is that *metadata* will replace any existing data with the same *type* while *events* will be added and never replaced even when using the same *name*

### Add metadata
To add metadata, use *Pure.associateMetadata(String type, JSONObject data, final PureCallback<PureResult> callback)*. The *type* has to be unique for each model you want to preserve. If sending userinfo, is could look something like this:

```java
                JSONObject data = new JSONObject();
                try {
                    data.put("userId", 1234567);
                    data.put("gender", "male");
                    data.put("birthYear", 1980);
                    
                } catch (JSONException e) {
                    e.printStackTrace();
                }

                Pure.associateMetadata("UserInfo", data, new PureCallback<PureResult>() {
                    @Override
                    public void onCallback(PureResult result) {
                        if(result.isSuccess())
                        {
                           Log.d(TAG, "The data was successfully registered");
                        }
                        else
                        {
                           // If not success, something went wrong. Consider retrying the request
                           Log.w(TAG, "Failed registering the data");
                        }
                        
                    }
                });
```

If executing the same call at a later stage, the data will be replaced with the last data in the cloud. If using a different *type* they will both be persisted.


### Create event
To create an event, use *Pure.createEvent(String name, JSONObject data, final PureCallback<PureResult> callback)*. The *name* describes what kind of event this is. If sending order information, it could look something like this:

```java
                JSONObject data = new JSONObject();
                try {
                    data.put("userId", 1234567);
                    data.put("orderId", 100000);
                    data.put("timestamp", "2018-02-01T11:49:31+00:00");
                    
                } catch (JSONException e) {
                    e.printStackTrace();
                }

                Pure.createEvent("Order", data, new PureCallback<PureResult>() {
                    @Override
                    public void onCallback(PureResult result) {
                        if(result.isSuccess())
                        {
                           Log.d(TAG, "The event was successfully registered");
                        }
                        else
                        {
                           // If not success, something went wrong. Consider retrying the request
                           Log.w(TAG, "Failed registering the event");
                        }
                        
                    }
                });
```

*Events will always be added and not replaced, so sending multiple duplicate events will create the same amount of events in the cloud*

#### What happens if tracking is disabled and you are sending an event?
By default, it will not send any events unless tracking is enabled. If you want to override this behaviour, you will have to use the overload parameter *force* (*Pure.createEvent(String name, JSONObject data, boolean force, final PureCallback<PureResult> callback)*):

```java
                JSONObject data = new JSONObject();
                try {
                    data.put("userId", 1234567);
                    data.put("orderId", 100000);
                    data.put("timestamp", "2018-02-01T11:49:31+00:00");
                    
                } catch (JSONException e) {
                    e.printStackTrace();
                }

                Pure.createEvent("Order", data, true, new PureCallback<PureResult>() {
                    @Override
                    public void onCallback(PureResult result) {
                        if(result.isSuccess())
                        {
                           Log.d(TAG, "The event was successfully registered");
                        }
                        else
                        {
                           // If not success, something went wrong. Consider retrying the request
                           Log.w(TAG, "Failed registering the event");
                        }
                        
                    }
                });
```


## How does the SDK work?
The SDK relies on Google Awareness API, and not without reason. It's using the API to look at the current state of the device, and make sure scanning is triggered less frequently if e.g. the device is still and not moving. It's also using the Awareness API to trigger scanning on intervals and when the device has moved a certain threshold. All scanning intervals and movement thresholds are configured from the cloud.

In the default configuration, the SDK will used JobScheduler on Android 5+ to further preserve battery. This makes the OS stack up any pending jobs, and make sure it only runs on optimal times. It is possible to override this behaviour through the cloud config, but recommended behaviour is to allow the OS to pick the best windows for scanning and reporting data.


## Geo-filtering
The SDK has built in support for geo-filtering. This means that if you need to blacklist or whitelist any areas this can be done through the cloud configuration. If you need to use geo-filtering, please contact Unacast to agree on which areas you want to enable. If an area is blacklisted, the SDK will go into blacklist mode and not send any data. It will still do the occational location lookup to see if the device has moved to a valid location, but it will not send these events while in blacklisted areas. 


## Troubleshooting
If you experience any issues implementing the SDK, the first thing you should do is look for any warnings in the log.

*SDK is unsupported on SDK versions prior to 14* means that the device is running on a OS version not supported by the SDK

*Google Play Services not available. PureSDK will be disabled.* means that we couldn't find Play Services on the device and SDK will be disabled

*Context was null. Unable to initialize.* if you get this warning, the SDK was unable to aquire the application context. This shouldn't happen, but if it do, please contact us for help debugging your exact configuration.

To verify the initialization was successful, look for *Init completed*

If you are doing a manual initialization, you will get the various through *getResultCode()* in the callback. 

