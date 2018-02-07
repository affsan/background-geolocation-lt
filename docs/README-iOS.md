# :books: API Documentation
### :wrench: [Configuration Options](#wrench-configuration-options-1)
  + [Geolocation Options](#wrench-geolocation-options)    
  + [Activity Recognition Options](#wrench-activity-recognition-options)    
  + [HTTP & Persistence Options](#wrench-http--persistence-options)
  + [Geofencing Options](#wrench-geofencing-options)
  + [Application Options](#wrench-application-options)    
### :zap: [Events](#zap-events-1)
### :small_blue_diamond: [Methods](#large_blue_diamond-methods)
  + [Core API Methods](#small_blue_diamond-core-api-methods)
  + [HTTP & Persistence Methods](#small_blue_diamond-http--persistence-methods)
  + [Geofencing Methods](#small_blue_diamond-geofencing-methods)
  + [Logging Methods](#small_blue_diamond-logging-methods)
### :blue_book: Guides
  + [Philosophy of Operation](../../../wiki/Philosophy-of-Operation)
  + [Geofencing](geofencing.md)
  + [HTTP Features](http.md)
  + [Android Headless Mode](../../..//wiki/Android-Headless-Mode)
  + [Location Data Schema](../../../wiki/Location-Data-Schema)
  + [Debugging](../../../wiki/Debugging)

# :wrench: Configuration Options

The following **Options** can all be provided to the plugin's `TSConfig` instance.

```obj-c

TSConfig *config = [TSConfig sharedInstance];

[config updateWithBlock:^(TSConfigBuilder *builder) {
    builder.debug = YES;
    builder.desiredAccuracy = kCLLocationAccuracyBest;
    build.distanceFilter = 10;
    builder.stopOnTerminate = NO;    
    builder.url = @"http://your.server.com/locations";
    builder.params = @{@"foo":@"bar"};
    builder.headers = @{@"X-FOO":@"FOO", @"X-BAR":@"BAR"};
    builder.autoSync = YES;
}];

TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
[bgGeo ready];

if (!config.enabled) {
    [bgGeo start];
}
```

## :wrench: Geolocation Options


| Option      | Type      | Default   | Note                              |
|-------------|-----------|-----------|-----------------------------------|
| [`desiredAccuracy`](#config-cllocationaccuracy-desiredaccuracy-kcllocationaccuracybest) | `CLLocationAccuracy` | `kCLLocationAccuracyBest` | Specify the desired-accuracy of the geolocation system. |
| [`distanceFilter`](#config-cllocationdistance-distancefilter-10) | `CLLocationDistance` | `10` | The minimum distance (measured in meters) a device must move horizontally before an update event is generated. |
| [`stationaryRadius`](#config-cllocationdistance-stationaryradius-25) | `CLLocationDistance`  | `25`  | When stopped, the minimum distance the device must move beyond the stationary location for aggressive background-tracking to engage. |
| [`disableElasticity`](#config-bool-disableelasticity-no) | `BOOL` | `NO` | Set true to disable automatic speed-based #distanceFilter elasticity. eg: When device is moving at highway speeds, locations are returned at ~ 1 / km. |
| [`elasticityMultiplier`](#config-double-elasticitymultiplier-1) | `double` | `1` | Controls the scale of automatic speed-based `distanceFilter` elasticity.  Increasing `elasticityMultiplier` will result in few location samples as speed increases. |
| [`stopAfterElapsedMinutes`](#config-double-stopafterelapsedminutes--1) | `double`  | `0`  | The plugin can optionally automatically stop tracking after some number of minutes elapses after the [`#start`](#start) method was called. |
| [`stopOnStationary`](#config-bool-stoponstationary-no) | `BOOL`  | `NO`  | The plugin can optionally automatically `#stop` tracking when the `stopTimeout` timer elapses. |
| [`desiredOdometerAccuracy`](#config-cllocationaccuracy-desiredodometeraccuracy-100) | `CLLocationAccuracy`  | `100`  | Location accuracy threshold in **meters** for odometer calculations. |
| [`useSignificantChangesOnly`](#config-bool-usesignificantchangesonly-no) | `BOOL` | `NO` | Defaults to `NO`.  Set `YES` in order to disable constant background-tracking and use only the iOS [Significant Changes API](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/index.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges). |
| [`locationAuthorizationRequest`](#config-nsstring-locationauthorizationrequest-always) | `NSString` | `Always` | The desired iOS location-authorization request, either `Always` or `WhenInUse`. |
| [`locationAuthorizationAlert`](#config-nsdictionary-locationauthorizationalert) | `NSDictionary` | `@{}` | When you configure the plugin [`locationAuthorizationRequest`](config-string-locationauthorizationrequest-always) `Always` or `WhenInUse` and the user *changes* that value in the app's location-services settings or *disables* location-services, the plugin will display an Alert directing the user to the **Settings** screen. |

## :wrench: Activity Recognition Options


| Option      | Type      | Default   | Note                              |
|-------------|-----------|-----------|-----------------------------------|
| [`activityRecognitionInterval`](#config-double-millis-10000-activityrecognitioninterval) | `double` | `10000` | The desired time between activity detections. Larger values will result in fewer activity detections while improving battery life. A value of `0` will result in activity detections at the fastest possible rate. |
| [`stopTimeout`](#config-double-stoptimeout-5) | `double` | `5`  | The number of **minutes** to wait before turning off location-services after the ActivityRecognition System (ARS) detects the device is `STILL` |
| [`minimumActivityRecognitionConfidence`](#config-nsinteger-minimumactivityrecognitionconfidence-75) | `NSInteger` | `75` | Each activity-recognition-result returned by the API is tagged with a "confidence" level expressed as a `%`.  You can set your desired confidence to trigger a state-change.|
| [`stopDetectionDelay`](#config-double-stopdetectiondelay-0) | `double` | `0` | Number of **minute** to delay the stop-detection system from being activated.  Default is no delay.| 
| [`disableStopDetection`](#config-bool-disablestopdetection-no) | `BOOL` | `NO` | Disable accelerometer-based **Stop-detection System**. :warning: Not recommended|
| [`activityType`](#config-clactivitytype-activitytype-clactivitytypeother) | `CLActivityType` |  `CLActivityTypeOther` | Presumably, this affects ios GPS algorithm.  See [Apple docs](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instp/CLLocationManager/activityType) for more information |
| [`disableMotionActivityUpdates`](#config-bool-disablemotionactivityupdates-no) | `BOOL` | `NO` | Disable iOS motion-activity updates (eg: "walking", "in_vehicle").  This feature requires a device having the **M7** co-processor (ie: iPhone 5s and up). :warning: The plugin is **HIGHLY** optimized to use this for improved battery performance.  You are **STRONLY** recommended to **NOT** disable this. |


## :wrench: HTTP & Persistence Options

:blue_book: [HTTP Guide](http.md)

| Option      | Type      | Default   | Note                              |
|-------------|-----------|-----------|-----------------------------------|
| [`url`](#config-string-url-undefined) | `NSString` | `""` | Your server url where you wish to HTTP POST locations to |
| [`httpTimeout`](#config-nsinteger-httptimeout-60000) | `NSInteger` | `60000` | HTTP request timeout in milliseconds. |
| [`params`](#config-nsdictionary-params) | `NSDictionary` | `null` | Optional HTTP params sent along in HTTP request to above [`#url`](#config-string-url-undefined) |
| [`extras`](#config-nsdictionary-extras) | `NSDictionary` | `null` | Optional meta-data to attach to *each* recorded location |
| [`headers`](#config-nsdictionary-headers) | `NSDictionary` | `null` | Optional HTTP headers sent along in HTTP request to above [`#url`](#config-string-url-undefined) |
| [`method`](#config-nsstring-method-post) | `NSString` | `POST` | The HTTP method.  Defaults to `POST`.  Some servers require `PUT`.|
| [`httpRootProperty`](#config-nsstring-httprootproperty-location) | `NSString` | `location` | The root property of the JSON data where location-data will be appended. |
| [`locationTemplate`](#config-nsstring-locationtemplate-undefined) | `NSString` | `undefined` | Optional custom location data schema (eg: `{ "lat:<%= latitude %>, "lng":<%= longitude %> }`|
| [`geofenceTemplate`](#config-nsstring-geofencetemplate-undefined) | `NSString` | `undefined` | Optional custom geofence data schema (eg: `{ "lat:<%= latitude %>, "lng":<%= longitude %>, "geofence":"<%= geofence.identifier %>:<%= geofence.action %>" }`|
| [`autoSync`](#config-bool-autosync-yes) | `BOOL` | `YES` | If you've enabeld HTTP feature by configuring an [`#url`](#config-string-url-undefined), the plugin will attempt to upload each location to your server **as it is recorded**.|
| [`autoSyncThreshold`](#config-nsinteger-autosyncthreshold-0) | `NSInteger` | `0` | The minimum number of persisted records to trigger an [`#autoSync`](#config-string-autosync-true) action. |
| [`batchSync`](#config-bool-batchsync-no) | `BOOL` | `NO` | If you've enabled HTTP feature by configuring an [`#url`](config-nsstring-url-undefined), [`batchSync: true`](#config-bool-batchsync-no) will POST all the locations currently stored in native SQLite datbase to your server in a single HTTP POST request.|
| [`maxBatchSize`](#config-nsinteger-maxbatchsize--1) | `NSInteger` | `-1` | If you've enabled HTTP feature by configuring an [`#url`](config-nsstring-url-undefined) and [`batchSync: true`](#config-bool-batchsync-no), this parameter will limit the number of records attached to each batch.|
| [`maxDaysToPersist`](#config-nsinteger-maxdaystopersist-1) | `NSInteger` |  `1` |  Maximum number of days to store a geolocation in plugin's SQLite database.|
| [`maxRecordsToPersist`](#config-nsinteger-maxrecordstopersist--1) | `NSInteger` |  `-1` |  Maximum number of records to persist in plugin's SQLite database.  Defaults to `-1` (no limit).  To disable persisting locations, set this to `0`|
| [`locationsOrderDirection`](#config-nsstring-locationsorderdirection-asc) | `String` |  `ASC` |  Controls the order that locations are selected from the database (and synced to your server).  Defaults to ascending (`ASC`), where oldest locations are synced first.  Descending (`DESC`) syncs latest locations first.|


## :wrench: Application Options

### [Application] Common Options

| Option      | Type      | Default   | Note                              |
|-------------|-----------|-----------|-----------------------------------|
| [`stopOnTerminate`](#config-bool-stoponterminate-yes) | `BOOL` |  `YES` | Set `NO` to continue tracking after user teminates the app. |
| [`startOnBoot`](#config-bool-startonboot-no) | `BOOL` | `NO` | Set to `YES` to enable background-tracking after the device reboots. |
| [`heartbeatInterval`](#config-nstimeinterval-heartbeatinterval-60) | `NSTimeInterval` | `60` | Rate in **seconds** to fire [`heartbeat`](#heartbeat) events. |
| [`schedule`](#config-nsarray-schedule-undefined) | `NSArray` | `undefined` | Defines a schedule to automatically start/stop tracking at configured times |
| [`preventSuspend`](#config-bool-preventsuspend-no) | `BOOL` | `NO` | Enable this to prevent **iOS** from suspending your app in the background while in the **stationary state**.  Must be used in conjunction with a [`#heartbeatInterval`](config-nstimeinterval-heartbeatinterval-60).|


## :wrench: Geofencing Options

:blue_book: [Geofencing Guide](geofencing.md)

| Option      | Type      | Default   | Note                              |
|-------------|-----------|-----------|-----------------------------------|
| [`geofenceProximityRadius`](#config-cllocationdistance-geofenceproximityradius-1000) | `CLLocationDistance`  | `1000`  | Radius in **meters** to query for geofences within proximity. |
| [`geofenceInitialTriggerEntry`](#config-bool-geofenceinitialtriggerentry-yes) | `BOOL` | `YES` | Set `NO` to disable triggering a geofence immediately if device is already inside it.|


## :wrench: Logging & Debug Options

:blue_book: [Logging & Debugging Guide](../../../wiki/Debugging)

| Option      | Type      | Default   | Note                              |
|-------------|-----------|-----------|-----------------------------------|
| [`debug`](#config-bool-debug-no) | `BOOL` | `false` | When enabled, the plugin will emit sounds & notifications for life-cycle events of background-geolocation |
| [`logLevel`](#config-tsloglevel-loglevel-tslogleveloff) | `TSLogLevel` | `tsLogLevelOff` | Sets the verbosity of the plugin's logs from `tsLogLevelOff` to `tsLogLevelVerbose` |
| [`logMaxDays`](#config-nsinteger-logmaxdays-3) | `NSInteger` | `3` | Maximum days to persist a log-entry in database. |


# :zap: Events

| Event Name         | Description                                     |
|--------------------|-------------------------------------------------|
| [`location`](#location) | Fired whenever a new location is recorded. |
| [`motionchange`](#motionchange) | Fired when the device changes state between **stationary** and **moving** |
| [`activitychange`](#activitychange) | Fired when the activity-recognition system detects a *change* in detected-activity (`still, on_foot, in_vehicle, on_bicycle, running`) |
| [`providerchange`](#providerchange)| Fired when a change in the state of the device's **Location Services** has been detected.  eg: "GPS ON", "Wifi only".|
| [`geofence`](#geofence) | Fired when a geofence crossing event occurs. |
| [`geofenceschange`](#geofenceschange) | Fired when the list of monitored geofences within [`#geofenceProximityRadius`](#config-cllocationdistance-geofenceproximityradius-1000) changed|
| [`http`](#http) | Fired after a successful HTTP response. `response` object is provided with `status` and `responseText`. |
| [`heartbeat`](#heartbeat) | Fired each [`#heartbeatInterval`](#config-integer-heartbeatinterval-undefined) while the plugin is in the **stationary** state with.  Your callback will be provided with a `params {}` containing the last known `location {Object}` |
| [`schedule`](#schedule) | Fired when a schedule event occurs.  Your `callbackFn` will be provided with the current **`state`** Object. | 
| [`powersavechange`](#powersavechange) | Fired when the state of the operating-system's "Power Saving" system changes.  Your `callbackFn` will be provided with a `Boolean` showing whether "Power Saving" is **enabled** or **disabled** | 

### Adding event-listeners: `#on`

Event-listeners can be attached using the method **`#on`**, supplying the **Event Name** in the following table. **`#on`** accepts both a **`successFn`** and **`failureFn`**.

```javascript
BackgroundGeolocation.on("location", successFn, failureFn);
```

### Removing event-listeners: `#un`

Event-listeners are removed with the method **`#un`**.  You must supply a reference to the *exact* `successFn` reference used with the **`#on`** method:

```javascript
function onLocation(location) { 
  console.log('- location: ', location); 
}
function onLocationError(error) {
  console.log('- location error: ', error);
}
// Add a location listener
BackgroundGeolocation.on('location', onLocation, onLocationError);
.
.
.
// Remove a location listener supplying only the successFn (onLocation)
BackgroundGeolocation.un('location', onLocation);
```


# :large_blue_diamond: Methods

### :small_blue_diamond: Core API Methods

| Method Name      | Arguments       | Notes                                |
|------------------|-----------------|--------------------------------------|
| [`configure`](#configureconfig-successfn-failurefn) | `{config}`, `successFn`, `failureFn` | Initializes the plugin and configures its config options. The **`success`** callback will be executed after the plugin has successfully configured and provided with the current **`state`** `Object`. |
| [`setConfig`](#setconfigconfig-successfn-failurefn) | `{config}`, `successFn`, `failureFn` | Re-configure the plugin with new config options. |
| [`on`](#onevent-successfn-failurefn) | `event`,`successFn`,`failureFn` | Adds an event-listener |
| [`un`](#unevent-callbackfn) | `event`,`callbackFn`, | Removes an event-listener |
| [`start`](#startsuccessfn-failurefn) | `callbackFn`| Enable location tracking.  Supplied **`callbackFn`** will be executed when tracking is successfully engaged.  This is the plugin's power **ON** button. |
| [`stop`](#stopsuccessfn-failurefn) | `callbackFn` | Disable location tracking.  Supplied **`callbackFn`** will be executed when tracking is successfully halted.  This is the plugin's power **OFF** button. |
| [`getState`](#getstatesuccessfn) | `callbackFn` | Fetch the current-state of the plugin, including **`enabled`**, **`isMoving`**, as well as all other config params |
| [`getCurrentPosition`](#getcurrentpositionsuccessfn-failurefn-options) | `successFn`, `failureFn`, `{options}` | Retrieves the current position using maximum power & accuracy by fetching a number of samples and returning the most accurate to your **`callbackFn`**.|
| [`watchPosition`](#watchpositionsuccessfn-failurefn-options) | `successFn`, `failureFn`, `{options}` | Start a stream of continuous location-updates. |
| [`stopWatchPosition`](#stopwatchpositionsuccessfn-failurefn) | `successFn`, `failureFn` | Halt [`#watchPosition`](#watchpositionsuccessfn-failurefn-options) updates. |
| [`changePace`](#changepaceenabled-successfn-failurefn) | `Boolean`, `successFn` | Toggles the plugin's state between **stationary** and **moving**. |
| [`getOdometer`](#getodometercallbackfn-failurefn) | `callbackFn` | The plugin constantly tracks distance travelled. The supplied callback will be executed and provided with the **`distance`** (meters) as the 1st parameter.|
| [`setOdometer`](#setodometervalue-callbackfn-failurefn) | `Integer`, `callbackFn` | Set the `odometer` to *any* arbitrary value.  **NOTE** `setOdometer` will perform a `getCurrentPosition` in order to record to exact location where odometer was set; as a result, the `callback` signatures are identical to those of `getCurrentPosition`.|
| [`resetOdometer`](#resetodometercallbackfn-failurefn) | `callbackFn` | Reset the **odometer** to `0`.  Alias for [`setOdometer(0)`](#setodometervalue-callbackfn-failurefn) |
| [`startSchedule`](#startschedulecallbackfn) | `callbackFn` | If a [`schedule`](#config-array-schedule-undefined) was configured, this method will initiate that schedule.|
| [`stopSchedule`](#stopschedulecallbackfn) | `callbackFn` | This method will stop the Scheduler service.  Your **`callbackFn`** will be executed after the Scheduler has stopped |
| [`removeListeners`](#removelistenerssuccessfn-failurefn) | `none` | Remove all events-listeners registered with **`#on`** method |
| [`startBackgroundTask`](#startbackgroundtaskcallbackfn) | `callbackFn` | Sends a signal to the native OS that you wish to perform a long-running task.  The OS will not suspend your app until you signal completion with the **`#finish`** method.|
| [`finish`](#finishtaskid) | `taskId` | Sends a signal to the native OS the supplied **`taskId`** is complete and the OS may proceed to suspend your application if applicable.|
| [`isPowerSaveMode`](#ispowersavemodecallbackfn) | `callbackFn` | Fetches the state of the operating-systems "Power Saving" mode, whether `enabled` or `disabled`|


### :small_blue_diamond: HTTP & Persistence Methods

| Method Name      | Arguments       | Notes                                |
|------------------|-----------------|--------------------------------------|
| [`getLocations`](#getlocationssuccessfn-failurefn) | `callbackFn` | Fetch all the locations currently stored in native plugin's SQLite database. Your **`callbackFn`** will receive an **`Array`** of locations in the 1st parameter |
| [`getCount`](#getcountsuccessfn-failurefn) | `callbackFn` | Fetches count of SQLite locations table **`SELECT count(*) from locations`** |
| [`destroyLocations`](#destroylocationssuccessfn-failurefn) | `callbackFn` | Delete all records in plugin's SQLite database |
| [`sync`](#syncsuccessfn-failurefn) | `successFn`, `failureFn` | If the plugin is configured for HTTP with an [`#url`](#config-string-url-undefined) and [`#autoSync: false`](#config-string-autosync-true), this method will initiate POSTing the locations currently stored in the native SQLite database to your configured [`#url`](#config-string-url-undefined)|


### :small_blue_diamond: Geofencing Methods

| Method Name      | Arguments       | Notes                                |
|------------------|-----------------|--------------------------------------|
| [`startGeofences`](#startgeofencescallbackfn) | `callbackFn` | Engages the geofences-only **`trackingMode`**.  In this mode, no active location-tracking will occur -- only geofences will be monitored|
| [`addGeofence`](#addgeofenceconfig-successfn-failurefn) | `{config}`, `successFn`, `failureFn` | Adds a geofence to be monitored by the native plugin.|
| [`addGeofences`](#addgeofencesgeofences-successfn-failurefn) | `[geofences]`, `sucessFn`, `failureFn` | Adds a list geofences to be monitored by the native plugin. |
| [`removeGeofence`](#removegeofenceidentifier-successfn-failurefn) | `identifier`, `successFn`, `failureFn` | Removes a geofence identified by the provided `identifier` |
| [`removeGeofences`](#removegeofencescallbackfn-failurefn) | `successFn`, `failureFn` | Removes all geofences |
| [`getGeofences`](#getgeofencessuccessfn-failurefn) | `callbackFn` | Fetch the list of monitored geofences. |


### :small_blue_diamond: Logging Methods

| Method Name      | Arguments       | Notes                                |
|------------------|-----------------|--------------------------------------|
| [`setLogLevel`](#setloglevelloglevel-callbackfn) | `Integer`, `callbackFn` | Set the Log filter:  `LOG_LEVEL_OFF`, `LOG_LEVEL_ERROR`, `LOG_LEVEL_WARNING`, `LOG_LEVEL_INFO`, `LOG_LEVEL_DEBUG`, `LOG_LEVEL_VERBOSE`|
| [`getLog`](#getlogcallbackfn) | `callbackFn` | Fetch the entire contents of the current log database as a `String`.|
| [`destroyLog`](#destroylogsuccessfn-failurefn) | `callbackFn`, `failureFn` | Destroy the contents of the Log database. |
| [`emailLog`](#emaillogemail-callbackfn) | `email`, `callbackFn` | Fetch the entire contents of Log database and email it to a recipient using the device's native email client.|
| [`getSensors`](#getsensorscallbackfn-failurefn) | `callbackFn`, `failureFn` | Returns the presense of device sensors *accelerometer*, *gyroscope*, *magnetometer*, in addition to iOS/Android-specific sensors|
| [`logger.error`](#logger) | `message` | Record a :exclamation: log message into the plugin's log database. |
| [`logger.warn`](#logger) | `message` | Record a :warning: log message into the plugin's log database. |
| [`logger.debug`](#logger) | `message` | Record a :beetle: log message into the plugin's log database. |
| [`logger.info`](#logger) | `message` | Record a :information_source: log message into the plugin's log database. |
| [`logger.notice`](#logger) | `message` | Record a :large_blue_circle: log message into the plugin's log database. |
| [`logger.header`](#logger) | `message` | Record a header log message into the plugin's log database. |
| [`logger.on`](#logger) | `message` | Record a :tennis: log message into the plugin's log database. |
| [`logger.off`](#logger) | `message` | Record a :red_circle: log message into the plugin's log database. |
| [`logger.ok`](#logger) | `message` | Record a :white_check_mark: log message into the plugin's 
| [`playSound`](#playsoundsoundid) | `Integer` | Here's a fun one.  The plugin can play a number of OS system sounds for each platform.  For [IOS](http://iphonedevwiki.net/index.php/AudioServices) and [Android](http://developer.android.com/reference/android/media/ToneGenerator.html).  I offer this API as-is, it's up to you to figure out how this works. |


# :wrench: Geolocation Options

#### `@config {CLLocationAccuracy} desiredAccuracy [kCLLocationAccuracyBest]`

Specify the desired-accuracy of the geolocation system with.

```javascript
BackgroundGeoloction.configure({
  desiredAccuracy: BackgroundGeolocation.DESIRED_ACCURACY_HIGH
});
```

:warning: Only **`kCLLocationAccuracyBest`** &amp; **`kCLLocationAccuracyBestForNavigation`** use GPS.  `speed`, `heading` and `altitude` are available only from GPS.

:blue_book: For platform-specific information about location accuracy, see the corresponding API docs:

- [iOS](https://developer.apple.com/reference/corelocation/cllocationmanager/1423836-desiredaccuracy?language=objc) 

------------------------------------------------------------------------------

#### `@config {CLLocationDistance} distanceFilter [10]`

The minimum distance (measured in meters) a device must move horizontally before an update event is generated.

However, by default, **`distanceFilter`** is elastically auto-calculated by the plugin:  When speed increases, **`distanceFilter`** increases;  when speed decreases, so too does **`distanceFilter`**.  

:information_source: To disable this behaviour, configure [`disableElasticity: true`](#config-boolean-disableelasticity-false)

:information_source: To control the scale of the automatic `distanceFilter` calculation, see [`elasticityMultiplier`](#config-float-elasticitymultiplier-1)

**`distanceFilter`** is auto calculated by rounding speed to the nearest `5 m/s` and adding **`distanceFilter`** meters for each `5 m/s` increment.

For example, at biking speed of 7.7 m/s with a configured **`distanceFilter: 30`**:

```
  rounded_speed = round(7.7, 5)
  => 10
  multiplier = rounded_speed / 5
  => 10 / 5 = 2
  adjusted_distance_filter = multiplier * distanceFilter
  => 2 * 30 = 60 meters
```

At highway speed of `27 m/s` with a configured `distanceFilter: 50`:

```
  rounded_speed = round(27, 5)
  => 30
  multiplier = rounded_speed / 5
  => 30 / 5 = 6
  adjusted_distance_filter = multiplier * distanceFilter * elasticityMultipiler
  => 6 * 50 = 300 meters
```

Note the following real example of background-geolocation on highway 101 towards San Francisco as the driver slows down as he runs into slower traffic (geolocations become compressed as distanceFilter decreases)

![distanceFilter at highway speed](https://dl.dropboxusercontent.com/s/uu0hs0sediw26ar/distance-filter-highway.png?dl=1)

Compare now background-geolocation in the scope of a city.  In this image, the left-hand track is from a cab-ride, while the right-hand track is walking speed.

![distanceFilter at city scale](s/yx8uv2zsimlogsp/distance-filter-city.png?dl=1)

------------------------------------------------------------------------------

#### `@config {BOOL} disableElasticity [NO]`

Defaults to **`NO`**.  Set **`YES`** to disable automatic, speed-based [`#distanceFilter`](#config-integer-distancefilter) elasticity.

------------------------------------------------------------------------------

#### `@config {double} elasticityMultiplier [1]`

Controls the scale of automatic speed-based [`#distanceFilter`](#config-cllocationdistance-distancefilter) elasticity.  Increasing `elasticityMultiplier` will result in fewer location samples as speed increases.  A value of `0` has the same effect as [`disableElasticity: true`](#config-boolean-disableelasticity-false)

------------------------------------------------------------------------------


#### `@config {CLLocationDistance} stationaryRadius [25]`

When stopped, the minimum distance the device must move beyond the stationary location for aggressive background-tracking to engage.

Configuring **`stationaryRadius: 0`** has **NO EFFECT** (in fact the plugin enforces a minimum **``stationaryRadius``** of `25`).

The following image shows the typical distance iOS requires to detect exit of the **`stationaryRadius`**, where the *green* polylines represent a transition from **stationary** state to **moving** and the *red circles* locations where the plugin entered the **stationary** state.:

![](https://dl.dropboxusercontent.com/s/vnio90swhs6xmqm/screenshot-ios-stationary-exit.png?dl=1)

:blue_book: For more information, see [Philosophy of Operation](../../../wiki/Philosophy-of-Operation)

:warning: iOS will not detect the exact moment the device moves out of the stationary-radius.  In normal conditions, it will typically take **~200 meters** before the plugin begins tracking.  

------------------------------------------------------------------------------


#### `@config {double} stopAfterElapsedMinutes [-1]`

The plugin can optionally automatically stop tracking after some number of minutes elapses after the `#start` method was called.

```javascript
BackgroundGeolocation.configure({
  stopAfterElapsedMinutes: 30
}, function(state) {
  BackgroundGeolocation.start();  // <-- plugin will automatically #stop in 30 minutes
});
```

------------------------------------------------------------------------------

#### `@config {BOOL} stopOnStationary [NO]`

The plugin can optionally automatically stop tracking when the `stopTimeout` timer elapses.  For example, when the plugin first detects a `motionchange` into the "moving" state, the next time a `motionchange` event occurs into the "stationary" state, the plugin will have automatically called `#stop` upon itself.

:warning: `stopOnStationary` will **only** occur due to `stopTimeout` timer elapse.  It will **not** occur by manually executing `changePace:NO`.

```javascript
BackgroundGeolocation.configure({
  stopOnStationary: true
}, function(state) {
  BackgroundGeolocation.start();
});
```

------------------------------------------------------------------------------

#### `@config {CLLocationAccuracy} desiredOdometerAccuracy [100]`

Specify an accuracy threshold in **meters** for odometer calculations.  Defaults to `100`.  If a location arrives having **`accuracy > desiredOdometerAccuracy`**, that location will not be used to update the odometer.  If you only want to calculate odometer from GPS locations, you could set **`desiredOdometerAccuracy: 10`**.  This will prevent odometer updates when a device is moving around indoors, in a shopping mall, for example.

------------------------------------------------------------------------------


#### `@config {BOOL} useSignificantChangesOnly [NO]`

Defaults to `NO`.  Set `YES` in order to disable constant background-tracking and use only the iOS [Significant Changes API](https://developer.apple.com/reference/corelocation/cllocationmanager/1423531-startmonitoringsignificantlocati?language=objc).  

:warning: If Apple has denied your application, refusing to grant your app the privelege of using the **`UIBackgroundMode: "location"`**, this can be a solution.  **NOTE** The Significant Changes API will report a location only every `500` to `1000` meters.  Many of the plugin's configuration parameters **will be ignored**, such as [`#distanceFilter`](#config-integer-distancefilter), [`#stationaryRadius`](#config-cllocationdistance-stationaryradius-meters), [`#activityType`](#config-clactivitytype-activitytype-clactivitytypeother), etc.

------------------------------------------------------------------------------

#### `@config {BOOL} pausesLocationUpdatesAutomatically [YES]`

:warning: This option should generally be left `undefined`.  You should only specifiy this option if you know *exactly* what you're doing.

The default behaviour of the plugin is to turn **off** location-services *automatically* when the device is detected to be stationary.  When set to `false`, location-services will **never** be turned off (and `disableStopDetection` will automatically be set to `true`) -- it's your responsibility to turn them off when you no longer need to track the device.  This feature should **not** generally be used.  `preventSuspend` will no longer work either.

------------------------------------------------------------------------------

#### `@config {NSString} locationAuthorizationRequest [Always]`

The desired iOS location-authorization request, either **`Always`** or **`WhenInUse`**.  **`locationAuthorizationRequest`** tells the plugin the mode it *expects* to be in &mdash; if the user changes this mode in their settings, the plugin will detect this (@see [`locationAuthorizationAlert`](#config-object-locationauthorizationalert)).  Defaults to **`Always`**.  **`WhenInUse`** will display a **blue bar** at top-of-screen informing user that location-services are on.

:warning: Configuring **`WhenInUse`** will disable many of the plugin's features, since iOS forbids any API which operates in the background to operate (such as **geofences**, which the plugin relies upon to automatically engage background tracking).

------------------------------------------------------------------------------

#### `@config {NSDictionary} locationAuthorizationAlert`

When you configure the plugin location-authorization `Always` or `WhenInUse` and the user changes the value in the app's location-services settings or disabled location-services, the plugin will display an Alert directing the user to the **Settings** screen.  This config allows you to configure all the Strings for that Alert popup and accepts an `{Object}` containing the following keys:

##### `@config {NSString} titleWhenOff [Location services are off]`  The title of the alert if user changes, for example, the location-request to `WhenInUse` when you requested `Always`.

##### `@config {NSString} titleWhenNotEnabled [Background location is not enabled]`  The title of the alert when user disables location-services or changes the authorization request to `Never`

##### `@config {NSString} instructions [To use background location, you must enable {locationAuthorizationRequest} in the Location Services settings]`  The body text of the alert.

##### `@config {NSString} cancelButton [Cancel]` Cancel button label

##### `@config {NSString} settingsButton [Settings]` Settings button label

![](s/wyoaf16buwsw7ed/docs-locationAuthorizationAlert.jpg?dl=1)

```javascript
BackgroundGeolocation.configure({
  locationAuthorizationAlert: {
    titleWhenNotEnabled: "Yo, location-services not enabled",
    titleWhenOff: "Yo, location-services OFF",
    instructions: "You must enable 'Always' in location-services, buddy",
    cancelButton: "Cancel",
    settingsButton: "Settings"
  }
})
```

------------------------------------------------------------------------------

# :wrench: Activity Recognition Options

#### `@config {double millis} [10000] activityRecognitionInterval`

Defaults to `10000` (10 seconds).  The desired time between activity detections. Larger values will result in fewer activity detections while improving battery life. A value of 0 will result in activity detections at the fastest possible rate.

------------------------------------------------------------------------------

#### `@config {NSInteger} minimumActivityRecognitionConfidence [75]` 

Each activity-recognition-result returned by the API is tagged with a "confidence" level expressed as a %.  You can set your desired confidence to trigger a [`motionchange`](#motionchange) event.  Defaults to **`75`**.

------------------------------------------------------------------------------

#### `@config {double} stopTimeout [5]`

When in the **moving** state, specifies the number of minutes to wait before turning off location-services and enter **stationary** state after the ActivityRecognition System detects the device is `STILL` (defaults to 5min).  If you don't set a value, the plugin is eager to turn off the GPS ASAP.  An example use-case for this configuration is to delay GPS OFF while in a car waiting at a traffic light.

:blue_book: See [Philosophy of Operation](../../../wiki/Philosophy-of-Operation)

:warning: Setting a value > 15 min is **not** recommended, particularly for Android.

------------------------------------------------------------------------------

#### `@config {BOOL} disableStopDetection [NO]`

Disables the accelerometer-based **Stop-detection System**.  When disabled, the plugin will use the default iOS behaviour of automatically turning off location-services when the device has stopped for exactly 15 minutes.  When disabled, you will no longer have control over [`#stopTimeout`](#config-integer-minutes-stoptimeout).

**iOS Stop-detection timing**.
![](https://dl.dropboxusercontent.com/s/ojjdfkmua15pskh/ios-stop-detection-timing.png?dl=1)

## :wrench: [Activity Recognition] iOS Options


#### `@config {CLActivityType} activityType [CLActivityTypeOther]`

Presumably, this affects ios GPS algorithm.

:blue_book: [Apple docs](https://developer.apple.com/reference/corelocation/cllocationmanager/1620567-activitytype?language=objc).

------------------------------------------------------------------------------

#### `@config {double} stopDetectionDelay [0]` 

Measured in **minutes**; defaults to **`0`**.  Allows the stop-detection system to be delayed from activating.  When the stop-detection system *is* engaged, location-services will be turned **off** and only the accelerometer is monitored.  Stop-detection will only engage if this timer expires.  The timer is cancelled if any movement is detected before expiration.  If a value of **`0`** is specified, the stop-detection system will engage as soon as the device is detected to be stationary.

------------------------------------------------------------------------------

#### `@config {BOOL} disableMotionActivityUpdates [NO]`

Defaults to **`NO`**.  Set **`YES`** to disable iOS [`CMMotionActivityManager`](https://developer.apple.com/reference/coremotion/cmmotionactivitymanager) updates (eg: `walking`, `in_vehicle`).  This feature requires a device having the **M7** co-processor (ie: iPhone 5s and up).

:information_source: This feature will ask the user for "Health updates" permission using the **[`MOTION_USAGE_DESCRIPTION`](https://github.com/transistorsoft/cordova-background-geolocation#configuring-the-plugin)**.  If you do not wish to ask the user for the "Health updates", set this option to `true`; However, you will no longer receive accurate activity data in the recorded locations.

:warning: The plugin is **HIGHLY** optimized for motion-activity-updates.  If you **do** disable this, the plugin *will* drain more battery power.  You are **STRONGLY** advised against disabling this.  You should explain to your users with the **[`MOTION_USAGE_DESCRIPTION`](https://github.com/transistorsoft/cordova-background-geolocation#configuring-the-plugin)**, for example:

> "Accelerometer use increases battery efficiency by intelligently toggling location-tracking"

# :wrench: Geofencing Options

#### `@config {CLLocationDistance} geofenceProximityRadius [1000]`

Defaults to `1000` meters.  **@see** releated event [`geofenceschange`](#geofenceschange).  When using Geofences, the plugin activates only thoses in proximity (the maximim geofences allowed to be simultaneously monitored is limited by the platform, where **iOS** allows only 20 and **Android**.  However, the plugin allows you to create as many geofences as you wish (thousands even).  It stores these in its database and uses spatial queries to determine which **20** or **100** geofences to activate.

:blue_book: [See Geofencing Guide](geofencing.md)

:tv: [View animation of this behaviour](https://dl.dropboxusercontent.com/u/2319755/background-geolocation/images/background-geolocation-infinite-geofencing.gif)

![](s/7sggka4vcbrokwt/geofenceProximityRadius_iphone6_spacegrey_portrait.png?dl=1)

------------------------------------------------------------------------------

#### `@config {BOOL} geofenceInitialTriggerEntry [YES]`

Defaults to `YES`.  Set `NO` to disable triggering a geofence immediately if device is already inside it.

------------------------------------------------------------------------------


# :wrench: HTTP & Persistence Options


#### `@config {NSString} url [""]`

Your server **`url`** where you wish to HTTP POST location data to.

```javascript
BackgroundGeolocation.configure({
  url: 'http://my-server.com/locations'
});
```

:blue_book: See [HTTP Guide](http.md) for more information.

:warning: It is highly recommended to let the plugin manage uploading locations to your server, **particularly for Android** when configured with **`stopOnTerminate: false`**, since your Cordova app (where your Javascript lives) *will* terminate &mdash; only the plugin's native Android background service will continue to operate, recording locations and uploading to your server.  The plugin's native HTTP service *is* better at this task than Javascript Ajax requests, since the plugin will automatically retry on server failure.

------------------------------------------------------------------------------

#### `@config {NSInteger} httpTimeout [60000]`

HTTP request timeout in **milliseconds**.  The `http` **`failureFn`** will execute when an HTTP timeout occurs.  Defaults to `60000 ms` (1 minute).

```javascript
BackgroundGeolocation.on('http', function(request) {
  console.log('HTTP SUCCESS: ', response);
}, function(request) {
  console.log('HTTP FAILURE', response);
});

BackgroundGeolocation.configure({
  url: 'http://my-server.com/locations',
  httpTimeout: 3000
});
```

------------------------------------------------------------------------------

#### `@config {NSString} method [POST]`

The HTTP method to use when creating an HTTP request to your configured [`#url`](#config-string-url-undefined).  Defaults to `POST`.  Valid values are `POST`, `PUT` and `OPTIONS`.

```javascript
BackgroundGeolocation.configure({
  url: 'http://my-server.com/locations',
  method: 'PUT'
});
```

------------------------------------------------------------------------------

#### `@config {NSDictionary} params`

Optional HTTP **`params`** sent along in each HTTP request.

```javascript
BackgroundGeolocation.configure({
  url: 'http://my-server.com/locations',
  params: {
    user_id: 1234,
    device_id: 'abc123'
  }
});
```

```javascript
POST /locations
 {
  "location": {
    "coords": {
      "latitude": 45.51927004945047,
      "longitude": -73.61650072045029
      .
      .
      .
    }
  },
  "user_id": 1234,
  "device_id": 'abc123'
}

```

------------------------------------------------------------------------------

#### `@config {NSDictionary} headers`

Optional HTTP params sent along in HTTP request to above [`#url`](#config-string-url-undefined).

------------------------------------------------------------------------------

#### `@config {NSString} httpRootProperty [location]`

The root property of the JSON data where location-data will be placed.

:blue_book: See [HTTP Guide](http.md) for more information.

```json
{
    "rootProperty":{
        "coords": {
            "latitude":23.232323,
            "longitude":37.373737
        }
    }
}
```

You may also specify the character **`httpRootProperty:"."`** to place your data in the *root* of the JSON:

```json
{
    "coords": {
        "latitude":23.232323,
        "longitude":37.373737
    }
}
```

------------------------------------------------------------------------------

#### `@config {NSString} locationTemplate [undefined]`

Optional custom template for rendering `location` JSON request data in HTTP requests.  Evaulate variables in your **`locationTemplate`** using Ruby `erb`-style tags:

```erb
<%= variable_name %>
```

:blue_book: See [HTTP Guide](http.md) for more information.

```javascript
BackgroundGeolocation.configure({
  locationTemplate: '{"lat":<%= latitude %>,"lng":<%= longitude %>,"event":"<%= event %>",isMoving:<%= isMoving %>}'
});

// Or use a compact [Array] template!
BackgroundGeolocation.configure({
  locationTemplate: '[<%=latitude%>, <%=longitude%>, "<%=event%>", <%=is_moving%>]'
})
```

:warning: If you've configured [`#extras`](#config-object-extras), these key-value pairs will be merged *directly* onto your location data.  Eg:

```javascript
BackgroundGeolocation.configure({
  httpRootProperty: 'data',
  locationTemplate: '{"lat":<%= latitude %>,"lng":<%= longitude %>}',
  extras: {
    "foo":"bar"
  }
})
```

Will result in JSON:
```json
{
    "data": {
        "lat":23.23232323,
        "lng":37.37373737,
        "foo":"bar"
    }
}
```

**Template Tags**

| Tag | Type | Description |
|-----|------|-------------|
| `latitude` | `Float` ||
| `longitude` | `Float` ||
| `speed` | `Float` | Meters|
| `heading` | `Float` | Degress|
| `accuracy` | `Float` | Meters|
| `altitude` | `Float` | Meters|
| `altitude_accuracy` | `Float` | Meters|
| `timestamp` | `String` |ISO-8601|
| `uuid` | `String` |Unique ID|
| `event` | `String` |`motionchange|geofence|heartbeat`
| `odometer` | `Float` | Meters|
| `activity.type` | `String` | `still|on_foot|running|on_bicycle|in_vehicle|unknown`|
| `activity.confidence` | `Integer` | 0-100%|
| `battery.level` | `Float` | 0-100%|
| `battery.is_charging` | `Boolean` | Is device plugged in?|

------------------------------------------------------------------------------

#### `@config {NSString} geofenceTemplate [undefined]`

Optional custom template for rendering `geofence` JSON request data in HTTP requests.  The `geofenceTemplate` is similar to [`#locationTemplate`](#config-string-locationtemplate-undefined) with the addition of two extra `geofence.*` tags.

Evaulate variables in your **`geofenceTemplate`** using Ruby `erb`-style tags:

```erb
<%= variable_name %>
```

:blue_book: See [HTTP Guide](http.md) for more information.

```javascript
BackgroundGeolocation.configure({
  geofenceTemplate: '{ "lat":<%= latitude %>, "lng":<%= longitude %>, "geofence":"<%= geofence.identifier %>:<%= geofence.action %>" }'
});

// Or use a compact [Array] template!
BackgroundGeolocation.configure({
  geofenceTemplate: '[<%= latitude %>, <%= longitude %>, "<%= geofence.identifier %>", "<%= geofence.action %>"]'
})

```

**Template Tags**
The tag-list is identical to [`#locationTemplate`](#config-string-locationtemplate-undefined) with the addition of `geofence.identifier` and `geofence.action`.  

| Tag | Type | Description |
|-----|------|-------------|
| **`geofence.identifier`** | `String` | Which geofence?|
| **`geofence.action`** | `String` | `ENTER|EXIT`|
| `latitude` | `Float` ||
| `longitude` | `Float` ||
| `speed` | `Float` | Meters|
| `heading` | `Float` | Degress|
| `accuracy` | `Float` | Meters|
| `altitude` | `Float` | Meters|
| `altitude_accuracy` | `Float` | Meters|
| `timestamp` | `String` |ISO-8601|
| `uuid` | `String` |Unique ID|
| `event` | `String` |`motionchange|geofence|heartbeat`
| `odometer` | `Float` | Meters|
| `activity.type` | `String` | `still|on_foot|running|on_bicycle|in_vehicle|unknown`
| `activity.confidence` | `Integer` | 0-100%|
| `battery.level` | `Float` | 0-100%|
| `battery.is_charging` | `Boolean` | Is device plugged in?|

------------------------------------------------------------------------------

#### `@config {BOOL} batchSync [NO]`

Default is **`NO`**.  If you've enabled HTTP feature by configuring an [`#url`](#config-string-url-undefined), [`batchSync: YES`](#config-string-batchsync-false) will POST *all* the locations currently stored in native SQLite datbase to your server in a single HTTP POST request.  With [`batchSync: false`](#config-string-batchsync-false), an HTTP POST request will be initiated for **each** location in database.

------------------------------------------------------------------------------

#### `@config {NSInteger} maxBatchSize [-1]`

If you've enabled HTTP feature by configuring an [`#url`](#config-string-url-undefined) with [`batchSync: true`](#config-string-batchsync-false), this parameter will limit the number of records attached to **each** batch request.  If the current number of records exceeds the **`maxBatchSize`**, multiple HTTP requests will be generated until the location queue is empty.

------------------------------------------------------------------------------

#### `@config {BOOL} autoSync [YES]`

Default is `YES`.  If you've enabeld HTTP feature by configuring an [`#url`](#config-string-url-undefined), the plugin will attempt to HTTP POST each location to your server **as it is recorded**.  If you set [`autoSync: NO`](#config-string-autosync-true), it's up to you to **manually** execute the [`#sync`](synccallbackfn-failurefn) method to initate the HTTP POST (**NOTE** The plugin will continue to persist **every** recorded location in the SQLite database until you execute [`#sync`](synccallbackfn-failurefn)).

------------------------------------------------------------------------------

#### `@config {NSInteger} autoSyncThreshold [0]`

The minimum number of persisted records to trigger an [`autoSync`](#config-string-autosync-true) action.  If you configure a value greater-than **`0`**, the plugin will wait until that many locations are recorded before executing HTTP requests to your server through your configured [`#url`](#config-string-url-undefined).

------------------------------------------------------------------------------

#### `@config {NSDictionary} extras`

Optional arbitrary key/value `{}` to attach to each recorded location

Eg: Every recorded location will have the following **`extras`** appended:

:blue_book: See [HTTP Guide](http.md) for more information.

```javascript
BackgroundGeolocation.configure({
  url: 'http://my-server.com/locations',
  extras: {
    route_id: 1234
  },
  params: {
    device_id: 'abc123'
  }
});
```

```javascript
- POST /locations
{
  "device_id": "abc123" // <-- params appended to root of JSON
  "location": {
    "coords": {
      "latitude": 45.51927004945047,
      "longitude": -73.61650072045029,
      .
      .
      .
    },
    "extras": {  // <-- extras appended to *each* location
      "route_id": 1234
    }
  }
}

```

------------------------------------------------------------------------------

#### `@config {NSInteger} maxDaysToPersist [1]`

Maximum number of days to store a geolocation in plugin's SQLite database when your server fails to respond with **`HTTP 200 OK`**.  The plugin will continue attempting to sync with your server until **`maxDaysToPersist`** when it will give up and remove the location from the database.

------------------------------------------------------------------------------

#### `@config {NSInteger} maxRecordsToPersist [-1]`

Maximum number of records to persist in plugin's SQLite database.  Default `-1`
 means **no limit**.

------------------------------------------------------------------------------

#### `@config {NSString} locationsOrderDirection [ASC]`

Controls the order that locations are selected from the database (and synced to your server).  Defaults to ascending (`ASC`), where oldest locations are synced first.  Descending (`DESC`) syncs latest locations first.|

------------------------------------------------------------------------------


# :wrench: Application Options

#### `@config {BOOL} stopOnTerminate [YES]`

Defaults to **`YES`**.  When the user terminates the app, the plugin will **stop** tracking.  Set this to **`NO`** to continue tracking after application terminate.

If you *do* configure **`stopOnTerminate: NO`**, your Javascript application **will** terminate at that time.  However, both Android and iOS differ in their behaviour *after* this point:

Before an iOS app terminates, the plugin will ensure that a **stationary geofence** is created around the last known position.  When the user moves beyond the stationary geofence (typically ~200 meters), iOS will completely reboot your application in the background, including your Javascript application and the plugin will resume tracking.  iOS maintains geofence monitoring at the OS level, in spite of application terminate / device reboot.

In the following image, imagine the user terminated the application at the **"red circle"** on the right then continued moving:  Once the device moves by about 200 meters, exiting the "stationary geofence", iOS reboots the app and tracking resumes.

:information_source: [Demo Video of `stopOnTerminate: false`](https://www.youtube.com/watch?v=aR6r8qV1TI8&t=214s)

![](https://dl.dropboxusercontent.com/s/1uip231l3gds68z/screenshot-stopOnTerminate-ios.png?dl=0)

------------------------------------------------------------------------------

#### `@config {BOOL} startOnBoot [NO]`

Defaults to **`NO`**.  Set **`YES`** to engage background-tracking after the device reboots.

iOS cannot **immediately** engage tracking after a device reboot.  Just like [`stopOnTerminate:false`](config-bool-stoponterminate-yes), iOS will not re-boot your app until the device moves beyond the **stationary geofence** around the last known location.  In addition, iOS subscribes to "background-fetch" events, which typically fire about every 15 minutes &mdash; these too are capable of rebooting your app after a device reboot.

------------------------------------------------------------------------------


#### `@config {NSTimeInterval} heartbeatInterval [60]`

Controls the rate (in seconds) the [`heartbeat`](#heartbeat) event will fire.  The plugin will **not** provide any updated locations to your **`callbackFn`**, since it will provide only the last-known location.  If you wish for an updated location in your **`callbackFn`**, it's up to you to request one with [`#getCurrentPosition`](#getcurrentpositionsuccessfn-failurefn-options).

:warning: On **iOS** the **`heartbeat`** event will fire only when configured with [`preventSuspend: YES`](config-bool-preventsuspend-no)

```javascript
BackgroundGeolocation.on('heartbeat', function(params) {
  var lastKnownLocation = params.location;
  console.log('- heartbeat: ', lastKnownLocation);
  // Or you could request a new location
  BackgroundGeolocation.getCurrentPosition(function(location) {
    console.log('- current position: ', location);
  });
});
```

------------------------------------------------------------------------------

#### `@config {NSArray} schedule [undefined]`

Provides an automated schedule for the plugin to start/stop tracking at pre-defined times.  The format is cron-like:

```javascript
  "{DAY(s)} {START_TIME}-{END_TIME}"
```

The `START_TIME`, `END_TIME` are in **24h format**.  The `DAY` param corresponds to the `Locale.US`, such that **Sunday=1**; **Saturday=7**).  You may configure a single day (eg: `1`), a comma-separated list-of-days (eg: `2,4,6`) or a range (eg: `2-6`), eg:


```javascript
BackgroundGeolocation.configure({
  .
  .
  .
  schedule: [
    '1 17:30-21:00',   // Sunday: 5:30pm-9:00pm
    '2-6 9:00-17:00',  // Mon-Fri: 9:00am to 5:00pm
    '2,4,6 20:00-00:00',// Mon, Web, Fri: 8pm to midnight (next day)
    '7 10:00-19:00'    // Sat: 10am-7pm
  ]
}, function(state) {
  // Start the Scheduler
  BackgroundGeolocation.startSchedule(function() {
    console.info('- Scheduler started');
  });
});

// Listen to "schedule" events:
BackgroundGeolocation.on('schedule', function(state) {
  console.log('- Schedule event, enabled:', state.enabled);

  if (!state.schedulerEnabled) {
    BackgroundGeolocation.startSchedule();
  }
});

// Later when you want to stop the Scheduler (eg: user logout)
BackgroundGeolocation.stopSchedule(function() {
  console.info('- Scheduler stopped');
  // You must explicitly stop tracking if currently enabled
  BackgroundGeolocation.stop();
});

// Or modify the schedule with usual #setConfig method
BackgroundGeolocation.setConfig({
  schedule: [
    '1-7 9:00-10:00',
    '1-7 11:00-12:00',
    '1-7 13:00-14:00',
    '1-7 15:00-16:00',
    '1-7 17:00-18:00',
    '2,4,6 19:00-22:00'
  ]
});
```

##### Literal Dates

The schedule can also be configured with a literal start date of the form:

```
  "yyyy-mm-dd HH:mm-HH:mm"
```

eg:

```javascript
BackgroundGeolocation.configure({
  schedule: [
    "2018-01-01 09:00-17:00"
  ]

})
```

Or **two** literal dates to specify both a start **and** stop date (note the format here is a bit ugly):

```
  "yyyy-mm-dd-HH:mm yyyy-mm-dd-HH:mm"
```

```javascript
schedule: [
    "2018-01-01-09:00 2019-01-01-17:00"  // <-- track for 1 year
  ]
```


**iOS**

iOS **cannot** evaluate the Schedule at the *exact* time you configure &mdash; it can only evaluate the **`schedule`** *periodically*, whenever your app comes alive.  When the app is running in a scheduled **off** period, iOS will continue to monitor the low-power, [significant location changes API (SLC)](https://developer.apple.com/reference/corelocation/cllocationmanager/1423531-startmonitoringsignificantlocati?language=objc) in order to ensure periodic schedule evaluation.  **SLC** is required in order guarantee periodic schedule-evaluation when you're configured [`stopOnTerminate: false`](#config-boolean-stoponterminate-true), since the [iOS Background Fetch]() is halted if user *manually* terminates the app.  **SLC** will awaken your app whenever a "significant location change" occurs, typically every `1000` meters.  If the schedule is currently in an **off** period, this location will **not** be persisted nor will it be sent to the [`location`](#location) event &mdash; only the **`schedule`** will be evaluated.

When a **`schedule`** is provided on iOS, it will be evaluated in the following cases:

- Application `pause` / `resume` events.
- Whenever a location is recorded (including **SLC**)
- Background fetch event

------------------------------------------------------------------------------


#### `@config {BOOL} preventSuspend [NO]`

Defaults to **`NO`**.  Set **`YES`** to prevent **iOS** from suspending after location-services have been switched off while your application is in the background.  Must be used in conjunction with a [`heartbeatInterval`](config-nstimeinterval-heartbeatinterval-60).

:warning: **`preventSuspend: true`** should **only** be used in **very** specific use-cases and should typically **not** be used as it *will* have a **very noticable impact on battery performance.**  You should carefully manage **`preventSuspend`**, engaging it for controlled periods-of-time.  You should **not** expect to run your app in this mode 24 hours / day, 7 days-a-week.

------------------------------------------------------------------------------


# :wrench: Logging & Debug Options

:blue_book: [Logging & Debugging Guide](../../../wiki/Debugging)

#### `@config {BOOL} debug [NO]`

Defaults to **`NO`**.  When set to **`YES`**, the plugin will emit debugging sounds and notifications for life-cycle events of background-geolocation!

In you wish to hear debug sounds in the background, you must manually enable the **[x] Audio and Airplay** background mode in *Background Capabilities* of XCode.

![](https://dl.dropboxusercontent.com/s/iplaxheoq63oul6/Screenshot%202017-02-20%2012.10.57.png?dl=1)

:blue_book: See [Debugging Sounds](../../../wiki/Debug-Sounds)

------------------------------------------------------------------------------

#### `@config {TSLogLevel} logLevel [tsLogLevelOff]`

BackgroundGeolocation contains powerful logging features.  By default, the plugin boots with a value of **`LOG_LEVEL_VERBOSE`**, storing **3 days** worth of logs (configurable with [`logMaxDays`](config-integer-logmaxdays-3)) in its SQLite database.

The following log-levels are defined as **constants** on the `BackgroundGeolocation` object:

| logLevel            | Description                                      |
|---------------------|--------------------------------------------------|
|`tsLogLevelOff`      | Logging disabled                                 |
|`tsLogLevelError`    | Only log errors                                  |
|`tsLogLevelWarning`  | Only log warnings &amp; errors                   |
|`tsLogLevelInfo`     | Log Info, Warnings &amp; errors                  |
|`tsLogLevelDebug`    | Log debug, info, warnings &amp; errors           |
|`tsLogLevelVerbose`  | Log everything                                   |

Eg:
```javascript
BackgroundGeolocation.configure({
  logLevel: BackgroundGeolocation.LOG_LEVEL_WARNING
});
```

:information_source: To retrieve the plugin's logs, see [`getLog`](#getlogcallbackfn) & [`emailLog`](#emaillogemail-callbackfn).

:warning: When submitting your app to production, take care to configure the **`logLevel`** appropriately (eg: **`LOG_LEVEL_ERROR`**)

------------------------------------------------------------------------------


#### `@config {NSInteger} logMaxDays [3]`

Maximum number of days to persist a log-entry in database.  Defaults to **`3`** days.

------------------------------------------------------------------------------

# :zap: Events

### `location`

Your **`successFn`** will be called with the following signature whenever a new location is recorded:

#### `successFn` Paramters

##### `@param {Object} location` The Location data (@see Wiki for [Location Data Schema](../../../wiki/Location-Data-Schema))

:information_source: When performing a `motionchange` or `getCurrentPosition`, the plugin requests **multiple** location *samples* in order to record the most accurate location possible.  These *samples* are **not** persisted to the database but they will be provided to your `location` listener, for your convenience, since it can take some seconds for the best possible location to arrive.  For example, you might use these samples to progressively update the user's position on a map.  You can detect these *samples* in your `callbackFn` via `location.sample === true`.  If you're manually `POST`ing location to your server, you should ignore these locations.

```javascript
BackgroundGeolocation.on('location', function(location) {
  var coords    = location.coords,
    timestamp   = location.timestamp
    latitude    = coords.latitude,
    longitude   = coords.longitude,
    speed       = coords.speed;

  console.log("- Location: ", timestamp, latitude, longitude, speed);
}, function(errorCode) {
  console.warn("- Location error: ", errorCode);
});
```

#### `failureFn` Paramters

##### `@param {Integer} errorCode`

| Code  | Error                       |
|-------|-----------------------------|
| 0     | Location unknown            |
| 1     | Location permission denied  |
| 2     | Network error               |
| 408   | Location timeout            |

------------------------------------------------------------------------------

### `motionchange`

Your **`callbackFn`** will be executed each time the device has changed-state between **MOVING** or **STATIONARY**.  The **`callbackFn`** will be provided with the following parameters:

##### `@param {Boolean} isMoving`
##### `@param {Object} location` The location at the state-change.

```javascript
BackgroundGeolocation.on('motionchange', function(isMoving, location) {
  if (isMoving) {
      console.log('Device has just started MOVING', location);
  } else {
      console.log('Device has just STOPPED', location);
  }  
});
```

------------------------------------------------------------------------------


### `activitychange`

Your **`callbackFn`** will be executed each time the activity-recognition system receives an event (`still, on_foot, in_vehicle, on_bicycle, running`).  

It will be provided an event `{Object}` containing the following parameters:

##### `@param {String} activity [still|on_foot|running|on_bicycle|in_vehicle]`
##### `@param {Integer} confidence [0-100%]`

```javascript
BackgroundGeolocation.on('activitychange', function(event) {
  console.log('- Activity changed: ', event.activity, event.confidence);
});
```

------------------------------------------------------------------------------


### `providerchange`

Your **`callbackFn`** fill be executed when a change in the state of the device's **Location Services** has been detected.  eg: "GPS ON", "Wifi only".  Your **`callbackFn`** will be provided with an **`{Object} provider`** containing the following properties

#### `callbackFn` Paramters

##### `@param {Boolean} enabled` Whether location-services is enabled
##### `@param {Boolean} gps` Whether gps is enabled
##### `@param {Boolean} network` Whether wifi geolocation is enabled.
##### `@param {Integer} status` Location authorization status.

| Name | Value | Platform |
|------|-------|----------|
| `AUTHORIZATION_STATUS_NOT_DETERMINED` | `0` | iOS only |
| `AUTHORIZATION_STATUS_RESTRICTED` | `1` | iOS only |
| `AUTHORIZATION_STATUS_DENIED` | `2` | iOS & Android |
| `AUTHORIZATION_STATUS_ALWAYS` | `3` | iOS & Android |
| `AUTHORIZATION_STATUS_WHEN_IN_USE` | `4` | iOS only |

:information_source: When Android location permission is **granted**, `status == AUTHORIZATION_STATUS_ALWAYS`, otherwise, `AUTHORIZATION_DENIED`.

```javascript
BackgroundGeolocation.on('providerchange', function(provider) {
  console.log('- Provider Change: ', provider);
  console.log('  enabled: ', provider.enabled);
  console.log('  gps: ', provider.gps);
  console.log('  network: ', provider.network);
  console.log('  status: ', provider.status);

  switch(provider.status) {
    case BackgroundGeolocation.AUTHORIZATION_STATUS_DENIED:
      // Android & iOS
      console.log('- Location authorization denied');
      break;
    case BackgroundGeolocation.AUTHORIZATION_STATUS_ALWAYS:
      // Android & iOS
      console.log('- Location always granted');
      break;
    case BackgroundGeolocation.AUTHORIZATION_STATUS_WHEN_IN_USE:
      // iOS only
      console.log('- Location WhenInUse granted');
      break;
  }
});
```

------------------------------------------------------------------------------


### `geofence`

Adds a geofence event-listener.  Your supplied **`callbackFn`** will be called when any monitored geofence crossing occurs.

#### `callbackFn` Paramters

##### `@param {Object} geofence` The geofence data, including `identifier`, `action`, `extras`, `location`

```javascript
BackgroundGeolocation.on('geofence', function(geofence) {
  var location    = geofence.location;
  var identifier  = geofence.identifier;
  var action      = geofence.action;

  console.log('A geofence has been crossed: ', identifier);
  console.log('ENTER or EXIT?: ', action);
  console.log('geofence: ', JSON.stringify(geofence));
});
```

------------------------------------------------------------------------------


### `geofenceschange`

Fired when the list of monitored-geofences changed.  The Background Geolocation contains powerful geofencing features that allow you to monitor any number of circular geofences you wish (thousands even), in spite of limits imposed by the native platform APIs (**20 for iOS; 100 for Android**).

The plugin achieves this by storing your geofences in its database, using a [geospatial query](https://en.wikipedia.org/wiki/Spatial_query) to determine those geofences in proximity (@see config [geofenceProximityRadius](#config-cllocationdistance-geofenceproximityradius-1000)), activating only those geofences closest to the device's current location (according to limit imposed by the corresponding platform).

When the device is determined to be moving, the plugin periodically queries for geofences in proximity (eg. every minute) using the latest recorded location.  This geospatial query is **very fast**, even with tens-of-thousands geofences in the database.

It's when this list of monitored geofences *changes*, the plugin will fire the **`geofenceschange`** event.

:blue_book: For more information, see [Geofencing Guide](./geofencing.md)

#### `callbackFn` Paramters

##### `@param {Array} on` The list of geofences just activated.
##### `@param {Array} off` The list of geofences just de-activated

```javascript
BackgroundGeolocation.on('geofenceschange', function(event) {
  var on = event.on;   //<-- new geofences activiated.
  var off = event.off; //<-- geofences that were de-activated.

  // Create map circles
  for (var n=0,len=on.length;n<len;n++) {
    var geofence = on[n];
    createGeofenceMarker(geofence)
  }

  // Remove map circles
  for (var n=0,len=off.length;n<len;n++) {
    var identifier = off[n];
    removeGeofenceMarker(identifier);
  }
});
```

This **`event`** object provides only the *changed* geofences, those which just activated or de-activated.

When **all** geofences have been removed, the event object will provide an empty-array **`[]`** for both **`#on`** and **`#off`** keys, ie:

```javascript
{
  on: [{}, {}, ...],  // <-- Entire geofence objects {}
  off: ['identifier_foo', 'identifier_bar']  <-- just the identifiers
}
```

```javascript
BackgroundGeolocation.on('geofenceschange', function(event) {
  console.log("geofenceschange fired! ", event);
});

// calling remove geofences will cause the `geofenceschange` event to fire
BackgroundGeolocation.removeGeofences();

=> geofenceschange fired! {on: [], off: []}

```

------------------------------------------------------------------------------


### `http`

The **`successFn`** will be executed for each successful HTTP request where the response-code is one of `200`, `201` or `204`.  **`failureFn`** will be executed for all other HTTP response codes.  The **`successFn`** and **`failureFn`** will be provided a single **`response {Object}`** parameter with the following properties:

#### `successFn`, `failureFn` Paramters

##### `@param {Integer} status`.  The HTTP status
##### `@param {String} responseText` The HTTP response as text.

Example:

```javascript
BackgroundGeolocation.onHttp(function(response) {
  var status = response.status;
  var responseText = response.responseText;
  var res = JSON.parse(responseText);  // <-- if your server returns JSON
  console.log("- HTTP success", status, res);
}, function(response) {
  var status = response.status;
  var responseText = response.responseText;
  console.log("- HTTP failure: ", status, responseText);
})
```

------------------------------------------------------------------------------


### `heartbeat`

The **`callbackFn`** will be executed for each [`#heartbeatInterval`](#config-nstimeinterval-heartbeatinterval-60) while the device is in **stationary** state (**iOS** requires [`preventSuspend: true`](#config-bool-preventsuspend-no) as well).  The **`callbackFn`** will be provided a single **`params {Object}`** parameter with the following properties:

#### `callbackFn` Paramters

##### `@param {String} motionType [still|on_foot|running|on_bicycle|in_vehicle|shaking|unknown]`  The current motion-type.

##### `@param {Object} location`  When the plugin detects movement (iOS only), it will always request a new high-accuracy location in order to determine if the device has moved beyond `stationaryRadius` and if the location has `speed > 0`.  This fresh location will be provided to your `callbackFn`.  Android will simply return the "last known location"

Example:

```javascript
BackgroundGeolocation.on('heartbeat', function(params) {
  console.log('- hearbeat');

  // You could request a new location if you wish.
  BackgroundGeolocation.getCurrentPosition(function(location) {
    console.log('- current location: ', location);
  });
});
```

------------------------------------------------------------------------------


### `schedule`

The **`callbackFn`** will be executed each time a [`schedule`](#schedule) event fires.  Your **`callbackFn`** will be provided with the current **`state`** object (@see [`#getState`](getstatesuccessfn)).  **`state.enabled`** will reflect the state according to your [configured `schedule`](#config-array-schedule-undefined).

#### `callbackFn` Paramters

##### `@param {Object} state` Current plugin state.

```javascript
BackgroundGeolocation.on('schedule', function(state) {
  if (state.enabled) {
    console.log('- BackgroundGeolocation scheduled start tracking');
  } else {
    console.log('- BackgroundGeolocation scheduled stop tracking');
  }
});
```

------------------------------------------------------------------------------


### `powersavechange`

Fired when the state of the operating-system's "Power Saving" mode changes.  Your `callbackFn` will be provided with a `Boolean` showing whether "Power Saving" is **enabled** or **disabled**.  Power Saving mode can throttle certain services in the background, such as HTTP requests or GPS.

:information_source: You can manually request the current-state of "Power Saving" mode with the **method** [`#isPowerSaveMode`](#ispowersavemodecallbackfn).

#### iOS

iOS Power Saving mode can be engaged manually by the user in **Settings -> Battery** or from an automatic OS dialog.

![](https://dl.dropboxusercontent.com/s/lz3zl2jg4nzstg3/Screenshot%202017-09-19%2010.34.21.png?dl=1)

#### Android

Android Power Saving mode can be engaged manually by the user in **Settings -> Battery -> Battery Saver** or automatically with a user-specified "threshold" (eg: 15%).

![](https://dl.dropboxusercontent.com/s/raz8lagrqayowia/Screenshot%202017-09-19%2010.33.49.png?dl=1)

#### `callbackFn` Paramters

##### `@param {Boolean} isPowerSaveMode`

```javascript
BackgroundGeolocation.on('powersavechange', function(isPowerSaveMode) {
  console.log("- powersavechange, power-saving mode enabled? ", isPowerSaveMode);
});
```


------------------------------------------------------------------------------


# :large_blue_diamond: Methods

## :small_blue_diamond: Core API Methods

### `ready`

The **`#ready`** method is your first point-of-contact with the SDK.  You must execute the `#ready` method each time your application boots.

:information_source: BackgroundGeolocation persists its **`enabled`** state between application terminate or device reboot and **`#ready`** will **automatically** [`#start`](startsuccessfn-failurefn) tracking if it finds **`enabled == true`**.  

------------------------------------------------------------------------------


### `start`

Enable location tracking.  This is the SDK's power **ON** button.  The plugin will initially start into its **stationary** state, fetching an initial location before turning off location services.

```javascript
BackgroundGeolocation.start(function(state) {
  console.log('- BackgroundGeolocation started, state: ', state);
});
```

:blue_book: For more information, see [Philosophy of Operation](../../../wiki/Philosophy-of-Operation)

------------------------------------------------------------------------------


### `stop`

Disable location tracking.  This is the SDK's power **OFF** button.

```javascript
BackgroundGeolocation.stop();
```

:warning: If you've configured a [`schedule`](config-array-schedule-undefined), **`#stop`** will **not** halt the Scheduler.  You must explicitly stop the Scheduler as well:

```javascript
// Later when you want to stop the Scheduler (eg: user logout)
BackgroundGeolocation.stopSchedule(function() {
  console.info('- Scheduler stopped');
  // You must explicitly stop tracking if currently enabled
  BackgroundGeolocation.stop();
});
```

------------------------------------------------------------------------------


### `getCurrentPosition:(TSCurrentPositionRequest*)`

Retrieves the current position.  This method instructs the native code to fetch exactly one location using maximum power & accuracy.  The native code will persist the fetched location to its SQLite database just as any other location in addition to POSTing to your configured [`#url`](#config-string-url-undefined) (if you've enabled the HTTP features).

If an error occurs while fetching the location, the **`failureFn`** will be executed with an **`Integer`** [Error Code](../../../wiki/Location-Error-Codes) as the first argument.

#### TSCurrentPositionRequest Options

##### `@config {NSTimeInterval} timeout [30]` An optional location-timeout.  If the timeout expires before a location is retrieved, the `failure` callback will be executed.

##### `@config {double millis} maximumAge [0]` Accept the last-recorded-location if no older than supplied value in milliseconds.

##### `@config {BOOL} persist [YES]` Defaults to `YES`.  Set `NO` to disable persisting the retrieved location in the plugin's SQLite database.

##### `@config {NSInteger} samples [3]` Sets the maximum number of location-samples to fetch.  The plugin will return the location having the best accuracy to your `successFn`.  Defaults to `3`.  Only the final location will be persisted.

##### `@config {CLLocationAccuracy} desiredAccuracy [stationaryRadius]` Sets the desired accuracy of location you're attempting to fetch.  When a location having `accuracy <= desiredAccuracy` is retrieved, the plugin will stop sampling and immediately return that location.  Defaults to your configured `stationaryRadius`.

##### `@config {NSDicationary} extras` Optional extra-data to attach to the location.  These `extras {Object}` will be merged to the recorded `location` and persisted / POSTed to your server (if you've configured the HTTP Layer).

#### `success {Block}`

#### `failure {Block}`

```obj-c
// Create Requst
TSCurrentPositionRequest *request = [[TSCurrentPositionRequest alloc] initWithSuccess:^(TSLocation *location) {
    NSLog(@"- getCurrentPosition success: %@", [location toDictionary]);
} failure:^(NSError *error) {
    NSLog(@"- getCurrentPosition failure: %@", error);
}];
request.maximumAge = 0;
request.persist = YES;
request.samples = 3;
request.desiredAccuracy = 0;
request.extras = @{@"foo":@"bar"};

TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
// Execute Request
[bgGeo getCurrentPosition:request];
```

:information_source: While the **`success`** block will receive only **one** location, the plugin *does* request **multiple** location [`samples`](#config-integer-samples-3-sets-the-maximum-number-of-location-samples-to-fetch--the-plugin-will-return-the-location-having-the-best-accuracy-to-your-successfn--defaults-to-3--only-the-final-location-will-be-persisted) in order to record the most accurate location possible.  These *samples* are **not** persisted to the database but they will be provided to your [`location`](#location) event-listener, for your convenience, since it can take some seconds for the best possible location to arrive.  For example, you might use these samples to progressively update the user's position on a map.  You can detect these *samples* in your [`location`](#location) `success` block via `location.sample`.  If you're manually `POST`ing location to your server, you should ignore these locations.

If a location failed to be retrieved, the `failure` block will be executed and provided with an `NSError` with one of the following error-codes:

| Code  | Error                       |
|-------|-----------------------------|
| 0     | Location unknown            |
| 1     | Location permission denied  |
| 2     | Network error               |
| 408   | Location timeout            |


------------------------------------------------------------------------------


### `watchPosition:(TSWatchPositionRequest*)`

Start a stream of continuous location-updates.  The native code will persist the fetched location to its SQLite database just as any other location in addition to POSTing to your configured [`#url`](#config-string-url-undefined) (if you've enabled the HTTP features).

:warning: **`#watchPosition`** is **not** reccommended for **long term** monitoring in the background &mdash; It's primarily designed for use in the foreground **only**.  You might use it for fast-updates of the user's current position on the map, for example.

**`#watchPosition`** will continue to run in the background, preventing iOS from suspending your application.  Take care to listen to `suspend` event and call [`#stopWatchPosition`](stopwatchpositionsuccessfn-failurefn) if you don't want your app to keep running (TODO make this configurable).


#### `TSWatchPositionRequest` Optios

##### `@config {double millis} interval [1000]` Location update interval
##### `@config {CLLocationAccuracy} desiredAccuracy [0]`
##### `@config {BOOL} persist [YES]` Whether to persist location to database
##### `@config {NSDictionary} extras [undefined]` Optional extras to append to each location
#### `success {Block}` 
#### `failure {Block}`

```obj-c
TSWatchPositionRequest *request = [[TSWatchPositionRequest alloc] initWithSuccess:^(TSLocation *location) {
    NSLog(@"- watchPosition success: %@", [location toDictionary]);
} failure:^(NSError *error) {
    NSLog(@"- watchPosition failure: %@", error);        
}];

request.interval = 5000;
request.persist = YES;
request.extras = @{@"foo":@"bar"};
request.timeout = 60000;
```

:information_source: Also see [`#stopWatchPosition`](stopwatchposition)

------------------------------------------------------------------------------


### `stopWatchPosition`

Halt [`#watchPosition`](watchpositiontswatchpositionrequest) updates.

```javascript
[[TSLocationManager sharedInstance] stopWatchPosition];
```

------------------------------------------------------------------------------


### `changePace:(BOOL)`

Manually Toggles the plugin **motion state** between **stationary** and **moving**.  When **`enabled`** is set to **`YES`**, the plugin will engage location-services and begin aggressively tracking the device's location *immediately*, bypassing stationary monitoring.  If you were making a "Jogging" application, this would be your **[Start Workout]** button to immediately begin location-tracking.  Send **`NO`** to turn **off** location-services and return the plugin to the **stationary** state.

```javascript
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
[bgGeo changePace:YES];
.
.
.
[bgGeo changePace:NO];
```

------------------------------------------------------------------------------

### `setOdometer:(TSCurrentPositionRequest*)`

Set the **`odometer`** to *any* arbitrary value.  **NOTE** `setOdometer` will perform a [`getCurrentPosition`](#getcurrentpositiontscurrentpositionrequest) in order to record to exact location where odometer was set; as a result, the `callback` signatures are identical to those of [`getCurrentPosition`](#getcurrentpositiontscurrentpositionrequest).

```obj-c
TSCurrentPositionRequest *request = [[TSCurrentPositionRequest alloc] initWithSuccess:^(TSLocation *location) {
    NSLog(@"- setOdometer success: %@", [location toDictionary])
} failure:^(NSError *error) {
    NSLog(@"- setOdometer failure: %@", error);
}];

[[TSLocationManager sharedInstance] setOdometer:0 request:request];
```

------------------------------------------------------------------------------

### `startSchedule`

If a [`#schedule`](#config-array-schedule-undefined) was configured, this method will initiate that schedule.  The plugin will automatically be started or stopped according to the configured [`#schedule`](#config-array-schedule-undefined).

```javascript
TSConfig *config = [TSConfig sharedInstance];
[config updateWithBlock:^(TSConfigBuilder *builder) {
    builder.schedule = @[
      '1-7 09:00-17:00',
      '1 19:00-23:00'
    ];
}];

TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

// Listen to schedule event
[bgGeo onSchedule:^(TSScheduleEvent *event) {
    NSLog(@"- Schedule event: %@", event.state);
}];

// Start the scheduler
[bgGeo startSchedule];
.
.
.
// Stop the scheduler
[bgGeo stopSchedule];
```

------------------------------------------------------------------------------


### `stopSchedule`

This method will stop the Scheduler service.

```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
[bgGeo stopSchedule];
```

:warning: **`#stopSchedule`** will not execute **`#stop`** if the plugin is currently **enabled**.  You must explicitly execute `#stop`.

------------------------------------------------------------------------------


### `(UIBackgroundTaskIdentifier) createBackgroundTask`

Sends a signal to iOS that you wish to perform a long-running task.  The OS will not suspend your app until you signal completion with the [`#stopBackgroundTask`](#stopBackgroundTaskuibackgroundtaskidentifier) method.  

Eg:
```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
UIBackgroundTaskIdentifier taskId = [bgGeo createBackgroundTask];

// Perform long-running task
.
.
.
// Signal completion of your task
[bgGeo stopBackgroundTask:taskId];

```

:warning: iOS provides **exactly** 180s of background-running time.  If your long-running task exceeds this time, the plugin has a fail-safe which will automatically [`#finish`](#finishtaskid) your **`taskId`** to prevent the OS from force-killing your application.

------------------------------------------------------------------------------


### `stopBackgroundTask:(UIBackgroundTaskIdentifier)`

Sends a signal to the native OS that your long-running task, addressed by `taskId` returned by `#uibackgroundtaskidentifier-createbackgroundtask` is complete and the OS may proceed to suspend your application if applicable.

Eg:
```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
UIBackgroundTaskIdentifier taskId = [bgGeo createBackgroundTask];

// Perform long-running task
.
.
.
// Signal completion of your task
[bgGeo stopBackgroundTask:taskId];

```

------------------------------------------------------------------------------


### `(BOOL) isPowerSaveMode`

Fetches the state of the operating-systems "Power Saving" mode, whether `enabled` or `disabled`.  Power Saving mode can throttle certain services in the background, such as HTTP requests or GPS.

:information_source: You can listen to changes in the state of "Power Saving" mode with the **event** [`#powersavechange`](#powersavechange).

iOS Power Saving mode can be engaged manually by the user in **Settings -> Battery** or from an automatic OS dialog.

![](https://dl.dropboxusercontent.com/s/lz3zl2jg4nzstg3/Screenshot%202017-09-19%2010.34.21.png?dl=1)

Eg:
```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
BOOL isPowerSaveEnabled = [bgGeo isPowerSaveMode];

```

------------------------------------------------------------------------------


### `removeListeners(successFn, failureFn)`

Remove all event-listeners registered with [`#on`](#zap-events) method.  You're free to add more listeners again after executing **`#removeListeners`**.

```javascript
BackgroundGeolocation.on('location', function(location) {
  console.log('- Location', location);  
})
.
.
.
BackgroundGeolocation.removeListeners();

BackgroundGeolocation.on('location', function(location) {
  console.log('- Location listener added again: ', location);
});
```

------------------------------------------------------------------------------


## :small_blue_diamond: HTTP & Persistence Methods

### `getLocations:(^Block) success failure:(^Block)failure`

Fetch all the locations currently stored in native plugin's SQLite database.  Your **`success`** will receive an `NSArray` of locations 

Eg:
```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

[bgGeo getLocations:^(NSArray* records) {
    NSLog(@"- getLocations success: %@", records);
} failure:^(NSString* error) {
    NSLog(@"- getLocations failure: %@", error);
}];

```

------------------------------------------------------------------------------


### `(int) getCount`
Fetches count of SQLite locations table `SELECT count(*) from locations`.  The 

```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
int count = [bgGeo getCount];
```

------------------------------------------------------------------------------

### `destroyLocations`

Remove all records in plugin's SQLite database.

```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
[bgGeo destroyLocatios];
```

------------------------------------------------------------------------------


### `sync:(^block)success failure:(^block)failure`

If the plugin is configured for HTTP with an [`#url`](#config-string-url-undefined) and [`autoSync: false`](#config-string-autosync-true), this method will initiate POSTing the locations currently stored in the native SQLite database to your configured [`#url`](#config-string-url-undefined).  When your HTTP server returns a response of `200 OK`, that record(s) in the database will be DELETED.  

If you configured [`batchSync: true`](#config-bool-batchsync-no), all the locations will be sent to your server in a single HTTP POST request, otherwise the plugin will create execute an HTTP post for **each** location in the database (REST-style).  Your **`callbackFn`** will be executed and provided with an Array of all the locations from the SQLite database.  If you configured the plugin for HTTP (by configuring an [`#url`](#config-nsstring-url-undefined), your **`callbackFn`** will be executed after the HTTP request(s) have completed.  If the plugin failed to sync to your server (possibly because of no network connection), the **`failureFn`** will be called with an `errorMessage`.  If you are **not** using the HTTP features, **`sync`** will delete all records from its SQLite datbase.  Eg:

Your callback will be provided with the following params

#### `successFn` Parameters

##### `@param {Array} locations`  The list of locations stored in SQLite database.

```ojb-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
[bgGeo sync:^(NSArray* records) {
    NSLog(@"- Sync success: %@", records);
}, (NSError *error {
    NSLog(@"- Sync error: %@", error);
}];
```

:blue_book: For more information, see [HTTP Guide](http.md)

------------------------------------------------------------------------------


## :small_blue_diamond: Geofencing Methods

### `startGeofences(callbackFn)`

Engages the geofences-only `trackingMode`.  In this mode, no active location-tracking will occur -- only geofences will be monitored.  To stop monitoring "geofences" `trackingMode`, simply use the usual `#stop` method.

```obj-c

TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

// Listen to geofence event
[bgGeo onGeofence:^(TSGeofenceEvent *event) {
    NSLog(@"- onGeofence: %@", event);
}];

// Create a geofence
TSGeofence geofence = [[TSGeofence alloc] initWithIdentifier: params[@"identifier"]
                                radius: 200
                              latitude: 37.2334123
                             longitude: 42.2343234
                         notifyOnEntry: YES
                          notifyOnExit: YES
                         notifyOnDwell: YES
                        loiteringDelay: 30000
                                extras: @{@"foo":@"bar"}];

[bgGeo addGeofence:geofence success:^{
    NSLog(@"- addGeofence success");
} failure:^(NSString *error) {
    NSLog(@"- addGeofence failure: %@", error);
}];

[bgGeo ready];

// Start monitoring geofences-only mode.
[bgGeo startGeofences];

```

------------------------------------------------------------------------------


### `addGeofence:(TSGeofence*) success(^block) failure(^block)`

Adds a geofence to be monitored by the native plugin.  If a geofence *already exists* with the configured **`identifier`**, the previous one will be **deleted** before the new one is inserted.  

#### `TSGeofence` Options

##### `@config {NSString} identifier` The name of your geofence, eg: "Home", "Office"

##### `@config {CLLocationDistance} radius` The radius (meters) of the geofence.  In practice, you should make this >= 100 meters.

##### `@config {CLLocationDegrees} latitude` Latitude of the center-point of the circular geofence.

##### `@config {CLLocationDegrees} longitude` Longitude of the center-point of the circular geofence.

##### `@config {BOOL} notifyOnExit` Whether to listen to EXIT events

##### `@config {BOOL} notifyOnEntry` Whether to listen to ENTER events

##### `@config {BOOL} notifyOnDwell` Whether to listen to DWELL events

##### `@config {double milliseconds} loiteringDelay` When `notifyOnDwell` is `true`, the delay before DWELL event is fired after entering a geofence (@see [Creating and Monitoring Geofences](https://developer.android.com/training/location/geofencing.html))

##### `@config {NSDictionary} extras` Optional arbitrary meta-data.

```obj-c
// Create a geofence
TSGeofence geofence = [[TSGeofence alloc] initWithIdentifier: params[@"identifier"]
                                radius: 200
                              latitude: 37.2334123
                             longitude: 42.2343234
                         notifyOnEntry: YES
                          notifyOnExit: YES
                         notifyOnDwell: YES
                        loiteringDelay: 30000
                                extras: @{@"foo":@"bar"}];

[bgGeo addGeofence:geofence success:^{
    NSLog(@"- addGeofence success");
} failure:^(NSString *error) {
    NSLog(@"- addGeofence failure: %@", error);
}];
```

:information_source: When adding a list-of-geofences, it's about **10* faster** to use [`#addGeofences`](#addgeofencesgeofences-nsarray-success-failure) instead.

:blue_book: See [Geofencing Guide](./geofencing.md) for more information.

#### `successFn` Parameters:

##### `@param {String} identifier` The name of your geofence, eg: "Home", "Office"

#### `failureFn` Parameters

##### `@param {String} errorMessage`

------------------------------------------------------------------------------


### `addGeofences:(NSArray*) success:^(block) failure:^(block)`

Adds a list of geofences to be monitored by the native plugin.  If a geofence *already* exists with the configured `identifier`, the previous one will be **deleted** before the new one is inserted.  The `geofences` param is an `Array` of geofence Objects `{}` with the following params:

#### Config Options

##### `@config {Array} geofences` An list of TSGeofence configured with the same parmeters as [`#addGeofence`](#config-options)

##### `@config {^block} success` Executed when geofences successfully added.

##### `@config {^block} failure` Executed when failed to add geofence.

Example:

```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

TSGeofence geofence1 = [[TSGeofence alloc] initWithIdentifier:@"geofence1"
                                radius: 200
                              latitude: 37.2334123
                             longitude: 42.2343234
                         notifyOnEntry: YES
                          notifyOnExit: YES
                         notifyOnDwell: YES
                        loiteringDelay: 30000
                                extras: @{@"foo":@"bar"}];

TSGeofence geofence2 = [[TSGeofence alloc] initWithIdentifier:@"geofence2"
                                radius: 200
                              latitude: 37.2334123
                             longitude: 42.2343234
                         notifyOnEntry: YES
                          notifyOnExit: YES
                         notifyOnDwell: YES
                        loiteringDelay: 30000
                                extras: @{@"foo":@"bar"}];

// Create an Array to contain geofences
NSArray *geofences = [NSArray new];
[geofences addObject:geofence1];
[geofences addObject:geofence2];

[bgGeo addGeofences:geofences success:^{
    NSLog(@"- addGeofences success");
} failure:^(NSString *error) {
    NSLog(@"- addGeofences failure: %@", error);
}];

```


------------------------------------------------------------------------------


### `removeGeofence:(NSString*)identifier success:^(block) failure:^(block)`

Removes a geofence having the given `{NSString} identifier`.

#### Config Options

##### `@config {NSString} identifier` Identifier of geofence to remove.

##### `@config {^block} success` Block called when successfully removed geofence.

##### `@config {^block} failure` Block called when failed to remove geofence

```javascript
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

[bgGeo removeGeofence:@"HOME" success:^{
    NSLog(@"- Remove geofence success");
} failure:^(NSString *error) {
    NSLog(@"- Remove geofence failure: %@", error);
}]
```

------------------------------------------------------------------------------


### `removeGeofences`

Removes all geofences.

```javascript
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

[bgGeo removeGeofences];

```

------------------------------------------------------------------------------


### `(NSArray*) getGeofences`

Fetch the list of monitored geofences.  Returns an `NSArray` of geofences.  If there are no geofences being monitored, you'll receive an empty Array `[]`.


```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

NSArray *geofences = [bgGeo getGeofences];
for (TSGeofence *geofence in geofences) {
    NSLog(@"- Geofence: %@", [geofence toDictionary]);
}
```

------------------------------------------------------------------------------


## :small_blue_diamond: Logging Methods

### `getLog:^(block) failure:^(block)`

Fetches the entire contents of the current circular-log and return it as an NSString.

```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

[bgGeo getLog:^(NSString* log) {
    NSLog(@"- Log: %@", log);
} failure:^(NSString *error) {
    NSLog(@"- getLog failure: %@", error);
}]
```

------------------------------------------------------------------------------

### `emailLog:(NSString*)email success:^(block) failure:^(block)`

Fetch the entire contents of the current circular log and email it to a recipient using the device's native email client.

#### Config Options:

##### `@param {NSString} email`  Email address to send log to.
##### `@param {^block} success`  Executed after successfully emailed.
##### `@param {^block} failure`  Executed on failure

```javascript
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

[bgGeo emailLog:@"foo@bar.com" success:^{
    NSLog(@"- emailLog success");
} failure:^(NSString error) {
    NSLog(@"- emailLog failure: %@", error);
}]
```

### `destroyLog`

Destory the entire contents of Log database.

```obj-c
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];

[bgGeo destroyLog];

```

------------------------------------------------------------------------------


### `playSound:(SystemSoundID)soundId`

Here's a fun one.  The plugin can play a number of OS system sounds for each platform.  For [IOS](http://iphonedevwiki.net/index.php/AudioServices) and [Android](http://developer.android.com/reference/android/media/ToneGenerator.html).

```javascript
// A soundId iOS recognizes
TSLocationManager *bgGeo = [TSLocationManager sharedInstance];
[bgGeo playSound:1303];

```

------------------------------------------------------------------------------
