## Advanced Integration Topics[¶](#advanced-integration-topics) {#advanced-integration-topics}

### Passing Dates and Times to QGraph Servers[¶](#passing-dates-and-times-to-qgraph-servers) {#passing-dates-and-times-to-qgraph-servers}

Sometimes you may wish to pass date or time to QGraph servers. For instances, you may wish to pass the date of journey to QGraph servers. QGraph SDKs (iOS, android and web) take only integers, real numbers, strings and booleans as parmeters. Thus, dates and times need to be formatted as strings before they can be passed to QGraph. This section describes the format which is understood by QGraph servers.

#### Format for Date[¶](#format-for-date) {#format-for-date}

Format for date is `YYYY-MM-DD`. Thus, July 12, 2017 would be passed as string `&quot;2017-07-12&quot;`.

#### Format for Time[¶](#format-for-time) {#format-for-time}

Format for time is `HH:MM:SS` where HH is in 24 hour format. Thus, 3:10:06 PM is to be formatted as `&quot;15:10:06&quot;` and 2:04:42 AM is to be formatted as `&quot;02:04:42&quot;`.

#### Format for Datetime[¶](#format-for-datetime) {#format-for-datetime}

There are two formats for datetime:

1.  Timezone unaware datetime is of the format `YYYY-MM-DDTHH:MM:SS`. For example, `&quot;2017-07-12T15:10:06&quot;`.
2.  Timezone aware datetime format is `YYYY-MM-DDTHH:MM:SS[+/-]HH:MM`. For example, `&quot;2017-07-12T15:10:06+05:30&quot;` is a datetime in IST timezone, while `&quot;2017-07-12T15:10:06-08:00&quot;` is a datetime in PST timezone.

### Passing data to QGraph from your servers[¶](#passing-data-to-qgraph-from-your-servers) {#passing-data-to-qgraph-from-your-servers}

The usual way for QGraph to get your users’ data is through our SDKs. As your users’ interact with your android, iOS or web app, you call some functions of QGraph SDK and transmit the activities to QGraph servers.

In some cases, you would need to pass your users’ data to QGraph through your servers. For example, if you are an ecommerce website, you may wish to pass QGraph an event when a customer order has been dispatched, or when an order has been delivered to your customer. This is so that you may run a triggered campaign on, say, order delivery, sending a notification to your user when the order has been delivered.

You can pass us two kinds of information about your users:

1.  Profile information:

    This is information like name, city, or birthday of the user.

2.  Event information:

    This is information about an event like an order has been picked up, or an order has been delivered.

To pass data to QGraph servers, you make an HTTP POST request to the following URL:

<pre>https://api.quantumgraph.com/qga/clients-data/
</pre>

Note that in one call, you can transfer the information about one user only.

POST body format is as following:

<pre>{
   &quot;appId&quot;: &lt;your app id&gt;
   &quot;appSecret&quot;: &lt;your app secret&gt;,
   &quot;identifier&quot;: &lt;an identifier to identify the user&gt;
   &quot;identifier_value&quot;: &lt;value of the identifier&gt;,
   &quot;device&quot;: &lt;device type of the user&gt;
   &quot;profiles&quot;: &lt;profile information of the user, if any&gt;,
   &quot;events&quot;: &lt;even information, if any&gt;
}
</pre>

Let us consider these fields one by one.

_appId_ is the unique identifier for your account. It is available from “Account Settings” page of your account.

_appSecret_ is the unique secret for your app. It is also available from “Account Settings” page of your account.

_identifier_ is the attribute on the basis of which you identify the user. It can be one of the following: (a) _user_id_, as set by calling the function _setUserId()_ from within the SDK (b) _email_, as set by calling the function _setEmail()_ from within the SDK (c) _IDFA_, for iOS users, (d) _advertisingId_, for android users. Note that if you are identifying the users through _user_id_ or _email_, you should have passed the _user_id_ or _email_ of the user to QGraph by calling _setUserId()_ or _setEmail()_ from within the SDK.

_identifier_value_ is the value of the _identifier_ for the user for whom the request is being made.

_device_ is one of _android_, _ios_, _web_ or _fb_.

_profiles_ is an optional key. Its value is a dictionary consisting of key value paris that you want to set for the particular user. An example profile entry would be:

<pre>{
    &quot;first_name&quot;: &quot;John&quot;,
    &quot;last_name&quot;: &quot;Doe&quot;
}
</pre>

_events_ is an optional key. Its value is a list consisting of dictionaries. Each dictionary contains an _eventName_ and an optional dictionary consiting _parameters_ which in turn consists of parameter name and values. Here is an example consisting of a single event:

<pre>[{
    &quot;eventName&quot;: &quot;user_registered&quot;
}]
</pre>

And here is an example consisting of two events, one with a parameters and the other without parameters:

<pre>[{
     &quot;eventName&quot;: &quot;user_registered&quot;
 },
 {
     &quot;eventName&quot;: &quot;order_placed&quot;,
     &quot;parameters&quot;: {
         &quot;order_id&quot;: &quot;3j3dkd4k50&quot;,
         &quot;order_value&quot;:  253
     }
 }]
</pre>

For example, here is one complete request that you may make:

<pre>POST https://api.quantumgraph.com/qga/clients-data/
{
    &quot;appId&quot;: &quot;ad55582957817e511c3d&quot;,
    &quot;appSecret&quot;: &quot;2dbc2fd2358e1ea1b7a6bc08ea647b9a337ac92d&quot;,
    &quot;identifier&quot;: &quot;email&quot;,
    &quot;identifier_value&quot;: &quot;johndoe@gmail.com&quot;,
    &quot;profiles&quot;: {
        &quot;first_name&quot;: &quot;John&quot;,
        &quot;last_name&quot;: &quot;Doe&quot;
    },
    &quot;events&quot;: [{
        &quot;eventName&quot;: &quot;order_placed&quot;,
        &quot;parameters&quot;: {
            &quot;order_id&quot;: &quot;3j3dkd4k50&quot;,
            &quot;order_value&quot;:  253
        }

    }]
}
</pre>