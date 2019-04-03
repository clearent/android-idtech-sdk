# android-idtech-sdk

The sdk contains everything needed to add android support to your app for interaction with an IDTech card reader that processes payments using Clearent's payment processing system. The Clearent solution wraps IDTech's solution and handles the processing of the credit card data for you. Instead of handling the credit card data, a transaction token will be sent back to you via the public listener, allowing you to present this token back to Clearent when running a payment transaction (using the /rest/v2/mobile/transactions endpoint).

The reader will be shipped to you with IDTech's default EMV configuration. Clearent has an EMV configuration based on an industry certification process that needs to be applied. When the reader is connected a call will be made to Clearent's system to retrieve the EMV configuration and apply it to the reader.

See the <a href="https://github.com/clearent/Android_IDTech_VP3300_Demo" target="_blank">Demo</a> for examples.

## How to use.
1 - Contact Clearent for a reader and a public key.

2 - Required jars are located in the lib folder.

3 - Implement the PublicOnReceiverListener interface. This is the object used to communicate back to your app.

4 - Implement the ApplicationContext interface.

5 - Use the DeviceFactory to get an object based on the device you have. This object will be used by your app to interact with the reader.

6 - Call the device_configurePeripheralAndConnect() method.

  This method will call the Clearent's system to get a configuration based on the android device you are using. There are peripheral specifications, such as the baud rate on the audio jack, that can be different per device. If Clearent does not have a configuration to use there is an xml file you can provide as a fallback based on an IDTech format. Supply the file location for this via the ApplicationContext.
  A xml file has been provided in the sdk (xml folder).

7 - Call the registerListen method (when using a bluetooth device only register after you've scanned and connected).

*Do not enable usage of reader until the isReady() method of the public listener is called.

To initiate the processing of a card call device_startTransaction.

The handleConfigurationErrors method will alert you of any issues related to configuration. The handleCardProcessingResponse method can be used to monitor issues during the processing of the card. The rest of the communication can be monitored with the lcdDisplay methods of the public listener. One lcdDisplay method gets all general messages and the other is used for user interaction that can be sent back via a callback.

Successful transaction tokens of card reads will be returned via the successfulTransactionToken method of the public listener.

There will be a delay in the initial connection of the reader since the EMV configuration will need to be applied to the reader's flash memory. This is a one time configuration the first time your app starts and connects to the reader.

JavaDocs are supplied in the docs folder.

## Creating a transaction token to represent a manually entered card.

In the libs folder is a newer version of the clearent idtech android jar, clearent-idtech-android-109-1.0.23.jar which replaces clearent-idtech-android-105-1.0.21.jar.

Release Highlights

clearent-idtech-android-2.0.20190122192757.jar (deprecated). Support has been added so you can manually enter a card and have it stored using our 'mobile jwt' solution. (Idtech recommended moving to 105)

clearent-idtech-android-105-1.0.5.jar - upgraded to support the IDTech Universal SDK 105 jar (in libs). Caches a flag when the reader is configured speeding up future connections. Added retry logic during communication issues with the reader to get the configuration to apply more consistently.

clearent-idtech-android-105-1.0.8.jar - fixed an issue when swapping back and forth between readers when the auto configuration has been disabled and the applyClearentConfiguration was called at a later time. Expose the verbose logging method for support.
Exposed firmware upgrade methods. New performance change was added so when the framework identifies when the configuration has been applied it will use a SharedPreference to store this, allowing the app to be shutdown and brought back up with this knowledge still retained. So, first time reader connections will take the hit on the reader configuration and every time thereafter should be faster.

clearent-idtech-android-105-1.0.9.jar - fixed an issue when you swipe a chip card. When this happens we want to force the user to try the chip. An error is returned (terminating the transaction) called 'USE CHIP READER'. You need to initiate a new transaction and attempt a dip card. If this fails you will then have an opportunity to perform a fallback swipe. This jar does not support contactless. You will get an error when attempting to use a contactless card. To get around this with a card that also has a chip, you need to first insert the card, then initiate the transaction.

clearent-idtech-android-105-1.0.21.jar - Fixed an issue with cleaning up resources after performing a rest call (HTTP connection). Fixed an issue with identifying when a reader has been previously configured.  

clearent-idtech-android-109-1.0.23.jar - When the idtech framework tells the clearent framework it has connected, and the clearent framework attempts to get the device serial number, sometimes it fails with a Command Not Allowed error. When this happens the Clearent framework will just proceed with a default device serial number of 9999999999, allowing the integrated app to continue processing. Added remote logging to clearent's backend.


The demo has an example:

1 - Create an object that implements the ManualEntry interface.

2 - Implement interface HasManualTokenizingSupport. The following methods are now in place:

//Returns a successful transaction token

void successfulTransactionToken(TransactionToken transactionToken);

//Handle errors related to the card.

void handleCardProcessingResponse(CardProcessingResponse cardProcessingResponse);

//Handle errors related to the manual entry request.

void handleManualEntryError(String message);

3 - Create an ManualCardTokenizer object.

manualCardTokenizer = new ManualCardTokenizerImpl(this);

4 - Call the createTransactionToken method with your card object.

 manualCardTokenizer.createTransactionToken(manualEntry);
