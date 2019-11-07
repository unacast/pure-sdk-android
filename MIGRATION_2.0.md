# Pure SDK 2.0.6

## Declaring dependencies
```Groovy
repositories {  
  maven {  
  url = "https://dl.bintray.com/unacast/pure"  
  }  
}

dependencies {
  implementation 'com.unacast.pure:pure-sdk:2.0.6'

  // Gradle should load these as transitive dependencies. If these **fail to load**, you can add them manually:
  implementation 'com.google.android.gms:play-services-location:17.0.0'
  implementation 'com.google.android.gms:play-services-ads-identifier:17.0.0'
  implementation 'androidx.appcompat:appcompat:1.0.2'
  implementation 'androidx.work:work-runtime-ktx:2.2.0'
  implementation 'androidx.core:core-ktx:1.0.2'
  implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:1.3.41"
}
```

## Breaking Changes
### SDK Initialization
- The SDK now has to be explicitly initialized anywhere within your application:
	```Kotlin
	// Kotlin
	val publisherId = "<YOUR-PUBLISHER-ID>"  
	Pure.getInstance(applicationContext).init(publisherId = publisherId, callback = object : OnPureCompleteListener<PureResult> {  
	    override fun onSuccess(pureResult: PureResult) {  
	    // Do something when initialization is done, e.g. start tracking  
	    }  
	})

	// Java
	String publisherId = "<YOUR-PUBLISHER-ID>";  
	Pure.getInstance(applicationContext).init(publisherId, new OnPureCompleteListener<PureResult>() {  
	    @Override  
	    public void onSuccess(PureResult pureResult) {  
	    // Do something when initialization is done, e.g. start tracking  
	    }  
	    
	    @Override  
	    public void onFailure(@Nullable Exception e, @Nullable PureResult result) {  
	    // Initialization failed  
	    }  
	});
	```

- Method signature for tracking start has changed to: 
	 ```Kotlin
	 // Can ble placed in `onSuccess` of init method.
	Pure.getInstance(applicationContext).startTracking()
	```
- Method signature for tracking stop has changed to:
	```Kotlin  
	Pure.getInstance(applicationContext).stopTracking()  
	```

- Method signature for retrieving clientId no longer uses callback:
	```Kotlin  
	// Kotlin
	Pure.getInstance(applicationContext).clientId

	// Java
	Pure.getInstance(applicationContext).getClientId()  
	```

- Method signature for retrieving tracking status has changed to:
	```Kotlin  
	// Kotlin
	Pure.getInstance(applicationContext).isTracking

	// Java
	Pure.getInstance(applicationContext).isTracking()  
	```

- Method signature for `associateMetadata` now uses `OnPureCompleteListener` instead of `PureCallback` and Pure has to be accessed with `getInstance(applicationContext)`.

- Method signature for `createEvent` now uses `OnPureCompleteListener` instead of `PureCallback` and Pure has to be accessed with `getInstance(applicationContext)`






