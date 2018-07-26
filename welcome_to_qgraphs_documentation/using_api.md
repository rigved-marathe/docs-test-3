## Using API[¶](#using-api) {#using-api}

We provide programmatic access to several features of our platform.

1\. Log in on [https://app.qgraph.io](https://app.qgraph.io) and click your name on the top right of the screen. In the drop down menu that comes, select “Account Settings”. Note down the “API Token” for your account.

2\. You need to set Authorization key as `&quot;Token: &lt;your API token&gt;&quot;` in the header of your http requests. For instance, if using curl, you do it like this:

<pre>curl -H &quot;Authorization: Token abcd&quot; &lt;relevant api url&gt;
</pre>

### Sending notifications[¶](#sending-notifications) {#sending-notifications}

First you need to create a campaign. Go to [https://app.qgraph.io](https://app.qgraph.io), log in, go to “Campaigns” tab and create a new campaign. You can fill any value in the campaign, they will be overridden by what you provide in the api call.

Once you have created the campaign, proceed to edit that campaign. URL of the performance page looks like: `https://app.qgraph.io/#/edit_campaign/&lt;campaign id&gt;`. Note down the campaign id for your campaign.

Next you can make a HTTP POST request at `https://api.qgraph.io/api/v2/send-notification/`. The POST body is of the following format:

<pre>{
   &quot;cid&quot;: &lt;campaign id&gt;,

   &quot;registration_ids&quot;: [&quot;regd 1&quot;, &quot;regid 2&quot;, ..., &quot;regid n&quot; (upto 500 registration ids)]
   or
   &quot;user_ids&quot;: [&quot;userid 1&quot;, &quot;userid 2&quot;, ..., &quot;userid n&quot; (upto 500 userid)]
   or
   &quot;emails&quot;: [&quot;email 1&quot;, &quot;email 2&quot;, ..., &quot;email n&quot; (upto 500 emails)]
   or
   &quot;segment_id&quot;: &lt;segment id&gt;

   &quot;message&quot;: &lt;message in the format described below&gt;
   &quot;os&quot;: &quot;android&quot; or &quot;ios-dev&quot; or &quot;ios-prod&quot;
}
</pre>

You need to provide one of `registration_ids`, `user_ids`, `emails` or `segment_id`. In case you provide segment id, specified segment id must be a valid segment in your account, and notifications will go to that segment. To find segment id of a given segment, proceed to edit that segment. URL of the segment page is of the format `https://app.qgraph.io/#/edit_segment/&lt;segment id&gt;`.

If you want to send notification to android devices, use `android` for key `os`. If you want to send notification to ios devices, use `ios-dev` or `ios-prod`, depending on whether you want us to use development profile or production profile. (You should have uploaded the respective .pem file to us)

For a simple android notification, `message` is of the following format:

<pre>{
    &quot;type&quot;: &quot;basic&quot;,
    &quot;title&quot;: &lt;title of the notification&gt;,
    &quot;message&quot;: &lt;body of the notification&gt;,
    &quot;imageUrl&quot;: &lt;url of the icon image&gt; (optional),
    &quot;bigImageUrl&quot;: &lt;url of the big image&gt; (optional),
    &quot;deepLink&quot;: &lt;deep link of notification&gt; (optional)
    &quot;actions&quot;: [{&quot;id&quot;: 1, &quot;text&quot;: &quot;&lt;button 1 text&gt;&quot;, &quot;deepLink&quot;: &quot;&lt;deep link if any&gt;&quot;},
                {&quot;id&quot;: 2, &quot;text&quot;: &quot;&lt;button 2 text&gt;&quot;, &quot;deepLink&quot;: &quot;&lt;deep link if any&gt;&quot;},
                {&quot;id&quot;: 3, &quot;text&quot;: &quot;&lt;button 3 text&gt;&quot;, &quot;deepLink&quot;: &quot;&lt;deep link if any&gt;&quot;}] &lt;optional&gt;
}
</pre>

A note about action buttons: If you would like a campaign with action buttons to be a poll campaign (where, on button press, response of the user is recorded, but the app does not open), set the key <cite>poll</cite> to <cite>true</cite> in the <cite>message</cite>. You can send 1, 2 or 3 actions, and deep link within each button is optional.

For ios notification, `message` is of the following format:

<pre>{
     &quot;aps&quot;: {
        &quot;alert&quot;: {
            &quot;title&quot;: &lt;title of the notification&gt;,
            &quot;body&quot;: &lt;body of the notification&gt;
         }
      },
      &quot;deepLink&quot;: &lt;deep link&gt; (optional)
}
</pre>

For banner notification (available only in android), `message` is of the following format:

<pre>{
    &quot;type&quot;: &quot;banner&quot;,
    &quot;title&quot;: &lt;title of the notification&gt;,
    &quot;message&quot;: &lt;body of the notification&gt;,
    &quot;contentImageUrl&quot;: &lt;url of banner image&gt;,
    &quot;deepLink&quot;: &lt;deep link of notification&gt; (optional)
}
</pre>

For carousel notification (available only in android), `message` is of the following format:

<pre>{
    &quot;type&quot;: &quot;carousel&quot;,
    &quot;title&quot;: &lt;title of the notification&gt;,
    &quot;message&quot;: &lt;body of the notification&gt;,
    &quot;deepLink&quot;: &lt;deep link of notification&gt; (optional),
    &quot;qg_prev_button&quot;:&quot;https://cdn.qgraph.io/img/left.png&quot;,
    &quot;qg_next_button&quot;:&quot;https://cdn.qgraph.io/img/right.png&quot;,
    &quot;carousel&quot;: [{&quot;image&quot;: &quot;&lt;URL of the image&gt;&quot;,
                  &quot;deepLink&quot;: &quot;&lt;deep link for image, if any&gt;&quot;,
                  &quot;title&quot;: &quot;&lt;title of image (optional)&gt;&quot;,
                  &quot;message&quot;: &quot;&lt;message of image (optional)&gt;&quot;},
            ... you can have up to 10 such elements]
}
</pre>

Note that you can customize previous and next buttons by using your own image URL.

For slider notification (available only in android), `message` is of the following format:

<pre>{
    &quot;type&quot;: &quot;slider&quot;,
    &quot;title&quot;: &lt;title of the notification&gt;,
    &quot;message&quot;: &lt;body of the notification&gt;,
    &quot;deepLink&quot;: &lt;deep link of notification&gt;, (optional)
    &quot;qg_prev_button&quot;:&quot;https://cdn.qgraph.io/img/left.png&quot;,
    &quot;qg_next_button&quot;:&quot;https://cdn.qgraph.io/img/right.png&quot;,
    &quot;slider&quot;: [{&quot;image&quot;: &quot;&lt;URL of the image&gt;&quot;,
               &quot;deepLink&quot;: &quot;&lt;deep link for image, if any&gt;&quot;},
            ... you can have up to 10 such elements]
}
</pre>

Note that you can customize previous and next buttons by using your own image URL.

For animated banner notification (available only in android), `message` is of the following format:

<pre>{
    &quot;title&quot;: &lt;title of the notification&gt;,
    &quot;message&quot;: &lt;body of the notification&gt;,
    &quot;deepLink&quot;: &lt;deep link of notification&gt;, (optional)
    &quot;gifPlayButton&quot;: &quot;https://cdn.qgraph.io/img/video_button.png&quot;,
    &quot;type&quot;: &quot;animation&quot;
    &quot;animation&quot;: {
        &quot;millisecondsToRefresh&quot;: &lt;duration between two frames in milliseconds&gt;,
        &quot;images&quot;: [url1, url2, ..., url n]
    }
}
</pre>

#### Specifying key value pairs[¶](#specifying-key-value-pairs) {#specifying-key-value-pairs}

You can specify key value pairs in (both android and ios) notifications. To do this, include a key `qgPayload` in your `message` dictionary. `qgPayload` should contain key-value pairs. For example, a sample `message` for android would be:

<pre>{
    &quot;type&quot;: &quot;basic&quot;,
    &quot;title&quot;: &lt;title of the notification&gt;,
    &quot;message: &lt;body of the notification&gt;,
    &quot;imageUrl&quot;: &lt;url of the icon image&gt; (optional),
    &quot;bigImageUrl&quot;: &lt;url of the big image&gt; (optional),
    &quot;deepLink&quot;: &lt;deep link of notification&gt; (optional)
    &quot;qgPayload&quot;: {
        &quot;key1&quot;: &quot;some value&quot;,
        &quot;key2&quot;: 123
     }
}
</pre>

Key value pairs can then be extracted in your activity as described here: [http://docs.qgraph.io/en/latest/integrating-android-sdk.html#receiving-key-value-pairs-in-activity](http://docs.qgraph.io/en/latest/integrating-android-sdk.html#receiving-key-value-pairs-in-activity)

### Getting user profiles[¶](#getting-user-profiles) {#getting-user-profiles}

Send a GET request to [https://app.qgraph.io/api/get-user-profiles/](https://app.qgraph.io/api/get-user-profiles/). For instance, if your token is `abcd`, the relevant call in curl would be:

<pre>curl -H &quot;Authorization: Token abcd&quot; https://app.qgraph.io/api/get-user-profiles/
</pre>

#### Specifying start and end dates[¶](#specifying-start-and-end-dates) {#specifying-start-and-end-dates}

You can optionally provide parameters `start_date` and `end_date` to the API call. If these parameters are provided, the API fetches entries only for the users who have installed the app on or after `start_date`, but on or before `end_date`. The format of the both the arguments is `yyyy-mm-dd`. A sample call would be:

<pre>curl -H &quot;Authorization: Token &lt;your token&gt;&quot; https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-22&amp;end_date=2015-12-25
</pre>

For faster response times, you should retrieve the data for small date ranges.

#### Specifying OS[¶](#specifying-os) {#specifying-os}

You can specify the ios for which you want to retrieve data. You specify this by providing a query parameter `os` whose values can be `android` (for android), `ios-prod` (for ios using production profile), or `ios-dev` (for ios using development profile). Default value for `os` is `android`. Here is an example of using this variable:

<pre>curl -H &quot;Authorization: Token &lt;your token&gt;&quot; https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-22&amp;end_date=2015-12-25&amp;os=android
</pre>

#### Specifying specific fields to retrieve[¶](#specifying-specific-fields-to-retrieve) {#specifying-specific-fields-to-retrieve}

You can get following fields using the api:

1.  _firstSeen_: Date when the user installed your app
2.  _mTime_: Latest date when the user accessed your app
3.  _monthlyActivity_: Number of days in last 30 days when the user accessed your app
4.  _email_: email of the user, if available
5.  _qgCity_: city of the user, if available
6.  _uninstallTime_: date when we detected that the user has uninstalled your app
7.  _user_id_: the user id set by `setUserId()` function of the SDK
8.  _qgType_: tells whether the install is a fresh one or a reinstall
9.  _qgSrc_: source of the install, if available
10.  _gcmId_: gcm registration id of the user in case of android and device token in case of ios
11.  _deviceId_: device id of the user
12.  _advId_: advertiser id of the user

You can specify what specific fields you want. For instance, if you want to get _firstSeen_, _uninstallTime_ and _gcmId_ of all the users who installed your app between December 1, 2015 and December 3, 2015, the relevant curl call would be:

<pre>curl -H &quot;Authorization: Token &lt;your token&gt;&quot; https://app.qgraph.io/api/get-user-profiles/?start_date=2015-12-01&amp;end_date=2015-12-03&amp;fields=firstSeen,uninstallTime,gcmId
</pre>

For faster response times, you should retrieve only the fields that you need.

### Create a user uploaded segment[¶](#create-a-user-uploaded-segment) {#create-a-user-uploaded-segment}

You usually create a user uploaded segment by manually uploading a file in the Segment -&gt; Add New -&gt; Uploaded Segment. However, you can also do it using our API. Uploading a segment is a two step process:

First you need to upload the segment file. This file needs contain one field value (such as email) per line. You upload it by a command similar to this:

<pre>curl -H &#039;Authorization: Token &lt;your token&gt;&#039;\
     -H &#039;content-type: multipart/form-data&#039;\
     -F file=@/path/to/your/file\
     https://app.qgraph.io/qganalyzedata/upload-segment-file/
</pre>

This gives an output like:

<pre>{&quot;filename&quot;: &quot;upload.csv1495733409.41&quot;}
</pre>

Here `upload.csv1495733409.41` is the temporary name of the file that has been created on the server. You will need this name in the second step.

Secondly, you use above outputted temporary filename to create a segment, like this:

<pre>curl -X POST\
     -H &#039;Authorization: Token &lt;your token&gt;&#039;\
     -H &#039;appId: &lt;your app id&gt;&#039; \
     -H &#039;content-type: application/json&#039;\
     -d &#039;{&quot;name&quot;: &quot;&lt;name of the segment&gt;&quot;, &quot;description&quot;: &quot;&lt;description of the segment&gt;&quot;, &quot;filename&quot;: &quot;&lt;filename produced in step 1&gt;&quot;, &quot;field&quot;: &quot;&lt;field name whose values are present in the file&gt;&quot;}&#039;\
     https://app.qgraph.io/qganalyzedata/upload_segment/
</pre>

For instance, if the uploade file contained email, a sample command to upload the file would be:

<pre>curl -X POST\
     -H &#039;Authorization: Token &lt;your token&gt;&#039;\
     -H &#039;appId: &lt;your app id&gt;&#039;
     -H &#039;content-type: application/json&#039;\
     -d &#039;{&quot;name&quot;: &quot;my uploaded segment&quot;, &quot;description&quot;: &quot;This is a bunch of emails&quot;, &quot;filename&quot;: &quot;upload.csv1495733409.41&quot;, &quot;field&quot;: &quot;email&quot;}&#039;\
     https://app.qgraph.io/qganalyzedata/upload_segment/
</pre>

Segment is created as a result of this request.