![Screenshot](docs/clearent_logo.jpg)

# IDTech Android Framework :iphone: :credit_card:


## Overview (See [Demo under branch 2.0](https://github.com/clearent/Android_IDTech_VP3300_JDemo) for more details)

The sdk contains everything needed to add android support to your app for interaction with an IDTech card reader that processes payments using Clearent's payment processing system. The Clearent solution wraps IDTech's solution and handles the processing of the credit card data for you. Instead of handling the credit card data, a transaction token will be sent back to you via the public listener, allowing you to present this token back to Clearent when running a payment transaction (using the /rest/v2/mobile/transactions/sale endpoint).

In the libs folder is a new version of the jar, clearent-idtech-android-139-2.0.3.jar (works with IDTech's jar Universal_SDK_1.00.139.jar).


## Release Notes

[Release Notes](docs/RELEASE_NOTES.md) :eyes:

## How to use.
1 - Contact Clearent for a reader and a public key.

2 - Required jars are located in the lib folder.

3 - Implement the PublicOnReceiverListener interface. This is the object used to communicate back to your app.

4 - Implement the ApplicationContext3In1 interface.

5 - Use the DeviceFactory to get an object based on the device you have. This object will be used by your app to interact with the reader.

6 - Configure a connection object. A Bluetooth object represents a bluetooth connection. An AudioJack object represents an audio jack connection.

7 - To initiate the processing of a card call the startTransaction method and pass your connection object.

```java
ClearentResponse startTransaction (PaymentRequest paymentRequest, Connection connection);
```

Successful transaction tokens of card reads will be returned via the successfulTransactionToken method of the public listener.

8 - Monitor for feedback using the feedback callback on the public listener. Communicate back all user actions to the user.

```java
void feedback (ClearentFeedback clearentFeedback);
```

## Design recommendations

There is a device_cancelTransaction method that cancels the transaction so you can start over at any time in the process. It's recommended
you empower your user by providing this option alongside starting a transaction.

For bluetooth devices, it's recommended you give your users the ability to disconnect from the device when they want to. There is a disconnectBluetooth method for this purpose. Users can have multiple devices and in some cases multiple readers at their disposal and
giving them this ability can cut down on support calls as well as aid support when trying to troubleshoot issues.

JavaDocs are supplied in the docs folder.

## Creating a transaction token to represent a manually entered card.


## Demo

The demo shows how to handle the transaction token returned from the framework when using the reader or when manually key entering a card.
