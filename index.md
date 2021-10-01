## Battery Optmization

Battery life is the single most important aspect of the mobile user experience. A device without power offers no functionality at all. For this reason, it is critically important that apps be as respectful of battery life as possible.

### Doze and App Standby

Starting from Android 6.0 (API level 23), Android introduces two power-saving features that extend battery life for users by managing how apps behave when a device is not connected to a power source. Doze reduces battery consumption by deferring background CPU and network activity for apps when the device is unused for long periods of time. App Standby defers background network activity for apps with which the user has not recently interacted. 

### Doze

If a user leaves a device unplugged for a certain period of time, the device enters the Doze. In the Doze mode the system conserves the battery by restricting the apps access to network and CPU-intensive services.

Periodically, the system exits Doze for a brief time to let apps complete their deferred activities. During this maintenance window, the system runs all pending syncs, jobs, and alarms, and lets apps access the network. 

### Support for Whitelisting
 Almost all apps should be able to support Doze by managing network connectivity, alarms, jobs, and syncs properly, and by using FCM high-priority messages. For a narrow set of use cases, this might not be sufficient. For such cases, the system provides a configurable list of apps that are partially exempt from Doze and App Standby optimizations.

An app that is partially exempt can use the network and hold partial wake locks during Doze and App Standby. However, other restrictions still apply to the app, just as they do to other apps. For example, the app’s jobs and syncs are deferred (on API level 23 and below), and its regular AlarmManager alarms do not fire. An app can check whether it is currently on the exemption list by calling isIgnoringBatteryOptimizations().

Users can manually configure the list of exempted apps in Settings > Battery > Battery Optimization. Alternatively, the system provides ways for apps to ask users to exempt them:

    - Most apps should invoke an intent that contains the ACTION_IGNORE_BATTERY_OPTIMIZATION_SETTINGS.
    - Apps that satisfy an acceptable use case can instead invoke an intent that contains the ACTION_REQUEST_IGNORE_BATTERY_OPTIMIZATIONS intent action to let the user add the app to the exemption list directly, without going to system settings.

### Adding request permission as part of the application

```markdown

<uses-permission android:name="android.permission.REQUEST_IGNORE_BATTERY_OPTIMIZATIONS"/>

```

### Requesting whitelist from the application

```

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    Intent intent = new Intent(); 
    String packageName = getPackageName();
    PowerManager pm = (PowerManager) getSystemService(POWER_SERVICE);

    if (!pm.isIgnoringBatteryOptimizations(packageName)) {
        Toast.makeText(MainActivity.this, "Battery Optimization for the app is not enbaled, enabling IT", Toast.LENGTH_LONG).show();
        intent.setAction(Settings.ACTION_REQUEST_IGNORE_BATTERY_OPTIMIZATIONS);
        Intent setData = intent.setData(Uri.parse("package:" + packageName));
    }
    else {
        Toast.makeText(MainActivity.this, "Battery Optimization is already enabled for the app, add them in the Settings: Apps & Notification -> Special app access -> Battery optimization", Toast.LENGTH_LONG).show();
        intent.setAction(Settings.ACTION_IGNORE_BATTERY_OPTIMIZATION_SETTINGS);
    }
startActivity(intent);
                    
```


