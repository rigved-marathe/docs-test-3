## iOS SDK integration[¶](#ios-sdk-integration) {#ios-sdk-integration}

For using quantumgraph iOS-SDK, do the following steps.

1.  Install our quantumgraph iOS-SDK
2.  Generate a .p12 file
3.  Using iOS-SDK

### Installing iOS SDK Library[¶](#installing-ios-sdk-library) {#installing-ios-sdk-library}

#### Using Cocoapods[¶](#using-cocoapods) {#using-cocoapods}

The easiest way to integrate quantumgraph iOS SDK into your iOS project is to use CocoaPods.

1.  Install CocoaPods using `gem install cocoapods`

2.  If you are using Cocoapods for the first time, run `pod setup` to create a local CocoaPods spec mirror.

3.  Create a file named `Podfile` in your Xcode project directory and add the following line in it:

    <pre>pod &#039;quantumgraph&#039;
    </pre>

    Alternatively, in the terminal, in the xcode project directory, type:

    <pre>pod init
    </pre>

    Your pod file is created and now you can add `pod &#039;quantumgraph&#039;` to the specific targets you want.

4.  Run `pod install` in Xcode project directory. Cocoapods will downloads and install the quantumgraph iOS-SDK library and create a new Xcode workspace. From now on you should use this workspace.

#### Manual installation[¶](#manual-installation) {#manual-installation}

Download the SDK from here:

For Objective C: [http://app.qgraph.io/static/sdk/ios/QGSdk-ObjC-3.3.4.zip](http://app.qgraph.io/static/sdk/ios/QGSdk-ObjC-3.3.4.zip)

For Swift: [http://app.qgraph.io/static/sdk/ios/QGSdk-Swift-3.3.4.zip](http://app.qgraph.io/static/sdk/ios/QGSdk-Swift-3.3.4.zip)

*   In your Xcode project, Go to File, add new Group to your project and name it as QGSdk.

*   Add libQSdk.a and QGSdk.h in QGSdk group

*   Go to Project -&gt; Target -&gt; Build Phases. In the section “Link Binary with Libraries”, add following frameworks:

    > *   AdSupport.framework
    > *   SystemConfiguration.framework
    > *   CoreTelephony.framework
    > *   CoreLocation.framework
    > *   ImageIO.framework
    > *   MobileCoreServices.framework
    > *   libz.tbd

We track location only if you initialize location service. If you don’t add location usage key in info.plist file, we don’t track the location of the user.

However, you do not need to add these frameworks if you use cocoapods.

### Generating .p12 file[¶](#generating-p12-file) {#generating-p12-file}

#### Generating SSL Certificate for APP ID[¶](#generating-ssl-certificate-for-app-id) {#generating-ssl-certificate-for-app-id}

1.  Log in to the iOS Dev Center and select the _Certificates, Identifiers and Profiles_
2.  Go to **App IDs** in the **Identifiers** Section of the sidebar and select your app if automatically created. Skip to Step 6.
3.  To create new App click + and fill the details for App ID, App Services (Check the push notification Checkbox) and Explicit App ID(Should be same as Bundle ID in your App)
4.  You will be asked to verify the details of the app id, if everything seems okay click Register.
5.  In the _Push Notification_ row there are two orange lights that say “Configurable” in the Development and Distribution column.
6.  Select your App ID and click on **EDIT**.
7.  If Push Notification is not enabled, enable it to make it configurable.
8.  Select the Create Certificate in the Development/Production SSL Certificate
9.  In the next step it will ask you for generating a CSR

#### Generating the Certificate Signing Request[¶](#generating-the-certificate-signing-request) {#generating-the-certificate-signing-request}

1.  Open Keychain Access on your Mac and choose the menu option _Certificate Assistant_ -&gt; _Request a Certificate_ from a Certificate Authority

2.  Enter some descriptive name for Common Name (Give your app name appended by QGraph preferably to identify it)

3.  Check Save to disk option and click continue. It saves a .certSigningRequest file.

4.  In the Keys section of the Keychain Access, a new private key would have appeared with Common name specified

5.  In the “App IDs” section in the apple developer account, choose the CSR that you generated to create the push certificate

6.  Click Continue and download the certificate

7.  Double click on the downloaded certificate. This will add your certificate to your private key in your keychain

8.  Go to Keys section in the Keychain and find your private key

9.  You should be able to expand the private key and find your certificate with it. Select both the private key and the certificate after expanding (as shown in the snapshot)

    ![_images/p12-1.png](_images/p12-1.png)![_images/p12-2.png](_images/p12-2.png)
10.  Right click on it to export it as .p12 file. Make sure you are exporting 2 items as shown￼

11.  Name your file as your_app_name and save it with file format .p12

12.  You will be prompted to enter a password. You can directly click Ok or add any password to it. If you add any password please remember it and send it along with your .p12 file.

13.  In the next step, you will require your system password to finally save the file.

Your p12 file is ready to be exported. Upload it in your account. Go to “Integration” -&gt; “iOS” to upload p12 files.

### Using iOS SDK - Objective C[¶](#using-ios-sdk-objective-c) {#using-ios-sdk-objective-c}

#### Change required for APNS Token and User Tracking[¶](#change-required-for-apns-token-and-user-tracking) {#change-required-for-apns-token-and-user-tracking}

It is required by QG SDK that you enable _Background Mode_ in the _Capabilities_ section of the main app target. After enabling background modes, select **Remote Notification** as shown in the snapshot.

> ![_images/ios-1.png](_images/ios-1.png)

#### AppDelegate Changes[¶](#appdelegate-changes) {#appdelegate-changes}

To initialise the library, in AppDelegate add `#import &quot;QGSdk.h&quot;`

In `didFinishLaunchingWithOptions` method of AppDelegate, add the following code for registering for remote notification:

<pre>(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    if (floor(NSFoundationVersionNumber) &lt; NSFoundationVersionNumber_iOS_8_0) {
        // here you go with iOS 7
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: (UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    } else {
        // registering push notification in ios 8 and above
        UIUserNotificationType types = UIUserNotificationTypeAlert | UIUserNotificationTypeSound |
        UIUserNotificationTypeBadge;
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:types
        categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    }
    //replace &lt;your app id&gt; with the one you received from QGraph
    [[QGSdk getSharedInstance] onStart:@&quot;&lt;YOUR APP ID&gt;&quot; setDevProfile:NO];

    return YES;
}
</pre>

Note that `[[UIApplication sharedApplication] registerForRemoteNotifications]` is called by our SDK for iOS 8 and iOS 9.

For development profile, set Boolean to YES in the following method:

<pre>[[QGSdk getSharedInstance] onStart:@&quot;&lt;your app id&gt;&quot; setDevProfile:YES];
</pre>

Just build and run the app to make sure that you receive a message that app would like to send push notification. If you get code signing error, make sure that proper provisioning profile is selected

Add the following code in AppDelegate.m to get the device token for the user:

<pre>- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
{
        NSLog(@&quot;My token is: %@&quot;, deviceToken);
        [[QGSdk getSharedInstance] setToken:deviceToken];
}

- (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
{
        NSLog(@&quot;Failed to get token, error: %@&quot;, error.localizedDescription);
}
</pre>

QGSdk `setToken` method will log user’s token so that you can send push notification to the user.

#### Handling Push Notification[¶](#handling-push-notification) {#handling-push-notification}

Notifications are delivered while the app is in foreground, background or not running state. We can handle them in the following delegate methods.

If the remote notification is tapped, the system launches the app and the app calls its delgate’s `application:didFinishLaunchingWithOptions:` method, passing in the notification payload (for remote notifications). Although `application:didFinishLaunchingWithOptions:` is not the best place to handle the notification, getting the payload at this point gives you the opportunity to start the update process before your handler method is called.

For remote notifications, the system also calls the `application:didReceiveRemoteNotification:fetchCompletionHandler:` method of the app delegate.

You can handle the notification and its payload as described:

<pre>- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    // Payload can be handled in this way
    NSDictionary *notification = [launchOptions objectForKey:UIApplicationLaunchOptionsRemoteNotificationKey];
    if (notification) {
       // you custom methods…
    }
    return YES;
}
</pre>

The notification is delivered when the app is running in the foreground. The app calls the `application:didReceiveRemoteNotification:fetchCompletionHandler:` method of the app delegate. (If `application:didReceiveRemoteNotification:fetchCompletionHandler:` is not implemented, the system calls `application:didReceiveRemoteNotification:`.) However, it is advised to use `application:didReceiveRemoteNotification:fetchCompletionHandler:` method to handle push notification.

Implementation:

<pre>- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
  fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
      // Please make sure you add this method
      [[QGSdk getSharedInstance] application:application didReceiveRemoteNotification:userInfo];

      handler(UIBackgroundFetchResultNoData);
      NSLog(@&quot;Notification Delivered”);
  }
</pre>

You can also handle background operation using the above method once remote notification is delivered. For this make sure, wake app in background is selected while creating a campaign to send the notification.

If you have implemented `application:didReceiveRemoteNotification:` add method `[[QGSdk getSharedInstance] application:application didReceiveRemoteNotification:userInfo];` inside it. Your implementation should look like:

<pre>- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
    [[QGSdk getSharedInstance] application:application didReceiveRemoteNotification:userInfo];
}
</pre>

#### Changes for iOS 10[¶](#changes-for-ios-10) {#changes-for-ios-10}

For integrating QGraph notification SDK, you need to add Capabilities **APP GROUPS**. Go to Project &gt; Main Target &gt; **Capabilities**. Check on App Groups and add a group as below. Use your bundle id to create App Group. For example, if your bundle id is `com.company.appname`, App Group could be `group.com.company.appname.xyz`.

> ![_images/ios-10-1.png](_images/ios-10-1.png)![_images/ios-10-2.png](_images/ios-10-2.png)

You need App Group so that data can be shared between extensions. Use that App Group name in `onStart:withAppGroup:setDevProfile:` in App Delegate.

#### AppDelegate Changes for iOS 10[¶](#appdelegate-changes-for-ios-10) {#appdelegate-changes-for-ios-10}

Add framework **UserNotifications** to app target and import in app delegate

<pre>#import &lt;UserNotifications/UserNotifications.h&gt;

//Define macros for checking iOS version
#define SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(v)  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedAscending)
#define SYSTEM_VERSION_LESS_THAN(v)                 ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedAscending)

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    QGSdk *qgsdk = [QGSdk getSharedInstance];

    [qgsdk onStart:@&quot;&lt;app_id&gt;&quot; withAppGroup:@“group.com.company.product.extension” setDevProfile:true];

    if (SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(@&quot;10.0&quot;)) {
        UNAuthorizationOptions options = (UNAuthorizationOptions) (UNAuthorizationOptionAlert | UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionCarPlay);

        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        center.delegate = self;

        NSSet *categories = [NSSet setWithObjects:[qgsdk getQGSliderPushActionCategoryWithNextButtonTitle:nil withOpenAppButtonTitle:nil], nil];
        [center setNotificationCategories:categories];

        [center requestAuthorizationWithOptions:options completionHandler:^(BOOL granted, NSError *error){
            NSLog(@&quot;GRANTED: %i, Error: %@&quot;, granted, error);
        }];
    } else if (SYSTEM_VERSION_LESS_THAN(@&quot;10.0&quot;)) {
        UIUserNotificationType types = UIUserNotificationTypeAlert | UIUserNotificationTypeSound |
        UIUserNotificationTypeBadge;
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:types
                                                                                 categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    }
    return YES;
}
</pre>

**NOTE**: If you have your own existing notification action category for iOS 10, you can add it along with Graph CAROUSEL/SLIDER category implemented as above. For the carousel and slider push action buttons, you can also specify button titles. Next button will be used to animate the carousel/slider and Open App Button will open the app with deeplink if any.

#### Handling Push Notification in iOS 10[¶](#handling-push-notification-in-ios-10) {#handling-push-notification-in-ios-10}

There are new delegate methods introduced in iOS 10 to track notification and display in foreground state as well. To track notifications in background state, you need to enable background mode in the capabilities. Above all these you need to activate push notification in the capabilities. This will add entitlement files to your app target.

> ![_images/ios-10-3.png](_images/ios-10-3.png)![_images/ios-10-4.png](_images/ios-10-4.png)

1.  You might have already included this method. Please make sure `[[QGSdk getSharedInstance] application:application didReceiveRemoteNotification:userInfo];` is added in it. It is required to track notifications.

<pre>//used for silent push handling
//pass completion handler UIBackgroundFetchResult accordingly
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(nonnull NSDictionary *)userInfo fetchCompletionHandler:(nonnull void (^)(UIBackgroundFetchResult))completionHandler {
   [[QGSdk getSharedInstance] application:application didReceiveRemoteNotification:userInfo];
   completionHandler(UIBackgroundFetchResultNoData);
}
</pre>

1.  The method will be called on the delegate only if the application is in the foreground. If the method is not implemented or the handler is not called in a timely manner then the notification will not be presented. The application can choose to have the notification presented as a sound, badge, alert and/or in the notification list. This decision should be based on whether the information in the notification is otherwise visible to the user.

<pre>- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
    [[QGSdk getSharedInstance] userNotificationCenter:center willPresentNotification:notification];

    [UIApplication sharedApplication].applicationIconBadgeNumber = 0;
    UNNotificationPresentationOptions option = UNNotificationPresentationOptionBadge | UNNotificationPresentationOptionSound | UNNotificationPresentationOptionAlert;

    completionHandler(option);
}
</pre>

1.  The method will be called on the delegate when the user responded to the notification by opening the application, dismissing the notification or choosing a <cite>UNNotificationAction</cite>. The delegate must be set before the application returns from <cite>applicationDidFinishLaunching:</cite>.

**NOTE**: This method is specifically required for carousel and slider push to work. Also used to track notification_clicked event for QGraph push.

<pre>- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler {
    [[QGSdk getSharedInstance] userNotificationCenter:center didReceiveNotificationResponse:response];
    completionHandler();
}
</pre>

#### Handling Deeplink for QGraph Push[¶](#handling-deeplink-for-qgraph-push) {#handling-deeplink-for-qgraph-push}

For Push notifications deeplinks should be handled in the method <cite>didReceiveNotificationResponse:withCompletionHandler:</cite> as described below. You can get the deeplink url and then pass it to <cite>openUrl:</cite> and then you should get a callback in the <cite>application:openUrl:options</cite> where you can handle the opening of a specific page.

<pre>- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler {
    NSDictionary *userInfo = response.notification.request.content.userInfo;
    if ([userInfo objectForKey:@&quot;deepLink&quot;]) {
        NSURL *url = [NSURL URLWithString:userInfo[@&quot;deepLink&quot;]];
        dispatch_async(dispatch_get_main_queue(), ^{
            [[UIApplication sharedApplication] openURL:url];
        });
    }
    [[QGSdk getSharedInstance] userNotificationCenter:center didReceiveNotificationResponse:response];
    completionHandler();
}
</pre>

For any deeplink specified in In-App campaigns, you should get a callback in the below method. You need to handle it on your own to open any specific page.

<pre>- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary&lt;NSString *,id&gt; *)options {
    NSLog(@&quot;deeplink&quot;);
    return true;
}
</pre>

#### Adding Extensions for iOS Push with Attachment and QGraph Carousel and Slider Push[¶](#adding-extensions-for-ios-push-with-attachment-and-qgraph-carousel-and-slider-push) {#adding-extensions-for-ios-push-with-attachment-and-qgraph-carousel-and-slider-push}

In iOS 10, two frameworks has been introduced for handling push notification with content. You can have a push notification with image, gif, audio and video. Apart from that you can also have your custom UI for notifications. For this, payload can be modified and used to download content before the notification is drawn. You simply need to follow the below steps to add two of the extensions targets for handling these notifications: **Service Extension** and **Content Extension**.

Before proceeding make sure to download all the QGraph files to be used here. You should have these files with you

1.  QGNotificationSdk
2.  QGNotificationServiceExtension
3.  QGNotificationContentExtension

NOTE: These files are to be used with service and content extensions only. Do not add them to main app target.

#### Notification Service Extension[¶](#notification-service-extension) {#notification-service-extension}

Service extension is basically the target extension where you get a callback when a push is delivered to the device. You can download and create attachments here. If you fail to download the content and pass it to contentHandler within certain time, default standard notification will be drawn.

##### Adding Service extension[¶](#adding-service-extension) {#adding-service-extension-0}

1.  Add an iOS target and choose Notification Service extension and proceed. Add a product name and Finish. When created you will be **prompted to activate the target**. Once activated, you can see 3 files added, NotificationService (.h and .m ) and Info.plist.

    ![_images/ios-10-5.png](_images/ios-10-5.png)
2.  Please delete the NotificationService.h and NotificationService.m files.

3.  Add files from _QGNotificationServiceExtension_

4.  Go to project navigator and select the _Service Extension Target_

5.  Select _Capabilities_ and check on _App Group_ and select the _APP GROUP_ which you added to your main app target.

    ![_images/ios-10-6.png](_images/ios-10-6.png)
6.  Go to NotificationService.m and change your app group

<pre>static NSString *APP_GROUP = @&quot;group.com.company.product.extension&quot;;
</pre>

##### Adding Content Extension[¶](#adding-content-extension) {#adding-content-extension-0}

1.  Add an iOS target and choose Notification Content extension and proceed. Add a product name and Finish. When created you will be **prompted to activate the target**. Once activated, you can see 4 files added, NotificationViewController (.h and .m), MainInterface.storyboard and Info.plist.

    ![_images/ios-10-7.png](_images/ios-10-7.png)
2.  Please delete NotificationViewController and MainInterface.storyboard.

3.  Add these files from **QGNotificationContentExtension**.

4.  As done above, enable App Groups and select the same app group through capabilities of the content extension target.

5.  Go to NotificationViewController.m and change your app group

<pre>static NSString *APP_GROUP = @&quot;group.com.company.product.extension&quot;;
</pre>

1.  Go to Info.plist and add **UNNotificationExtensionDefaultContentHidden** (Boolean) - YES and **UNNotificationExtensionCategory** (string) - **QGCAROUSEL** in NSExtensionAttributes dict of NSExtension dict as shown in the screenshot.

    ![_images/ios-10-8.png](_images/ios-10-8.png)
2.  Add _QuartzCore.framework_ in this target.

3.  **Add QGNotificationSdk to both extension targets. Do not add it to main app target.**

**NOTE:**

1.  Please make sure **APP_GROUP** used in all the three targets are same.
2.  Set the deployment target to 10.0 in both the extensions.
3.  Remove **-ObjC/$(inherited)** (if it exists) from build settings of service and content extension targets.

#### Click Through and View Through Attribution[¶](#click-through-and-view-through-attribution) {#click-through-and-view-through-attribution}

QGraph SDK attributes events for each notification clicked or viewed. Events are attributed on the basis of time interval specified for all log events.

Currently, click through attribution works for push notification clicked (sent via QGraph) and InApp notification clicked. View through attribution works only in the case of InApp notifications.

By default click through attribution window (time interval) is set to 86400 seconds (24 hrs) and view through attribution window is set to 3600 seconds (1 hr). You can change this window any time using following apis:

<pre>// to set click through attribution window
- (void)setClickAttributionWindow:(NSInteger)seconds;
// to set view through attribution window
- (void)setAttributionWindow:(NSInteger)seconds;
</pre>

To set a custom value, pass the time interval in seconds. e.g.: to set click attribution window to be 12 hrs:

<pre>[[QGSdk getSharedInstance] setClickAttributionWindow:43200];
</pre>

To disable any of the click through or view through attribution, pass the value 0\. E.g.:

<pre>[[QGSdk getSharedInstance] setAttributionWindow:0];
</pre>

#### Configuring Batching[¶](#configuring-batching) {#configuring-batching}

Our SDK batches the network requests it makes to QGraph server, in order to optimize network usage. By default, it flushes data to the server every 15 seconds in release builds, and every second in debug builds. This interval is configurable using the following method:

<pre>[[QGSdk getSharedInstance] setFlushInterval:&lt;flush interval in seconds&gt;];
</pre>

Further, you can force the SDK to flush the data to server any time by calling the following function:

<pre>[[QGSdk getSharedInstance] flush];
</pre>

Furthermore, you can invoke a completion handler after flush using function:

<pre>[[QGSdk getSharedInstance] flushWithCompletion:^{
   //some method
}];
</pre>

#### Matching mobile app users with mobile web users[¶](#matching-mobile-app-users-with-mobile-web-users) {#matching-mobile-app-users-with-mobile-web-users}

Our SDK can help you track your mobile app users across your app and mobile web. If you want to enable this functionality, you need to add **Safari Services Framework** in your app.

If you have added Safari Services Framework in your app, but would like to _disable_ our tracking, use the following function:

<pre>[[QGSdk getSharedInstance] disableUserTrackingForSafari];
</pre>

#### In app Notification[¶](#in-app-notification) {#in-app-notification}

QGraph SDK supports InApp notification starting in sdk version 2.0.0\. InApp notification are supported in two types: Textual and Image. Visit your QGraph account to create InApp Campaigns.

These notifications are shown based on the log events app sends through our sdk and the matching conditions of the InApp Campaigns. Make sure to send appropriate log event (with parameter or valueToSum if any) for InApp notifications to work.

By default, InApp notifications are enabled. You can enable/disable it anytime using following method in the sdk:

<pre>- (void)disableInAppCampaigns:(BOOL)disabled;
</pre>

eg. to disable:

<pre>[[QGSdk getSharedInstance] disableInAppCampaigns:YES];
</pre>

Disabling it will restrict the device to get any new InApp campaigns. It will also disable InApp notification to be drawn.

For All InApp Notification, you can configure a deep link url from the dashboard while creating an InApp campaign.

There is tap event defined on textual and image InApps. When the user taps on text on textual InApp or clicks on image in the image InApp and if there is a valid deep link setup, you will get a call back in your AppDelegate.m in the following method:

<pre>- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary&lt;NSString *,id&gt; *)options;
</pre>

or:

<pre>- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(nullable NSString *)sourceApplication annotation:(id)annotation; (Deprecated in iOS_9)
</pre>

Here you can implement your deep link with the url.

#### Registering Your Actionable Notification Types[¶](#registering-your-actionable-notification-types) {#registering-your-actionable-notification-types}

Actionable notifications let you add custom action buttons to the standard iOS interfaces for local and push notifications. Actionable notifications give the user a quick and easy way to perform relevant tasks in response to a notification. Prior to iOS 8, user notifications had only one default action. In iOS 8 and later, the lock screen, notification banners, and notification entries in Notification Center can display one or two custom actions. Modal alerts can display up to four. When the user selects a custom action, iOS notifies your app so that you can perform the task associated with that action.

For defining a notification action and its category, and to handle actionable notification, please refer the description in the apple docs. ([Click here](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html))

Action Category can be set in the dashboard while sending notification. While configuring to send notification through campaigns, use the categories defined in the app.

#### Logging user profile information[¶](#logging-user-profile-information) {#logging-user-profile-information}

User profiles are information about your users, like their name, city, date of birth or any other information that you may wish to track. You log user profiles by using one or more of the following functions:

<pre>- (void)setUserId:(NSString *)userId;
</pre>

Other methods you may use to pass user profile prameters to us:

<pre>- (void)setUserId:(NSString *)userId;
- (void)setName:(NSString *)name;
- (void)setFirstName:(NSString *)name;
- (void)setLastName:(NSString *)name;
- (void)setCity:(NSString *)city;
- (void)setEmail:(NSString *)email;
- (void)setDayOfBirth:(NSNumber *)day;
- (void)setMonthOfBirth:(NSNumber *)month;
- (void)setYearOfBirth:(NSNumber *)year;
</pre>

Other than these method, you can log your own custom user parameters. You do it using:

<pre>- (void)setCustomKey:(NSString *)key withValue:(id)value;
</pre>

For example, you may wish to have the user’s current rating like this:

<pre>[[QGSdk getSharedInstance] setCustomKey:@&quot;current rating&quot; withValue:@&quot;123&quot;];
</pre>

#### Logging events information[¶](#logging-events-information) {#logging-events-information}

Events are the activities that a user performs in your app, for example, viewing the products, playing a game or listening to a music. Each event has follow properties:

1.  Name. For instance, the event of viewing a product is called `product_viewed`
2.  Optionally, some parameters. For instance, for event `product_viewed`, the parameters are `id` (the id of the product viewed), `name` (name of the product viewed), `image_url` (image url of the product viewed), `deep_link` (a deep link which takes one to the product page in the app), and so on.
3.  Optionally, a “value to sum”. This value will be summed up when doing campaing attribution. For instance, if you pass this value in your checkout completed event, you will be able to view stats such as a particular campaign has been responsible to drive Rs 84,000 worth of sales.
4.  Optionally, the currency of value to sum. Currency needs to be a 3 digit code A currency, as described [in this page](http://www.nationsonline.org/oneworld/currencies.htm).

You log events using the function `logEvent()`. It comes in four variations

*   `(void)logEvent:(NSString *)name`
*   `(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters`
*   `(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters` `withValueToSum:(NSNumber *) valueToSum`
*   `(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters` `withValueToSum:(NSNumber *) valueToSum withValueToSumCurrency:(NSString *)vtsCurr`

Once you log event information to use, you can segment users on the basis of the events (For example, you can create a segment consisting of users have not launched for past 7 days, or you can create a segment consiting of users who, in last 7 days, have purchased a product whose value is more than $1000)

You can also define your events, and your own parameters for any event. However, if you do that, you will need to sync up with us to be able to segment the users on the basis of these events or customize your creatives based on these events.

You can use the following method to pass event information to us:

<pre>- (void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters;
</pre>

Here is how you set up some of the popular events.

**Registration Completed**

This event does not have any parameters:

<pre>[[QGSdk getSharedInstance] logEvent:@&quot;registration_completed&quot; withParameters:nil];
</pre>

**Category Viewed**

This event has one paraemter:

<pre>NSMutableDictionary *categoryDetails = [[NSMutableDictionary alloc] init];
[CategoryDetails setObject:@&quot;apparels&quot; forKey: @&quot;category&quot;];

[[QGSdk getSharedInstance] logEvent:@&quot;category_viewed&quot; withParameters:categoryDetails];
</pre>

**Product Viewed**

You may choose to have the following fields:

<pre>NSMutableDictionary *productDetails = [[NSMutableDictionary alloc] init];
[productDetails setObject:@&quot;123&quot; forKey:@&quot;id&quot;];
[productDetails setObject:@&quot;Nikon Camera&quot; forKey:@&quot;name&quot;];
[productDetails setObject:@&quot;http://mysite.com/products/123.png&quot; forKey:@&quot;image_url&quot;];
[productDetails setObject:@&quot;myapp//products?id=123&quot; forKey:@&quot;deep_link&quot;];
[productDetails setObject:@&quot;black&quot; forKey:@&quot;color&quot;];
[productDetails setObject:@&quot;electronics&quot; forKey:@&quot;category&quot;];
[productDetails setObject:@&quot;small&quot; forKey:@&quot;size&quot;];
[productDetails setObject:@&quot;6999&quot; forKey:@&quot;price&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;product_viewed&quot; withParameters:productDetails];
</pre>

**Product Added to Wishlist**:

<pre>NSMutableDictionary *productDetails = [[NSMutableDictionary alloc] init];
[productDetails setObject:@&quot;123&quot; forKey:@&quot;id&quot;];
[productDetails setObject:@&quot;Nikon Camera&quot; forKey:@&quot;name&quot;];
[productDetails setObject:@&quot;http://mysite.com/products/123.png&quot; forKey:@&quot;image_url&quot;];
[productDetails setObject:@&quot;myapp//products?id=123&quot; forKey:@&quot;deep_link&quot;];
[productDetails setObject:@&quot;black&quot; forKey:@&quot;color&quot;];
[productDetails setObject:@&quot;electronics&quot; forKey:@&quot;category&quot;];
[prdouctDetails setObject:@&quot;Nikon&quot; forKey:@&quot;brand&quot;];
[productDetails setObject:@&quot;small&quot; forKey:@&quot;size&quot;];
[productDetails setObject:@&quot;6999&quot; forKey:@&quot;price&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;product_added_to_wishlist&quot; withParameters:productDetails];
</pre>

**Product Purchased**:

<pre>NSMutableDictionary *productDetails = [[NSMutableDictionary alloc] init];
[productDetails setObject:@&quot;123&quot; forKey:@&quot;id&quot;];
[productDetails setObject:@&quot;Nikon Camera&quot; forKey:@&quot;name&quot;];
[productDetails setObject:@&quot;http://mysite.com/products/123.png&quot; forKey:@&quot;image_url&quot;];
[productDetails setObject:@&quot;myapp//products?id=123&quot; forKey:@&quot;deep_link&quot;];
[productDetails setObject:@&quot;black&quot; forKey:@&quot;color&quot;];
[productDetails setObject:@&quot;electronics&quot; forKey:@&quot;category&quot;];
[productDetails setObject:@&quot;small&quot; forKey:@&quot;size&quot;];
[productDetails setObject:@&quot;6999&quot; forKey:@&quot;price&quot;];
</pre>

and then:

<pre>[[QGSdk getSharedInstance] logEvent:@&quot;product_purchased&quot; withParameters:productDetails];
</pre>

or:

<pre>[[QGSdk getSharedInstance] logEvent:@&quot;product_purchased&quot; withParameters:productDetails withValueToSum price];
</pre>

**Checkout Initiated**:

<pre>NSMutableDictionary *checkoutDetails = [[NSMutableDictionary alloc] init];
[checkoutDetails setObject:@&quot;2&quot; forKey:@&quot;num_products&quot;];
[checkoutDetails setObject:@&quot;12998.44&quot; forKey:@&quot;cart_value&quot;];
[checkoutDetails setObject:@&quot;myapp://myapp/cart&quot; forKey:@&quot;deep_link&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;checkout_initiated&quot; withParameters:checkoutDetails];
</pre>

**Product Rated**:

<pre>NSMutableDictionary *productRated = [[NSMutableDictionary alloc] init];

[productRated setObject:@&quot;1232&quot; forKey:@&quot;id&quot;];
[productRated setObject:@&quot;2&quot; forKey:@&quot;rating&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;product_rated&quot; withParameters:productRated];
</pre>

**Searched**:

<pre>NSMutableDictionary *searchDetails = [[NSMutableDictionary alloc] init];
[searchDetails setObject:@&quot;1232&quot; forKey:@&quot;id&quot;];
[searchDetails setObject:@&quot;Nikon Camera&quot; forKey:@&quot;name&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;searched&quot; withParameters:searched];
</pre>

**Reached Level**:

<pre>NSMutableDictionary *level = [[NSMutableDictionary alloc] init];
[level setObject:@&quot;23&quot; forKey:@&quot;level&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;level&quot; withParameters:level];
</pre>

**Your custom events**

Apart from above predefined events, you can create your own custom events, and have custom parameters in them:

<pre>NSMutableDictionary *event = [[NSMutableDictionary alloc] init];
[event setObject:@&quot;2&quot; forKey:@&quot;num_products&quot;];
[event setObject:@&quot;some_value&quot; forKey:@&quot;my_param&quot;];
[event setObject:@&quot;123&quot; forKey:@&quot;some_other_param&quot;];
[[QGSdk getSharedInstance] logEvent:@&quot;my_custom_event&quot; withParameters:event];
</pre>

### Using iOS SDK - Swift (3.0)[¶](#using-ios-sdk-swift-3-0) {#using-ios-sdk-swift-3-0}

#### Change required for APNS Token and User Tracking[¶](#id1) {#change-required-for-apns-token-and-user-tracking-0}

It is required by QG SDK that you enable _Background Mode_ in the _Capabilities_ section of the main app target. After enabling background modes, select **Remote Notification** as shown in the snapshot.

> ![_images/ios-1.png](_images/ios-1.png)

#### Adding bridging headers[¶](#adding-bridging-headers) {#adding-bridging-headers}

1.  In Xcode, create the header file and name it by your product module name followed by `adding-Bridging-Header.h`. File name should look like `Project_Name-Bridging-Header.h`. Please make sure this header file is in root path of the project (although you can keep it anywhere).

2.  Now Click on project tab to open _Build Settings_. In your project _target -&gt; Build Setting_, search for `Objective-C Bridging Header` and add path of the `Project_Name-Bridging-Header.h`. (_Project_Name/Project_Name-Bridging-Header.h_)

    ![_images/swift-1.png](_images/swift-1.png)
3.  Import SDK header file in the bridging header file. Your file should look like this:

    <pre>#ifndef Project_Name_Bridging_Header_h
    #define Project_Name_Bridging_Header_h
    #import &quot;QGSdk.h&quot;
    #endif /* Project_Name_Bridging_Header_h */
    </pre>

#### App Delegate Changes[¶](#app-delegate-changes) {#app-delegate-changes}

In `didFinishLaunchingWithOptions` method of AppDelegate, initialise the sdk using `onStart()` method add the following code for registering for remote notification:

NOTE: Add _UserNotifications.framework_ and import _UserNotifications_ in AppDelegate for iOS 10 notification.

Also add `UNUserNotificationCenterDelegate` in AppDelegate. iOS 10 implementation is documented below:

<pre>func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -&gt; Bool {
   let QG = QGSdk.getSharedInstance()
   QG?.onStart(&quot;&lt;your_app_id&gt;&quot;, setDevProfile: true)

   let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
   UIApplication.shared.registerUserNotificationSettings(settings)

   return true
}
</pre>

Note that `UIApplication.shared.registerForRemoteNotifications()` is called by our SDK for iOS 8 and above to track APNs Token for user tracking.

Just build and run the app to make sure that you receive a message that app would like to send push notification. If you get code signing error, make sure that proper provisioning profile is selected Add the following code in _AppDelegate.m_ to get the device token for the user:

<pre>func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
   let QG = QGSdk.getSharedInstance()
   print(&quot;My token is: \(deviceToken.description)&quot;)
   QG?.setToken(deviceToken as Data!)
}

 func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
   print(&quot;Failed to get token, error: %@&quot;, error.localizedDescription)
 }
</pre>

#### Handling Push Notification[¶](#id2) {#handling-push-notification-0}

Notifications are delivered while the app is in foreground, background or not running state. We can handle them in the following delegate methods.

If the remote notification is tapped, the system launches the app and the app calls its delgate’s `didFinishLaunchingWithOptions:` method, passing in the notification payload (for remote notifications). Although `didFinishLaunchingWithOptions:` is not the best place to handle the notification, getting the payload at this point gives you the opportunity to start the update process before your handler method is called.

For remote notifications, the system also calls the `didReceiveRemoteNotification:fetchCompletionHandler:` method of the app delegate.

The notification is delivered when the app is running in the foreground. The app calls the `application:didReceiveRemoteNotification:fetchCompletionHandler:` method of the app delegate. This method is called if the app is running in background or suspended state. (If `application:didReceiveRemoteNotification:fetchCompletionHandler:` is not implemented, the system calls `application:didReceiveRemoteNotification:`.) However, it is advised to use `application:didReceiveRemoteNotification:fetchCompletionHandler:` method to handle push notification.

Implementation:

<pre>func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -&gt; Void) {
   let QG = QGSdk.getSharedInstance()
   // to enable track click on notification
   QG?.application(application, didReceiveRemoteNotification: userInfo)
   completionHandler(UIBackgroundFetchResult.noData)
}
</pre>

You can also handle background operation using the above method once remote notification is delivered. For this make sure, wake app in background is selected while creating a campaign to send the notification. Also, enable _BACKGROUND MODE_ in capabilities and select Remote Notification.

> ![_images/swift-2.png](_images/swift-2.png)

If you have implemented `application:didReceiveRemoteNotification:` add method `QGSdk.getSharedInstance().application(application, didReceiveRemoteNotification: userInfo)` inside it. Your implementation should look like:

<pre>func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
   let QG = QGSdk.getSharedInstance()
   // to enable track click on notification
   QG?.application(application, didReceiveRemoteNotification: userInfo)
}
</pre>

#### Changes for iOS 10[¶](#id3) {#changes-for-ios-10-0}

Your basic integration for iOS 8 and 9 is complete. From iOS 10 and above two new frameworks has been introduced for notifications. For integrating QGraph notification SDK, you need to add Capabilities _APP GROUPS_. Go to _Project &gt; Main Target &gt; Capabilities_. Check on App Groups and add a group as below. Use your bundle id to create App Group. For example, if your bundle id is `com.company.appname`, App Group could be `group.com.company.appname.xyz`.

> ![_images/swift-3.png](_images/swift-3.png)![_images/swift-4.png](_images/swift-4.png)

You need App Group so that data can be shared between extensions. Use that App Group name in `onStart:withAppGroup:setDevProfile:` in App Delegate.

#### AppDelegate Changes for Swift Apps for iOS 10[¶](#appdelegate-changes-for-swift-apps-for-ios-10) {#appdelegate-changes-for-swift-apps-for-ios-10}

Add framework UserNotifications to app target and import in app delegate. Also add `UNUserNotificationCenterDelegate` in it:

<pre>import UIKit
import UserNotifications

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

    var window: UIWindow?

    let APP_GROUP = “group.com.company.product.extension”

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -&gt; Bool {
        // Override point for customization after application launch.

        let QG = QGSdk.getSharedInstance()
        QG?.onStart(“&lt;your_app_id&gt;”, withAppGroup: APP_GROUP, setDevProfile: true)

        if #available(iOS 10.0, *) {
            let center = UNUserNotificationCenter.current()
            center.delegate = self

            // adding category for QGraph Carousel and Slider Push
            let categories = NSSet(object: QG!.getQGSliderPushActionCategory(withNextButtonTitle: nil, withOpenAppButtonTitle: nil)) as! Set&lt;UNNotificationCategory&gt;
            center.setNotificationCategories(categories)

            center.requestAuthorization(options: [.badge, .carPlay, .alert, .sound]) { (granted, error) in
                print(&quot;Granted: \(granted), Error: \(error)&quot;)
            }

        } else {
            // Fallback on earlier versions
            let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
            UIApplication.shared.registerUserNotificationSettings(settings)
        }

        return true
    }

}
</pre>

**NOTE**: If you have your own existing notification action category for iOS 10, you can add it along with QGraph _CAROUSEL/SLIDER_ category. For the carousel and slider push action buttons, you can also specify button titles. Next button will be used to animate the carousel/slider and Open App Button will open the app with deeplink if any.

#### Handling Push Notification in iOS 10[¶](#id4) {#handling-push-notification-in-ios-10-0}

There are new delegate methods introduced in iOS 10 to track notification and display in foreground state as well. To track notifications in background state, you need to enable background mode in the capabilities. Above all these you need to activate push notification in the capabilities. This will add entitlement files to your app target.

1.  You might have already included this method. Please make sure `QGSdk.getSharedInstance().application(application, didReceiveRemoteNotification: userInfo)` is added in it. It is required to track notifications:

    <pre>func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -&gt; Void) {
       let QG = QGSdk.getSharedInstance()
       // to enable track click on notification
       QG?.application(application, didReceiveRemoteNotification: userInfo)
       completionHandler(UIBackgroundFetchResult.noData)
    }
    </pre>

2.  The below method will be called on the delegate only if the application is in the foreground. If the method is not implemented or the handler is not called in a timely manner then the notification will not be presented. The application can choose to have the notification presented as a sound, badge, alert and/or in the notification list. This decision should be based on whether the information in the notification is otherwise visible to the user:

    <pre>@available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -&gt; Void) {
       QGSdk.getSharedInstance().userNotificationCenter(center, willPresent: notification)
       completionHandler([.alert, .badge, .sound]);
    }
    </pre>

3.  The method will be called on the delegate when the user responded to the notification by opening the application, dismissing the notification or choosing a <cite>UNNotificationAction</cite>. The delegate must be set before the application returns from `applicationDidFinishLaunching:`.

**NOTE**: This method is specifically required for carousel and slider push to work. Also used to track notification_clicked event for QGraph push:

<pre>@available(iOS 10.0, *)
func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -&gt; Void) {
   QGSdk.getSharedInstance().userNotificationCenter(center, didReceive: response)
   completionHandler()
}
</pre>

#### Handling Deeplink for QGraph Push[¶](#id5) {#handling-deeplink-for-qgraph-push-0}

For Push notifications deeplinks should be handled in the method <cite>didReceiveNotificationResponse:withCompletionHandler:</cite> as described below. You can get the deeplink url and then pass it to <cite>openUrl:</cite> and then you should get a callback in the <cite>application:openUrl:options</cite> where you can handle the opening of a specific page.

<pre>@available(iOS 10.0, *)
func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -&gt; Void) {
    let userInfo = response.notification.request.content.userInfo
    if (userInfo[&quot;deepLink&quot;] != nil) {
        let url = URL.init(string: userInfo[&quot;deepLink&quot;] as! String)
        DispatchQueue.main.async {
            UIApplication.shared.openURL(url!)
        }
    }

    QGSdk.getSharedInstance().userNotificationCenter(center, didReceive: response)
    completionHandler()
}
</pre>

For any deeplink specified in In-App campaigns, you should get a callback in the below method. You need to handle it on your own to open any specific page.

<pre>func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -&gt; Bool {
   print(&quot;deeplink called&quot;)
   return true
}
</pre>

Finally, after adding all the above methods your app delegate should look like:

<pre>import UIKit
import UserNotifications

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

    var window: UIWindow?

    let APP_GROUP = “group.com.company.product.extension”

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -&gt; Bool {
        // Override point for customization after application launch.

        let QG = QGSdk.getSharedInstance()
        QG?.onStart(“&lt;your_app_id&gt;”, withAppGroup: APP_GROUP, setDevProfile: true)

        if #available(iOS 10.0, *) {
            let center = UNUserNotificationCenter.current()
            center.delegate = self

            let categories = NSSet(object: QG!.getQGSliderPushActionCategory(withNextButtonTitle: nil, withOpenAppButtonTitle: nil)) as! Set&lt;UNNotificationCategory&gt;
            center.setNotificationCategories(categories)

            center.requestAuthorization(options: [.badge, .carPlay, .alert, .sound]) { (granted, error) in
                print(&quot;Granted: \(granted), Error: \(error)&quot;)
            }

        } else {
            // Fallback on earlier versions
            let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
            UIApplication.shared.registerUserNotificationSettings(settings)
        }

        return true
    }

    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let QG = QGSdk.getSharedInstance()
        print(&quot;My token is: \(deviceToken.description)&quot;)
        QG?.setToken(deviceToken as Data!)
    }

    func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(&quot;Failed to get token, error: %@&quot;, error.localizedDescription)
    }

    func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -&gt; Void) {
        let QG = QGSdk.getSharedInstance()
        // to enable track click on notification
        QG?.application(application, didReceiveRemoteNotification: userInfo)
        completionHandler(UIBackgroundFetchResult.noData)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -&gt; Void) {
        QGSdk.getSharedInstance().userNotificationCenter(center, didReceive: response)
        completionHandler()
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -&gt; Void) {
        QGSdk.getSharedInstance().userNotificationCenter(center, willPresent: notification)

        completionHandler([.alert, .badge, .sound]);
    }

    func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -&gt; Bool {
        print(&quot;deeplink called&quot;)
        return true
    }
}
</pre>

#### Adding Extensions for iOS Push with Attachment and QGraph Carousel and Slider Push[¶](#id6) {#adding-extensions-for-ios-push-with-attachment-and-qgraph-carousel-and-slider-push-0}

In iOS 10, two frameworks has been introduced for handling push notification with content. You can have a push notification with image, gif, audio and video. Apart from that you can also have your custom UI for notifications. For this, payload can be modified and used to download content before the notification is drawn. You simply need to follow the below steps to add two of the extensions targets for handling these notifications: Service Extension and Content Extension. Before proceeding make sure to download all the QGraph files to be used here. You should have these files with you

1.  QGNotificationSdk
2.  QGNotificationServiceExtension
3.  QGNotificationContentExtension

**NOTE**: These files are to be used with service and content extensions only. Do not add them to main app target.

#### Notification Service Extension[¶](#id7) {#notification-service-extension-0}

Service extension is basically the target extension where you get a callback when a push is delivered to the device. You can download and create attachments here. If you fail to download the content and pass it to contentHandler within certain time, default standard notification will be drawn.

#### Adding Service extension[¶](#id8) {#adding-service-extension}

1.  Add an iOS target and choose Notification Service extension and proceed. Add a product name and Finish. When created you will be **prompted to activate the target**. Once activated, you can see 2 files added, NotificationService.swift and Info.plist in the created target.

    ![_images/swift-5.png](_images/swift-5.png)
2.  Delete the NotificationService.swift file from the service extension target.

3.  Add file NotificationService.swift from downloaded folder _QGNotificationServiceExtension_

4.  Go to project navigator and select the _Service Extension Target_

5.  Select _Capabilities_ and check on _App Group_ and select the _APP GROUP_ which you added to your main app target.

    ![_images/swift-6.png](_images/swift-6.png)
6.  Go to NotificationService.swift and change your app group:

    <pre>let APP_GROUP = &quot;group.com.company.product.extension&quot;
    </pre>

#### Adding Content Extension[¶](#id9) {#adding-content-extension}

1.  Add an iOS target and choose Notification Content extension and proceed. Add a product name and Finish. When created you will be **prompted to activate the target**. Once activated, you can see 3 files added, _NotificationViewController.swift_, _MainInterface.storyboard_ and _Info.plist_.

    ![_images/swift-7.png](_images/swift-7.png)
2.  Delete _NotificationViewController.swift_ and _MainInterface.storyboard_.

3.  Add files _NotificationViewController.swift_ and _MainInterface.storyboard_ from downloaded folder _QGNotificationContentExtension_.

4.  As done above, enable App Groups and select the same app group through capabilities of the content extension target.

5.  Go to _NotificationViewController.m_ and change your app group:

    <pre>let APP_GROUP = &quot;group.com.company.product.extension&quot;
    </pre>

6.  Go to Info.plist and add **UNNotificationExtensionDefaultContentHidden** (Boolean) - YES and **UNNotificationExtensionCategory** (string) - **QGCAROUSEL** in NSExtensionAttributes dict of NSExtension dict as shown in the screenshot.

    ![_images/swift-8.png](_images/swift-8.png)
7.  Add _QuartzCore.framework_ in this target.

8.  **Add QGNotificationSdk to both extension targets. Do not add it to main app target.**

**NOTE:**

1.  Please make sure **APP_GROUP** used in all the three targets are same.
2.  Set the deployment target to 10.0 in both the extensions.
3.  Remove **-ObjC/$(inherited)** (if it exists) from build settings of service and content extension targets.

**IMP**: You need to add _QGNotificationSdk.h_ and _iCarousel.h_ in `Bridging-Header`, so that these objective C files can be used in your extension targets.

> > Go to _Project_ -&gt; _Extension Targets_ -&gt; _Build Setting_ -&gt; _Objective-C Bridging Header_
> > 
> > Add the path to your bridging-header-file similar to Main App Target.
> 
> ![_images/swift-9.png](_images/swift-9.png)

Note: Add bridging-header-file in any one of the extensions (service extension or content extension) and then add the file path of bridging header in both the extensions.

#### Click Through and View Through Attribution[¶](#id10) {#click-through-and-view-through-attribution-0}

QGraph SDK attributes events for each notification clicked or viewed. Events are attributed on the basis of time interval specified for all log events.

Currently, click through attribution works for push notification clicked (sent via QGraph) and InApp notification clicked. View through attribution works only in the case of InApp notifications.

By default click through attribution window (time interval) is set to 86400 seconds (24 hrs) and view through attribution window is set to 3600 seconds (1 hr). You can change this window any time using following apis:

<pre>// to set click through attribution window
- (void)setClickAttributionWindow:(NSInteger)seconds;
// to set view through attribution window
- (void)setAttributionWindow:(NSInteger)seconds;
</pre>

To set a custom value, pass the time interval in seconds. e.g.: to set click attribution window to be 12 hrs:

<pre>QGSdk.getSharedInstance().setClickAttributionWindow(43200)
</pre>

To disable any of the click through or view through attribution, pass the value 0\. E.g.:

<pre>QGSdk.getSharedInstance().setAttributionWindow(0)
</pre>

#### Configuring Batching[¶](#id11) {#configuring-batching-0}

Our SDK batches the network requests it makes to QGraph server, in order to optimize network usage. By default, it flushes data to the server every 15 seconds in release builds, and every second in debug builds. This interval is configurable using the following method:

<pre>QGSdk.getSharedInstance().flushInterval = &lt;flush interval in seconds&gt;
</pre>

Further, you can force the SDK to flush the data to server any time by calling the following function:

<pre>QGSdk.getSharedInstance().flush()
</pre>

Furthermore, you can invoke a completion handler after flush using function:

<pre>QGSdk.getSharedInstance().flush(completion: {
    //some method
})
</pre>

#### Matching mobile app users with mobile web users[¶](#id12) {#matching-mobile-app-users-with-mobile-web-users-0}

Our SDK can help you track your mobile app users across your app and mobile web. If you want to enable this functionality, you need to add **Safari Services Framework** in your app.

If you have added Safari Services Framework in your app, but would like to disable our tracking, use the following function:

<pre>QGSdk.getSharedInstance().disableUserTrackingForSafari()
</pre>

#### In app Notification[¶](#id13) {#in-app-notification-0}

QGraph SDK supports InApp notification starting in sdk version 2.0.0\. InApp notification are supported in two types: Textual and Image. Visit your QGraph account to create InApp Campaigns.

These notifications are shown based on the log events app sends through our sdk and the matching conditions of the InApp Campaigns. Make sure to send appropriate log event (with parameter or valueToSum if any) for InApp notifications to work.

By default, InApp notifications are enabled. You can enable/disable it anytime using following method in the sdk:

<pre>- (void)disableInAppCampaigns:(BOOL)disabled;
</pre>

eg. to disable:

<pre>QGSdk.getSharedInstance().disable(inAppCampaigns: true)
</pre>

Disabling it will restrict the device to get any new InApp campaigns. It will also disable InApp notification to be drawn.

For All InApp Notification, you can configure a deep link url from the dashboard while creating an InApp campaign.

There is tap event defined on textual and image InApps. When the user taps on text on textual InApp or clicks on image in the image InApp and if there is a valid deep link setup, you will get a call back in your _AppDelegate.m_ in the following method:

<pre>func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -&gt; Bool
</pre>

Here you can implement your deep link with the url.

#### Registering Your Actionable Notification Types[¶](#id14) {#registering-your-actionable-notification-types-0}

Actionable notifications let you add custom action buttons to the standard iOS interfaces for local and push notifications. Actionable notifications give the user a quick and easy way to perform relevant tasks in response to a notification. Prior to iOS 8, user notifications had only one default action. In iOS 8 and later, the lock screen, notification banners, and notification entries in Notification Center can display one or two custom actions. Modal alerts can display up to four. When the user selects a custom action, iOS notifies your app so that you can perform the task associated with that action.

For defining a notification action and its category, and to handle actionable notification, please refer the description in the apple docs. ([please click here](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html))

Action Category can be set in the dashboard while sending notification. While configuring to send notification through campaigns, use the categories defined in the app.

#### Logging user profile information[¶](#id15) {#logging-user-profile-information-0}

User profiles are information about your users, like their name, city, date of birth or any other information that you may wish to track. You log user profiles by using one or more of the following functions:

<pre>- (void)setUserId:(NSString *)userId;
</pre>

Other methods you may use to pass user profile prameters to us:

<pre>- (void)setUserId:(NSString *)userId;
- (void)setName:(NSString *)name;
- (void)setFirstName:(NSString *)name;
- (void)setLastName:(NSString *)name;
- (void)setCity:(NSString *)city;
- (void)setEmail:(NSString *)email;
- (void)setDayOfBirth:(NSNumber *)day;
- (void)setMonthOfBirth:(NSNumber *)month;
- (void)setYearOfBirth:(NSNumber *)year;
</pre>

Other than these method, you can log your own custom user parameters. You do it using:

<pre>- (void)setCustomKey:(NSString *)key withValue:(id)value;
</pre>

For example, you may wish to have the user’s current rating like this:

<pre>QGSdk.getSharedInstance().setCustomKey(&quot;current rating&quot;, withValue: &quot;123&quot;)
</pre>

#### Logging events information[¶](#id16) {#logging-events-information-0}

Events are the activities that a user performs in your app, for example, viewing the products, playing a game or listening to a music. Each event has follow properties:

1.  Name. For instance, the event of viewing a product is called product_viewed.
2.  Optionally, some parameters. For instance, for event product_viewed, the parameters are id(the id of the product viewed), name (name of the product viewed), image_url (image url of the product viewed), deep_link (a deep link which takes one to the product page in the app), and so on.

3\. Optionally, a “value to sum”. This value will be summed up when doing campaing attribution. For instance, if you pass this value in your checkout completed event, you will be able to view stats such as a particular campaign has been responsible to drive Rs 84,000 worth of sales. You log events using the function `logEvent()`. It comes in four variations.

<pre>(void)logEvent:(NSString *)name

(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters

(void)logEvent:(NSString *)name withValueToSum:(NSNumber *) valueToSum

(void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters withValueToSum:(NSNumber *) valueToSum
</pre>

Once you log event information to use, you can segment users on the basis of the events (For example, you can create a segment consisting of users have not launched for past 7 days, or you can create a segment consiting of users who, in last 7 days, have purchased a product whose value is more than $1000)

You can also define your events, and your own parameters for any event. However, if you do that, you will need to sync up with us to be able to segment the users on the basis of these events or customize your creatives based on these events.

You can use the following method to pass event information to us:

<pre>- (void)logEvent:(NSString *)name withParameters:(NSDictionary *)parameters;
</pre>

Here is how you set up some of the popular events.

##### Registration Completed[¶](#registration-completed) {#registration-completed}

This event does not have any parameters:

<pre>QGSdk.getSharedInstance().logEvent(&quot;registration_completed&quot;, withParameters: nil)
</pre>

##### Category Viewed[¶](#category-viewed) {#category-viewed}

This event has one parameter:

<pre>let categoryDetails = [&quot;category&quot;: &quot;apparels&quot;]
QGSdk.getSharedInstance().logEvent(&quot;category_viewed&quot;, withParameters: categoryDetails)
</pre>

##### Product Viewed[¶](#product-viewed) {#product-viewed}

You may choose to have the following fields:

<pre>var productDetails : [String:String] = [:]
productDetails[&quot;id&quot;] = &quot;123&quot;
productDetails[&quot;name&quot;] = &quot;Nikon Camera&quot;
productDetails[&quot;image_url&quot;] = &quot;http://mysite.com/products/123.png&quot;
productDetails[&quot;deep_link&quot;] = &quot;myapp://products?id=123&quot;
productDetails[&quot;color&quot;] = &quot;black&quot;
productDetails[&quot;category&quot;] = &quot;electronics&quot;
productDetails[&quot;size&quot;] = &quot;small&quot;
productDetails[&quot;price&quot;] = &quot;6999&quot;
QGSdk.getSharedInstance().logEvent(&quot;product_viewed&quot;, withParameters: productDetails)
</pre>

##### Product Added to Wishlist[¶](#product-added-to-wishlist) {#product-added-to-wishlist}

<pre>var productDetails : [String:String] = [:]
productDetails[&quot;id&quot;] = &quot;123&quot;
productDetails[&quot;name&quot;] = &quot;Nikon Camera&quot;
productDetails[&quot;image_url&quot;] = &quot;http://mysite.com/products/123.png&quot;
productDetails[&quot;deep_link&quot;] = &quot;myapp://products?id=123&quot;
productDetails[&quot;color&quot;] = &quot;black&quot;
productDetails[&quot;category&quot;] = &quot;electronics&quot;
productDetails[&quot;size&quot;] = &quot;small&quot;
productDetails[&quot;price&quot;] = &quot;6999&quot;
QGSdk.getSharedInstance().logEvent(&quot;product_added_to_wishlist&quot;, withParameters: productDetails)
</pre>

##### Product Purchased[¶](#product-purchased) {#product-purchased}

<pre>var productDetails : [String:String] = [:]
productDetails[&quot;id&quot;] = &quot;123&quot;
productDetails[&quot;name&quot;] = &quot;Nikon Camera&quot;
productDetails[&quot;image_url&quot;] = &quot;http://mysite.com/products/123.png&quot;
productDetails[&quot;deep_link&quot;] = &quot;myapp://products?id=123&quot;
productDetails[&quot;color&quot;] = &quot;black&quot;
productDetails[&quot;category&quot;] = &quot;electronics&quot;
productDetails[&quot;size&quot;] = &quot;small&quot;
productDetails[&quot;price&quot;] = &quot;6999&quot;
</pre>

and then:

<pre>QGSdk.getSharedInstance().logEvent(&quot;product_purchased”, withParameters: productDetails)
</pre>

or:

<pre>QGSdk.getSharedInstance().logEvent(&quot;product_purchased&quot;, withParameters: productDetails, withValueToSum: price)
</pre>

##### Checkout Initiated[¶](#checkout-initiated) {#checkout-initiated}

<pre>var checkoutDetails : [String:String] = [:]
checkoutDetails[&quot;num_products&quot;] = &quot;2&quot;
checkoutDetails[&quot;cart_value&quot;] = &quot;12998.44&quot;
checkoutDetails[&quot;deep_link&quot;] = &quot;myapp://myapp/cart&quot;
QGSdk.getSharedInstance().logEvent(&quot;checkout_initiated&quot;, withParameters: checkoutDetails)
</pre>

##### Product Rated[¶](#product-rated) {#product-rated}

<pre>var productRated : [String:String] = [:]
productRated[&quot;id&quot;] = &quot;1232&quot;
productRated[&quot;rating&quot;] = &quot;2&quot;
QGSdk.getSharedInstance().logEvent(&quot;product_rated&quot;, withParameters: productRated)
</pre>

##### Searched[¶](#searched) {#searched}

<pre>var searchDetails : [String:String] = [:]
searchDetails[&quot;id&quot;] = &quot;1232&quot;
searchDetails[&quot;name&quot;] = &quot;2&quot;
QGSdk.getSharedInstance().logEvent(“searched”, withParameters: searchDetails)
</pre>

##### Reached Level[¶](#reached-level) {#reached-level}

<pre>let level = [&quot;level&quot; : &quot;23&quot;]
QGSdk.getSharedInstance().logEvent(&quot;level&quot;, withParameters: level)
</pre>

##### Your custom events[¶](#your-custom-events) {#your-custom-events}

Apart from above predefined events, you can create your own custom events, and have custom parameters in them:

<pre>var event : [String:String] = [:]
event[&quot;num_products&quot;] = &quot;2&quot;
event[&quot;my_param&quot;] = &quot;some_value&quot;
event[&quot;some_other_param&quot;] = &quot;123&quot;
QGSdk.getSharedInstance().logEvent(&quot;my_custom_event&quot;, withParameters: event)
</pre>