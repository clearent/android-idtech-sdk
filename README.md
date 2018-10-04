# android-idtech-sdk


This solution will allow you to interact with an IDTech card reader and process payments using Clearent's payment processing system. It wraps IDTech's solution and handles the processing of the credit card data for you. Instead of handling the credit card data, a transaction token will be sent back to you via the public listener, allowing you to present this token when running a payment transaction.

The reader will be shipped to you with a default EMV configuration. Clearent has an EMV configuration based on an industry certification process that needs to be applied. When the reader is connected a call will be made to Clearent's system to retrieve the EMV configuration and apply it to the reader.

See the <a href="https://github.com/clearent/Android_IDTech_VP3300_Demo" target="_blank">Demo</a> for examples.

General usage
Implement the PublicOnReceiverListener interface. This is the object used to communicate with your app.
Implement the ApplicationContext interface. You will need to contact Clearent to get a public key.
Use the DeviceFactory to get an object based on the device you have.
Call the device_configurePeripheralAndConnect() method. This is a convenience method that will call the Clearent's system to get a configuration based on the android device you are using. There are peripheral specifications, such as the baud rate on the audio jack, that can be different per device. If Clearent does not have a configuration to use there is an xml file you can provide as a fallback that is based on an IDTech format. Supply the file location for this via the ApplicationContext.
Call the registerListen method (when using a bluetooth device only register after you've scanned and connected).
Do not enable usage of reader until the isReady() method of the public listener is called.
To start a read of a card call emv_startTransaction or device_startTransaction.
Follow the instructions coming back from the lcdDisplay of the public listener. See demo for usage. One lcdDisplay method gets all general messages and the other is used for user interaction that can be sent back via a callback.
Successful transaction tokens of card reads will be returned via the successfulTransactionToken of the public listener
