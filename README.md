# SMSKit - Android SMS and MMS Library

![Promo](https://raw.githubusercontent.com/klinker41/android-smsmms/master/android-messaging.png)

SMSKit bundles the APIs that Google has left out of the Android ecosystem for easily sending SMS or MMS messages, without digging through source code. This is a **fork** of the original [Android SMS/MMS Library](https://github.com/klinker41/android-smsmms) developed by klinker41.

This fork is specifically designed for the [weMessage App](https://wemessageapp.com), so if you would like to make a pull request please submit it to the original.

---

## Library Overview

**Sending Messages**

First, create a Settings object with all of the required information for what you need to do. If you don't set something, then it will be set to the default and that feature may not work.

``` java
Settings sendSettings = new Settings();
```

Next, attach that settings object to a Transaction object, which will perform the sending.

``` java
Transaction sendTransaction = new Transaction(mContext, sendSettings);
```

Now, create a Message object for the message you want to send.

``` java
Message mMessage = new Message(textToSend, addressToSendTo);
mMessage.setImage(mBitmap);   // not necessary for voice or sms messages
```

And then send the message.

``` java
sendTransaction.sendNewMessage(message, threadId)
```

Note: threadId can be set to Transaction.NO_THREAD_ID, but this sometimes results in a new thread being created instead of the message being added to an existing thread

If you want to send MMS messages, be sure to add this to your manifest:

``` xml
<service android:name="com.android.mms.transaction.TransactionService"/>
```

You'll also need to register a few receivers for when the messages have been sent and for delivery reports. In your manifest, add these lines:

```xml
<receiver
    android:name="com.klinker.android.send_message.SentReceiver"
    android:taskAffinity="[insert package name here].SMS_SENT"/>

<receiver
    android:name="com.klinker.android.send_message.DeliveredReceiver"
    android:taskAffinity="[insert package name here].SMS_DELIVERED"/>

<!-- Your custom receiver which is child of com.klinker.android.send_message.MmsSentReceiver -->
<receiver
    android:name="[insert your custom receiver here. eg. com.example.sms_mms.receivers.MyMmsSentReceiver]"
    android:taskAffinity="com.klinker.android.messaging.MMS_SENT"/>
```

Be sure to replace the [insert package name here] with your package name defined in the manifest.

Lastly, you'll need to include permissions in your manifest depending on what you want to do. Here are all of them (there are a lot unfortunately):

```xml
<uses-permission android:name="android.permission.SEND_SMS"/>
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.WRITE_SMS"/>
<uses-permission android:name="android.permission.RECEIVE_SMS"/>
<uses-permission android:name="android.permission.RECEIVE_MMS"/>
<uses-permission android:name="android.permission.VIBRATE"/>
<uses-permission android:name="android.provider.Telephony.SMS_RECEIVED" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
```

### Using on Lollipop+ devices

If you are using this on devices with Android 5.0 or higher, use this method:

``` java
Settings settings = new Settings();
settings.setUseSystemSending(true);
Transaction transaction = new Transaction(mContext, settings);
Message message = new Message(textToSend, addressToSendTo);
message.setImage(mBitmap);
transaction.sendNewMessage(message, threadId)
```

This will invoke the MMS APIs that were made available in SDK 21. It is recommended over the old approach since you no longer need to ask the user for APNs and much less setup is necessary. The option will only be applied when the device has SDK 21 or higher, otherwise it will fall back to the old method.

For full details on how to implement, please check out the sample application on the Original Project. It has more information on permissions, creating a transaction and sending a message.

---

### Dependencies Information

To include in your gradle project:

```groovy
compile 'com.klinkerapps:android-smsmms:4.3.0'
```

---

## License

    Copyright 2017 Jacob Klinker

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
