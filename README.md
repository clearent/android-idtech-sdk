![Screenshot](docs/clearent_logo.jpg)

# IDTech Android Framework :iphone: :credit_card:


## Overview (See [Demo](https://github.com/clearent/Android_IDTech_VP3300_Demo) for more details)

The sdk contains everything needed to add android support to your app for interaction with an IDTech card reader that processes payments using Clearent's payment processing system. The Clearent solution wraps IDTech's solution and handles the processing of the credit card data for you. Instead of handling the credit card data, a transaction token will be sent back to you via the public listener, allowing you to present this token back to Clearent when running a payment transaction (using the /rest/v2/mobile/transactions/sale endpoint).

In the libs folder is a new version of the jar, clearent-idtech-android-126-1.0.0.jar (works with IDTech's jar Universal_SDK_1.00.126.jar). This README has been updated to reflect the latest changes.

:warning: This release has only passed contactless testing on VP3300 bluetooth encrypted and unencrypted readers using idtech firmware version .151. We are still testing the audio jack readers to confirm IDTech's firmware version works with this release for contactless.
If you plan on upgrading to use with the audio jack readers you should keep contactless disabled. If you want to upgrade to this release using encrypted audio jack readers using firmware version .064 you can do so as long as contactless is disabled.

## Release Notes

[Release Notes](docs/RELEASE_NOTES.md) :eyes:

## How to use.
1 - Contact Clearent for a reader and a public key.

2 - Required jars are located in the lib folder.

3 - Implement the PublicOnReceiverListener interface. This is the object used to communicate back to your app.

4 - Implement the ApplicationContext interface for 2 in 1 mode (DIP/SWIPE), ApplicationContext3In1 for 3 in 1 mode (CONTACTLESS/DIP/SWIPE).

5 - Use the DeviceFactory to get an object based on the device you have. This object will be used by your app to interact with the reader.

6 - If you are using an audio jack reader, call the device_configurePeripheralAndConnect() method.

  This method will call the Clearent's system to get a audio jack peripheral configuration based on the android device you are using. There are peripheral specifications, such as the baud rate on the audio jack, that can be different per device. If Clearent does not have a configuration to use there is an xml file you can provide as a fallback based on an IDTech format. Supply the file location for this via the ApplicationContext.
  A xml file has been provided in the sdk (xml folder).

7 - Call the registerListen method (when using a bluetooth device only register after you've scanned and found the device). The registerListen handles the connection with the reader. A successful connection calls back to the isReady method. Do not enable usage of reader until the isReady() method of the public listener is called.

8 - To initiate the processing of a card call device_startTransaction.

The handleConfigurationErrors method will alert you of any issues related to configuration. The handleCardProcessingResponse method can be used to monitor issues during the processing of the card. The rest of the communication can be monitored with the lcdDisplay methods of the public listener. One lcdDisplay method gets all general messages and the other is used for user interaction that can be sent back via a callback. This second one is not used for VP3300 readers Clearent supports because they have been configured to not send back customer prompts.

Successful transaction tokens of card reads will be returned via the successfulTransactionToken method of the public listener.

When configuration is being applied, there will be a delay in the initial connection of the reader since the configuration will need to be applied to the reader's flash memory. This is a one time configuration the first time your app starts and connects to the reader. When this succeeds a flag is place in local storage to stop any further configurations.

JavaDocs are supplied in the docs folder.

## Creating a transaction token to represent a manually entered card.


## Demo

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


 ## Disabling emv configuration when using a preconfigured reader

By default Clearent will apply an emv configuration to your device. This configuration was determined by going through a certification process. The configuration can also be applied before the device is shipped to you. When this happens, you should disable the configuration feature. To do this, return true when you implement the ApplicationContext interface method disableAutoConfiguration().

## User experience when emv configuration is being applied.

When the Clearent framework applies the emv configuration to the reader it is using IDTech's framework for communication. This process can take up to a couple of minutes and also has its own unique failures that need to be managed (with possible retry logic). The Clearent framework has some retry capability to account for these failures (example bluetooth connectivity) but only attempts a limited number of times. It's up to the client app to account for this initial user experience. Once the reader has been configured the device serial number is cached so the framework knows not to configure again. If you want to avoid hitting this one time delay during a transaction flow you can advise the merchant to perform an initial connection with the reader, maybe at the time they pull the reader out of the box or some time prior to running transactions.
