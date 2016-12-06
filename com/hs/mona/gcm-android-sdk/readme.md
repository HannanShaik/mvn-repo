#GCM Android SDK

##Latest Update

Google has deprecated GCM & introduced FCM.


>Firebase Cloud Messaging (FCM) is the new version of GCM. It inherits the reliable and scalable GCM infrastructure, plus new features! If you are integrating messaging in a new app, start with FCM. GCM users are strongly recommended to upgrade to FCM, in order to benefit from new FCM features today and in the future.

FCM setup is very easy. All you need to do is follow [this link](https://firebase.google.com/docs/cloud-messaging/android/client).


##GCM Android SDK

**Latest Version - 1.0.3**


As per google’s documentation “Google Cloud Messaging for Android (GCM) is a service that helps developers send data from servers to their Android applications on Android devices”. Using this service you can send data to your application whenever new data is available instead of making requests to server in timely fashion. Integrating GCM in your android application enhances user experience and saves lot of battery power

Push notifications have become an integral part of all the applications in the market today. They are used for engagement, marketing, sales etc.

Google has given a nice writeup on how to configure GCM for Push Notifications in an android app.

https://developers.google.com/cloud-messaging/android/client

So I followed it and created a small library out of it. Now none of us have to read the long documentation and do trial and error for each section you code. All you've to do is include this library in your code.


**Sample Project can be found here : https://github.com/HannanShaik/MonaGCMSample**

##Setup

**Include the below gradle dependency**

```
repositories {
    ...
    jcenter()
}

dependencies {
    ...
    compile 'com.hs.mona:gcm-android-sdk:1.0.3'
    ...
}
```

**Generate the Sender ID and the API Key from Google Developer Console**

https://goo.gl/DvVgnJ

**Add the below changes in your AndroidManifest.xml**

```
        <!-- GCM Setup Start -->
        <receiver
            android:name="com.google.android.gms.gcm.GcmReceiver"
            android:exported="true"
            android:permission="com.google.android.c2dm.permission.SEND" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <category android:name="<your_package_name>" />
            </intent-filter>
        </receiver>

        <!-- Create a Broadcast Receiver that will recieve the push and Process them.
        Ex - PushNotificationReceiver (which is placed in package)-->
        
        <receiver
            android:name=".PushNotificationReceiver"
            android:exported="false" >
            <intent-filter>
                <action android:name="<your_action_name>" />
            </intent-filter>
        </receiver>

        <!--
         * Create New Project with GCM support => https://goo.gl/DvVgnJ
         * sender id obtained from google dev console.
         -->
        <meta-data
            android:name="com.hs.mona.gcm_sender_id"
            android:value="id:<your_sender_id>" />

        <!-- GCM Setup End -->
```

Once the dependencies are downloaded and your broadcast receiver is in place, include the below lines to perform respective actions.

**Register/Receive GCM Token**

```
  GCM.getInstance(context).generateToken();
```

In order to receive and save the token in your server, register a broadcast receiver.

```
@Override
protected void onResume() {
  super.onResume();
  LocalBroadcastManager.getInstance(this).registerReceiver(mGumTokenReceiver, new IntentFilter(GCM.GCM_TOKEN_BROADCAST_INTENT));
}

BroadcastReceiver mGumTokenReceiver = new BroadcastReceiver() {
  @Override
  public void onReceive(Context context, Intent intent) {
    Log.d("GCM Token", intent.getExtras().getString(GCM.TOKEN));
  }
};

@Override
protected void onPause() {
  super.onPause();
  LocalBroadcastManager.getInstance(this).unregisterReceiver(mGumTokenReceiver);
}
    
```

**Subcribe for a topic**

```
GCM.getInstance(context).subscribe(topicName);
```

**Unsubcribe for a topic**

```
GCM.getInstance(context).unSubscribe(topicName);
```

**Get GCM Token**

Anytime you would want to fetch the GCM token, fire this.

```
GCM.getInstance(context).getGCMToken();
```



Once these changes are done. You can test whether the changes are working with the below curl command.

```
curl -X POST -H "Content-Type:application/json" \
-H "Authorization:Key=<<API_KEY>>" \
-d '{"to":"<<GCM_TOKEN>>","data":{"action":"<<YOUR INTENT ACTION>>","message":"Hello this is a sample notification"}}' \
https://gcm-http.googleapis.com/gcm/send \
```

##That's it :) It should've worked.

If it didn't you can report the issue issues section or mail me `me@hannanshaik.com`
