# android-idtech-sdk

This sdk contains everything needed to add android support to your app for interaction with an IDTech card reader that processes payments using Clearent's payment processing system. The Clearent solution wraps IDTech's solution and handles the processing of the credit card data for you. Instead of handling the credit card data, a transaction token will be sent back to you via the public listener, allowing you to present this token back to Clearent when running a payment transaction (using the /rest/v2/mobile/transactions endpoint).

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
