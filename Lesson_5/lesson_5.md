# Lesson 5  - Places

https://github.com/udacity/AdvancedAndroid_Shushme

## How Location works

GPS = Global Positioning System

The built-in GPS use all satellites in sight, but it use a lot of battery power.
![](lesson_5_3_gps_satellites.png)

WiFi and Cell Towers are also used to locate a phone.
And while they are less accurate, they tend to use much less baterry power than using GPS.     
They are also a lot quicker in helping decide what country or area the device is in.    
They are also faster than communicating with satellites when locating the device for the very first time.

So combining all the techniques together, android can smartly locate a device with the best accuracy and with minimal power consumption.

## API Key Setup Instructions

API Key Setup Instructions

1. Go to the [Google API Console](https://console.developers.google.com/flows/enableapi?apiid=placesandroid&reusekey=true&pli=1).
2. Create or select a project.
3. Click Continue to enable the Google Places API for Android.
4. On the Credentials page, get an API key.

**Note**: If you have an existing API key with Android restrictions, you may use that key.

### Restricting your API key

To control who can use this key to access Google's API; restrict the key to a certain Android app

1. From the dialog displaying the API key, select Restrict key to set an Android restriction on the API key.
2. In the Restrictions section, select Android apps, then enter your app's SHA-1 fingerprint and package name. For example: BB:0D:AC:74:D3:21:E1:43:67:71:9B:62:91:AF:A1:66:6E:44:5D:75 com.example.android.places-example
3. Click Save.

To find your app's SHA-1 fingerprint information and for more information refer to this [Google documentation](https://developers.google.com/places/android-api/signup).

Your new Android-restricted API key appears in the list of API keys for your project. An API key is a string of characters, something like this:

```AIzaSyBdVl-cTICSwYKrZ95SuvNw7dbMuDt1KG0```

It may take up to 5 minutes for the key to be fully provisioned. If the key does not work immediately, try again in 5 minutes.

## Including the API Key

In the Manifest file:
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.android.shushme">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">

        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="insert-your-api-key-here" />

        <activity android:name="com.example.android.shushme.MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <provider
            android:name="com.example.android.shushme.provider.PlaceContentProvider"
            android:authorities="com.example.android.shushme"
            android:exported="false"/>

    </application>

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

In app/build.gradle file, add these dependencies:
```
implementation 'com.google.android.gms:play-services-places:9.8.0'
implementation 'com.google.android.gms:play-services-location:9.8.0'
```

## Starter Code

![](lesson_5_8_starter.png)

![](lesson_5_8_checklist_starter.png)

![](lesson_5_8_permissions.png)

![](lesson_5_8_app_settings.png)

In MainActivity:
```java
 private static final int PERMISSIONS_REQUEST_FINE_LOCATION = 111;


/***
     * Button Click event handler to handle clicking the "Add new location" Button
     *
     * @param view
     */
    public void onAddPlaceButtonClicked(View view) {
        if (ActivityCompat.checkSelfPermission(this, android.Manifest.permission.ACCESS_FINE_LOCATION)
                != PackageManager.PERMISSION_GRANTED) {
            Toast.makeText(this, getString(R.string.need_location_permission_message), Toast.LENGTH_LONG).show();
            return;
        }
        Toast.makeText(this, getString(R.string.location_permissions_granted_message), Toast.LENGTH_LONG).show();
    }

    @Override
    public void onResume() {
        super.onResume();

        // Initialize location permissions checkbox
        CheckBox locationPermissions = (CheckBox) findViewById(R.id.location_permission_checkbox);
        if (ActivityCompat.checkSelfPermission(MainActivity.this,
                android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            locationPermissions.setChecked(false);
        } else {
            locationPermissions.setChecked(true);
            locationPermissions.setEnabled(false);
        }
    }

    public void onLocationPermissionClicked(View view) {
        ActivityCompat.requestPermissions(MainActivity.this,
                new String[]{android.Manifest.permission.ACCESS_FINE_LOCATION},
                PERMISSIONS_REQUEST_FINE_LOCATION);
}
```

## Google API Client

Some Google Play Services APIs require you to create a client that will connect to Google Play Services and use that connection to communicate with the APIs

In our case, both places API and location services API require that client, so how do we create one? A Google API Client is created using `GoogleApiClient.Builder` as follows:
```java
// Build up the LocationServices API client
// Uses the addApi method to request the LocationServices API
// Also uses enableAutoManage to automatically when to connect/suspend the client
GoogleApiClient client = new GoogleApiClient.Builder(this)
    .addConnectionCallbacks(this)
    .addOnConnectionFailedListener(this)
    .addApi(LocationServices.API)
    .addApi(Places.GEO_DATA_API)
    .enableAutoManage(this, this)
    .build();
```

First 2 methods, `addConnectionCallbacks` and `addOnConnectionFailedListener`, are setting the callbacks that will notify when a connection is successful, since we will be creating this client in `MainActivity`, we will set the callbacks to this. `addApi` is then called to add the API libraries we are planning to use, as we mentioned earlier we will be using the location services api and the places api. enableAutoManage means that the client will connect/disconnect on its own, otherwise you will have to call connect and disconnect explicitly. And then finally build creates the actual client object.

Now, since we’ve chosen to set this (`MainActivity`) as our callback listener, we need to implement a couple of interfaces:
```java
public class MainActivity extends AppCompatActivity implements
GoogleApiClient.ConnectionCallbacks,
GoogleApiClient.OnConnectionFailedListener
Loader.loaderCallbacks<Cursor> {
```
And hence implement the required methods:
```java
/***
     * Called when the Google API Client is successfully connected
     *
     * @param connectionHint Bundle of data provided to clients by Google Play services
     */
    @Override
    public void onConnected(@Nullable Bundle connectionHint) {
        Log.i(TAG, "API Client Connection Successful!");
    }

    /***
     * Called when the Google API Client is suspended
     *
     * @param cause cause The reason for the disconnection. Defined by constants CAUSE_*.
     */
    @Override
    public void onConnectionSuspended(int cause) {
        Log.i(TAG, "API Client Connection Suspended!");
    }

    /***
     * Called when the Google API Client failed to connect to Google Play Services
     *
     * @param result A ConnectionResult that can be used for resolving the error
     */
    @Override
    public void onConnectionFailed(@NonNull ConnectionResult result) {
        Log.e(TAG, "API Client Connection Failed!");
}
```

Once the client automatically connects to Google Play Services, `onConnected` method will fire, that is if the connections was successful.

If for any reason it wasn’t, `onConnectionFailed` will fire instead.

`onConnectionSuspended` as the name suggests fires when the client’s AutoManager decided to suspend the connection.

For now, all three methods are simply logging a message when any of them gets called.

## Place Picker

In MainActivity:
```java
private static final int PLACE_PICKER_REQUEST = 1;

/***
     * Button Click event handler to handle clicking the "Add new location" Button
     *
     * @param view
     */
    public void onAddPlaceButtonClicked(View view) {
        if (ActivityCompat.checkSelfPermission(this, android.Manifest.permission.ACCESS_FINE_LOCATION)
                != PackageManager.PERMISSION_GRANTED) {
            Toast.makeText(this, getString(R.string.need_location_permission_message), Toast.LENGTH_LONG).show();
            return;
        }
        try {
            // Start a new Activity for the Place Picker API, this will trigger {@code #onActivityResult}
            // when a place is selected or with the user cancels.
            PlacePicker.IntentBuilder builder = new PlacePicker.IntentBuilder();
            Intent i = builder.build(this);
            startActivityForResult(i, PLACE_PICKER_REQUEST);
        } catch (GooglePlayServicesRepairableException e) {
            Log.e(TAG, String.format("GooglePlayServices Not Available [%s]", e.getMessage()));
        } catch (GooglePlayServicesNotAvailableException e) {
            Log.e(TAG, String.format("GooglePlayServices Not Available [%s]", e.getMessage()));
        } catch (Exception e) {
            Log.e(TAG, String.format("PlacePicker Exception: %s", e.getMessage()));
        }
    }


    /***
     * Called when the Place Picker Activity returns back with a selected place (or after canceling)
     *
     * @param requestCode The request code passed when calling startActivityForResult
     * @param resultCode  The result code specified by the second activity
     * @param data        The Intent that carries the result data.
     */
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == PLACE_PICKER_REQUEST && resultCode == RESULT_OK) {
            Place place = PlacePicker.getPlace(this, data);
            if (place == null) {
                Log.i(TAG, "No place selected");
                return;
            }

            // Extract the place information from the API
            String placeName = place.getName().toString();
            String placeAddress = place.getAddress().toString();
            String placeID = place.getId();

            // Insert a new place into DB
            ContentValues contentValues = new ContentValues();
            contentValues.put(PlaceContract.PlaceEntry.COLUMN_PLACE_ID, placeID);
            getContentResolver().insert(PlaceContract.PlaceEntry.CONTENT_URI, contentValues);
        }
}
```