## Android SDK integration[¶](#android-sdk-integration) {#android-sdk-integration}

### Installation in Android Studio[¶](#installation-in-android-studio) {#installation-in-android-studio}

#### A. If you use FCM[¶](#a-if-you-use-fcm) {#a-if-you-use-fcm}

1.  Add dependencies to _app/build.gradle_:

    <pre>compile &quot;com.quantumgraph.sdk:QG:4.3.1&quot;
    compile &#039;com.google.firebase:firebase-messaging:11.2.2&#039;
    </pre>

2.  If you have implemented FirebaseMessagingService in your project add the following code inside <cite>onMessageReceived(RemoteMessage remoteMessage)</cite> method:

    <pre>String from = remoteMessage.getFrom();
    Map data = remoteMessage.getData();
    if (data.containsKey(&quot;message&quot;) &amp;&amp; QG.isQGMessage(data.get(&quot;message&quot;).toString())) {
          Bundle qgData = new Bundle();
          qgData.putString(&quot;message&quot;, data.get(&quot;message&quot;).toString());
          Context context = getApplicationContext();
          if (from == null || context == null) {
              return;
          }
          Intent intent = new Intent(context, NotificationJobIntentService.class);
          intent.setAction(&quot;QG&quot;);
          intent.putExtras(qgData);
          JobIntentService.enqueueWork(context, NotificationJobIntentService.class, 1000, intent);
          return;
      }
    </pre>

3.  If you have implemented FirebaseInstanceIdService, add the following code inside <cite>onTokenRefresh()</cite>:

    <pre>QG.logFcmId(getApplicationContext());
    </pre>

4.  Additional settings:

    > *   If you would like to reach out to uninstalled users by email, add following line in _app/src/main/AndroidManifest.xml_ outside the _&lt;application&gt;_ tag:
    >     
    >     
    >     
    >     <pre>&lt;uses-permission android:name=&quot;android.permission.GET_ACCOUNTS&quot; /&gt;
    >     </pre>
    >     
    >     
    > *   If you would like us to track the city of the user, add the following line in _app/src/main/AndroidManifest.xml_ outside the _&lt;application&gt;_ tag:
    >     
    >     
    >     
    >     <pre>&lt;uses-permission android:name=&quot;android.permission.ACCESS_COARSE_LOCATION&quot; /&gt;
    >     &lt;uses-permission android:name=&quot;android.permission.ACCESS_FINE_LOCATION&quot; /&gt;
    >     </pre>
    >     
    >     
    > *   If you would like us to track device id the user, add the following line in _app/src/main/AndroidManifest.xml_ outside the _&lt;application&gt;_ tag:
    >     
    >     
    >     
    >     <pre>&lt;uses-permission android:name=&quot;android.permission.READ_PHONE_STATE&quot; /&gt;
    >     </pre>

##### Note[¶](#note) {#note}

If while building your project, you get a `ClassNotFoundException`, check the following

1.  Check that you are using Support Library version 26 or above

<pre>compile &#039;com.android.support:appcompat-v7:26.0.1&#039;
</pre>

1.  Check that you have included maven properly in `project/build.gradle`

<pre>allprojects {
  repositories {
    jcenter()
    maven {
      url &quot;https://maven.google.com&quot;
    }
  }
}
</pre>

#### B. If you use GCM[¶](#b-if-you-use-gcm) {#b-if-you-use-gcm}

We prefer that you integrate using FCM. However, if you are already using GCM (and have GCM tokens), follow the following steps

1.  Add dependencies to _app/build.gradle_:

    <pre>compile &quot;com.quantumgraph.sdk:QG:2.3.5&quot;
    compile &quot;com.google.android.gms:play-services-gcm:11.2.2&quot;
    </pre>

2.  Additional settings:

    > *   If you would like to reach out to uninstalled users by email, add following line in _app/src/main/AndroidManifest.xml_ outside the _&lt;application&gt;_ tag:
    >     
    >     
    >     
    >     <pre>&lt;uses-permission android:name=&quot;android.permission.GET_ACCOUNTS&quot; /&gt;
    >     </pre>
    >     
    >     
    > *   If you would like us to track the city of the user, add the following line in _app/src/main/AndroidManifest.xml_ outside the _&lt;application&gt;_ tag:
    >     
    >     
    >     
    >     <pre>&lt;uses-permission android:name=&quot;android.permission.ACCESS_COARSE_LOCATION&quot; /&gt;
    >     &lt;uses-permission android:name=&quot;android.permission.ACCESS_FINE_LOCATION&quot; /&gt;
    >     </pre>
    >     
    >     
    > *   If you would like us to track device id the user, add the following line in _app/src/main/AndroidManifest.xml_ outside the _&lt;application&gt;_ tag:
    >     
    >     
    >     
    >     <pre>&lt;uses-permission android:name=&quot;android.permission.READ_PHONE_STATE&quot; /&gt;
    >     </pre>

3.  If you use your own service that extends `GCMListenerService`, following code snippet must be added in your service:

    <pre>@Override
    public void onMessageReceived(String from, Bundle data) {
        if (data.containsKey(&quot;message&quot;) &amp;&amp; QG.isQGMessage(data.getString(&quot;message&quot;))) {
            Context context = getApplicationContext();
            Intent intent = new Intent(context, NotificationJobIntentService.class);
            intent.setAction(&quot;QG&quot;);
            intent.putExtras(data);
            JobIntentService.enqueueWork(context, NotificationJobIntentService.class, 1000, intent);
            return;
        }
    }
    </pre>

### Installation in Cordova[¶](#installation-in-cordova) {#installation-in-cordova}

QGraph supports apps built with Cordova. Please look at our github plugin for cordova [here](https://github.com/quantumgraph/cordova).

### Using Android SDK[¶](#using-android-sdk) {#using-android-sdk}

Follow these steps to use our android SDK

#### Import QG SDK in your activity[¶](#import-qg-sdk-in-your-activity) {#import-qg-sdk-in-your-activity}

In all the activity classes, starting with the class for the main activity, import QG SDK:

<pre>import com.quantumgraph.sdk.QG;
</pre>

#### Initialization of SDK[¶](#initialization-of-sdk) {#initialization-of-sdk}

1.  Define a variable called `qg` in your activity:

    <pre>private QG qg;
    </pre>

2.  Add a line in `onCreate()` of your activity. If you do not use Firebase in your app, add the following:

    <pre>QG.initializeSdk(getApplication(), &lt;your app id&gt;);
    </pre>

    If you use Firebase in your app, you need to know your sender id. In that case, add the following:

    <pre>QG.initializeSdk(getApplication(), &lt;your app id&gt;, &lt;your sender id&gt;);
    </pre>

    App id for your app is available from the settings page of our webapp. To get your sender id, go to your project settings in [https://console.firebase.google.com](https://console.firebase.google.com). (You need to access “Cloud Messaging” tab in Firebase console).

    ![_images/fcm-console.png](_images/fcm-console.png)
3.  In the `onStart()` function of your activity, add the following:

    <pre>qg = QG.getInstance(getApplicationContext());
    qg.onStart();
    </pre>

#### Logging user profiles[¶](#logging-user-profiles) {#logging-user-profiles}

User profiles are information about your users, like their name, city, date of birth or any other information that you may wish to track. You log user profiles by using one or more of the following functions:

<pre>qg.setUserId(String userId);
</pre>

userId is the id of the user. It might be email, or username, or facebook id, or any other form of id that you may wish to keep.

Other functions that you may use are:

<pre>qg.setName(String name);
qg.setFirstName(String firstName);
qg.setLastName(String lastName);
qg.setCity(String city);
qg.setEmail(String email);
qg.setDayOfBirth(int day);
qg.setMonthOfBirth(int month);
qg.setYearOfBirth(int year);
qg.setPhoneNumber(String phoneNo);
</pre>

Other than these functions, you can log your own custom user parameters. You do it using:

<pre>qg.setCustomUserParameter(String key, E value);
</pre>

For instance, you may wish to have the user’s current rating like this:

<pre>qg.setCustomUserParameter(&quot;current_rating&quot;, 123);
</pre>

As implied by the function definition, the value can be of any data type.

Once user profile is set, you can use this to create personalized messages (For example: “Hi John, exciting deals are available in Mountain View”), or to create user segments (For example you can create a segment of users who were born after 1990 and live in Mountain View)

Apart from above user profile parameters, you can log the UTM source through which the user installed the app, using the following functions:

<pre>qg.setUtmSource(String utmSource);
qg.setUtmMedium(String utmMedium);
qg.setUtmTerm(String utmTerm);
qg.setUtmContent(String utmContent);
qg.setUtmCampaign(String utmCampaign);
</pre>

#### Logging events[¶](#logging-events) {#logging-events}

Events are the activities that a user performs in your app, for example, viewing the products, playing a game or listening to a music. Each event has a name (for instance, the event of viewing a product can be called `product_viewed`), and can have some parameters. For instance, for event `product_viewed`, the parameters can be `id` (the id of the product viewed), `name` (name of the product viewed), `image_url` (image url of the product viewed), `deep_link` (a deep link which takes one to the product page in the app), and so on.

Once you log event information to use, you can segment users on the basis of the events (For example, you can create a segment consisting of users have not launched for past 7 days, or you can create a segment consiting of users who, in last 7 days, have purchased a product whose value is more than $1000)

You can also define your events, and your own parameters for any event. However, if you do that, you will need to sync up with us to be able to segment the users on the basis of these events or customize your creatives based on these events.

You can optionally log a “value to sum” with an event. This value will be summed up when doing campaing attribution. For instance, if you pass this value in your checkout completed event, you will be able to view stats such as a particular campaign has been responsible to drive Rs 84,000 worth of sales. You can also optionally provide a currency code for the value to sum. Currency needs to be a 3 digit code A currency, as described [in this page](http://www.nationsonline.org/oneworld/currencies.htm).

Thus, there are four variants of the function `logEvent()` which logs the event

*   `logEvent(String eventName)`
*   `logEvent(String eventName, JSONObject parameters)`
*   `logEvent(String eventName, JSONObject parameters, double valueToSum)`
*   `logEvent(String eventName, JSONObject parameters, double valueToSum, String valueToSumCurrency)`

Here is how you set up some of the popular events.

**Registration Completed**

This event does not have any parameters:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject registrationDetails = new JSONObject();
try {
   qg.logEvent(&quot;registration_completed&quot;, registrationDetails);
} catch (JSONException e) {
}
</pre>

**Category Viewed**

This event has one paraemter:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject categoryDetails = new JSONObject();
try {
   categoryDetails.put(&quot;category&quot;, &quot;apparels&quot;);
} catch (JsonException e) {
}
qg.logEvent(&quot;category_viewed&quot;, categoryDetails);
</pre>

**Product Viewed**

You may choose to have the following fields:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject productDetails = new JSONObject();
try {
   productDetails.put(&quot;id&quot;, &quot;123&quot;);
   productDetails.put(&quot;name&quot;, &quot;Nikon Camera&quot;);
   productDetails.put(&quot;image_url&quot;, &quot;http://mysite.com/products/123.png&quot;);
   productDetails.put(&quot;deep_link&quot;, &quot;myapp//products?id=123&quot;);
   productDetails.put(&quot;type&quot;, &quot;new&quot;);
   productDetails.put(&quot;category&quot;, &quot;electronics&quot;);
   productDetails.put(&quot;brand&quot;, &quot;Nikon&quot;);
   productDetails.put(&quot;color&quot;, &quot;white&quot;);
   productDetails.put(&quot;size&quot;, &quot;small&quot;);
   productDetails.put(&quot;price&quot;, 6999);
} catch (JsonException e) {
}
qg.logEvent(&quot;product_viewed&quot;, productDetails);
</pre>

**Product Added to Cart**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject productDetails = new JSONObject();
try {
   productDetails.put(&quot;id&quot;, &quot;123&quot;);
   productDetails.put(&quot;name&quot;, &quot;Nikon Camera&quot;);
   productDetails.put(&quot;image_url&quot;, &quot;http://mysite.com/products/123.png&quot;);
   productDetails.put(&quot;deep_link&quot;, &quot;myapp//products?id=123&quot;);
   productDetails.put(&quot;type&quot;, &quot;new&quot;);
   productDetails.put(&quot;category&quot;, &quot;electronics&quot;);
   productDetails.put(&quot;brand&quot;, &quot;Nikon&quot;);
   productDetails.put(&quot;color&quot;, &quot;white&quot;);
   productDetails.put(&quot;size&quot;, &quot;small&quot;);
   productDetails.put(&quot;price&quot;, 6999);
} catch (JsonException e) {
}
qg.logEvent(&quot;product_added_to_cart&quot;, productDetails);
</pre>

**Product Added to Wishlist**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject productDetails = new JSONObject();
try {
   productDetails.put(&quot;id&quot;, &quot;123&quot;);
   productDetails.put(&quot;name&quot;, &quot;Nikon Camera&quot;);
   productDetails.put(&quot;image_url&quot;, &quot;http://mysite.com/products/123.png&quot;);
   productDetails.put(&quot;deep_link&quot;, &quot;myapp//products?id=123&quot;);
   productDetails.put(&quot;type&quot;, &quot;new&quot;);
   productDetails.put(&quot;category&quot;, &quot;electronics&quot;);
   productDetails.put(&quot;brand&quot;, &quot;Nikon&quot;);
   productDetails.put(&quot;color&quot;, &quot;white&quot;);
   productDetails.put(&quot;size&quot;, &quot;small&quot;);
   productDetails.put(&quot;price&quot;, 6999);
} catch (JsonException e) {
}
qg.logEvent(&quot;product_added_to_wishlist&quot;, productDetails);
</pre>

**Product Purchased**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject productDetails = new JSONObject();
try {
   productDetails.put(&quot;id&quot;, &quot;123&quot;);
   productDetails.put(&quot;name&quot;, &quot;Nikon Camera&quot;);
   productDetails.put(&quot;image_url&quot;, &quot;http://mysite.com/products/123.png&quot;);
   productDetails.put(&quot;deep_link&quot;, &quot;myapp//products?id=123&quot;);
   productDetails.put(&quot;type&quot;, &quot;new&quot;);
   productDetails.put(&quot;category&quot;, &quot;electronics&quot;);
   productDetails.put(&quot;brand&quot;, &quot;Nikon&quot;);
   productDetails.put(&quot;color&quot;, &quot;white&quot;);
   productDetails.put(&quot;size&quot;, &quot;small&quot;);
   productDetails.put(&quot;price&quot;, 6999);
} catch (JsonException e) {
}
qg.logEvent(&quot;product_purchased&quot;, productDetails, 6999);
/* Or if you do not want to pass the third argument, you can simply write
qg.logEvent(&quot;product_purchased&quot;, productDetails);*/
</pre>

**Checkout Initiated**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject checkoutDetails = new JSONObject();
try {
   checkoutDetails.put(&quot;num_products&quot;, 2);
   checkoutDetails.put(&quot;cart_value&quot;, 12998.44);
   checkoutDetails.put(&quot;deep_link&quot;, &quot;myapp://myapp/cart&quot;);
} catch (JsonException e) {
}
qg.logEvent(&quot;checkout_initiated&quot;, checkoutDetails);
</pre>

**Checkout Completed**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject checkoutCompleted = new JSONObject();
try {
   checkoutDetails.put(&quot;num_products&quot;, 2);
   checkoutDetails.put(&quot;cart_value&quot;, 12998.44);
   checkoutDetails.put(&quot;deep_link&quot;, &quot;myapp://myapp/cart&quot;);
} catch (JsonException e) {
}
qg.logEvent(&quot;checkout_completed&quot;, checkoutDetails, 12998.44);
/* Or if you do not want to pass the third argument, you can simply write
qg.logEvent(&quot;product_purchased&quot;, productDetails);*/
</pre>

**Product Rated**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject rating = new JSONObject();
try {
   rating.put(&quot;id&quot;, &quot;1232&quot;);
   rating.put(&quot;rating&quot;, 2);
} catch (JsonException e) {
}
qg.logEvent(&quot;product_rated&quot;, rating);
</pre>

**Searched**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject search = new JSONObject();
try {
   search.put(&quot;id&quot;, &quot;1232&quot;);
   search.put(&quot;rating&quot;, 2);
} catch (JsonException e) {
}
qg.logEvent(&quot;product_rated&quot;, rating);
</pre>

**Reached Level**:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject level = new JSONObject();
try {
   level.put(&quot;level&quot;, 23);
} catch (JsonException e) {
}
qg.logEvent(&quot;level&quot;, rating);
</pre>

**Your custom events**

Apart from above predefined events, you can create your own custom events, and have custom parameters in them:

<pre>QG qg = QG.getInstance(getApplicationContext());
JSONObject json = new JSONObject();
try {
   json.put(&quot;my_param&quot;, &quot;some value&quot;);
   json.put(&quot;some_other_param&quot;, 123);
   json.put(&quot;what_ever&quot;, 1234.23);
} catch (JsonException e) {
}
qg.logEvent(&quot;my_custom_event&quot;, json);
</pre>

#### Retrieving stored notifications[¶](#retrieving-stored-notifications) {#retrieving-stored-notifications}

We provide the facility to store the notifications that you send. To enable notification storage, please contact us at app@qgraph.io. We automatically store the notifications which arrive at the SDK, and you can access them at any point of time. Here is how you access stored notifications:

<pre>JSONArray storedNotifications = QG.getInstance(context).getStoredNotifications();
</pre>

Different notifications have different fields. All of them have a `title` and `message`. They may also have `imageUrl` (URL of icon image), `bigImageUrl` (URL of the big image), `deepLink` and some other fields depending on the type of the notification.

#### Configuring Batching[¶](#configuring-batching) {#configuring-batching}

Our SDK batches the network requests it makes to QGraph server, in order to optimize network usage. It flushes data to the server every 15 seconds, or when number data points exceed 100.

You can force the SDK to flush the data to server any time by calling the following function:

<pre>QG.getSharedInstance(context).flush();
</pre>

#### InApp Notifications[¶](#inapp-notifications) {#inapp-notifications}

InApp notfications work by default and you do not have to do anything specific.

In case you wish to disable in-app notifications in some Activity, call:

<pre>QG.getInstance(context).hideInApp(Activity activityInWhichInAppIsToBeHidden)
</pre>

Note that `hideInApp(activity)` should be called before `onStart()` of activity in which you wish to hide in-app gets called.

#### Event Attribution[¶](#event-attribution) {#event-attribution}

To track how QG notifications are affecting the metrics on your app, we attribute some of your app events to QG notifications. We support two types of attributions: view through attribution and click through attribution. We view-through attribute an event to a notification if the event happens within 1 hour (this can be configured) of a user receiving a notification. We click-through attribute an event to a notification if the event happens within 24 hours (this can be configured) of a user receiving a notification.

You can see the attribution metrics on the performance page of the campaigns:

> ![_images/attributed-events.png](_images/attributed-events.png)

You can change view through attribution window by using following function:

<pre>QG.getInstance(context).setAttributionWindow(long seconds);
</pre>

You can change click through attribution window by using following function:

<pre>QG.getInstance(context).setClickAttributionWindow(long seconds);
</pre>

### Notification checklist[¶](#notification-checklist) {#notification-checklist}

#### Launcher image[¶](#launcher-image) {#launcher-image}

Make sure that you have an image called `ic_launcher.png` in your `drawable/` folder. We use this image to display as icon image if you don’t set an icon image explicitly. This image should be 192px x 192px or larger, with an aspect ratio of 1:1.

#### Notification image[¶](#notification-image) {#notification-image}

Make sure that you have an image called `ic_notification.png` in your `drawable/` foler. This is the image shown in the status bar when a notification arrives. As per Android guidelines ([http://developer.android.com/design/patterns/notifications.html](http://developer.android.com/design/patterns/notifications.html)) this image should be a white image on a transparent background. The size of this image should be 72px x 72px or larger, with an aspect ratio of 1:1\. This is what ic_notification.png should look like: [https://developer.android.com/samples/MediaBrowserService/res/drawable-hdpi/ic_notification.png](https://developer.android.com/samples/MediaBrowserService/res/drawable-hdpi/ic_notification.png)

#### Recommended sizes of images[¶](#recommended-sizes-of-images) {#recommended-sizes-of-images}

Follow are the recommended sizes of images:

1.  Big Image Notification - Big image should be 1024px x 512px or larger, with an aspect ratio close to 2:1
2.  Icon Image - Icon image should be 192px x 192px or larger, with aspect ratio of 1:1
3.  Carousel Notification - Recommended image size is 600px x 600px, with aspect ratio of 1:1
4.  Slider Notification - 1024px x 512px or larger, with aspect ratio close to 2:1
5.  Static Banner Notification - 1024px x 170px with an aspect ratio of 6:1
6.  Animated Banner Notification - a series of images of 1024px x 170px with an aspect ratio of 6:1

Depending on the screen’s resolution android crops the image to fit it into the container. For this, we recommend that you do not have any text in the 10% margins of Big Image and Carousel.

#### If you use your own Service to extend GCMListenerService[¶](#if-you-use-your-own-service-to-extend-gcmlistenerservice) {#if-you-use-your-own-service-to-extend-gcmlistenerservice}

If you use your own service that extends `GCMListenerService`, following code snippet must be added in your service:

<pre>@Override
public void onMessageReceived(String from, Bundle data) {
    if (data.containsKey(&quot;message&quot;) &amp;&amp; QG.isQGMessage(data.getString(&quot;message&quot;))) {
        Context context = getApplicationContext();
        Intent intent = new Intent(context, NotificationJobIntentService.class);
        intent.setAction(&quot;QG&quot;);
        intent.putExtras(data);
        JobIntentService.enqueueWork(context, NotificationJobIntentService.class, 1000, intent);
        return;
    }
}
</pre>

#### Receiving key value pairs in activity[¶](#receiving-key-value-pairs-in-activity) {#receiving-key-value-pairs-in-activity}

If you have set key value pairs in the campaign you can get them in the activity. Let’s say you passed a key valled `myKey` in the campaign, then you can get its value as following:

<pre>@override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   Intent intent = getIntent();
   Bundle bundle = intent.getExtras();
   String val = null;
   if (bundle != null) {
       val = bundle.getString(&quot;myKey&quot;);
   }

   /* More code */
}
</pre>