![Screenshot](clearent_logo.jpg)

# Release Notes

clearent-idtech-android-2.0.20190122192757.jar (deprecated). Support has been added so you can manually enter a card and have it stored using our 'mobile jwt' solution. (Idtech recommended moving to 105)

clearent-idtech-android-105-1.0.5.jar - upgraded to support the IDTech Universal SDK 105 jar (in libs). Caches a flag when the reader is configured speeding up future connections. Added retry logic during communication issues with the reader to get the configuration to apply more consistently.

clearent-idtech-android-105-1.0.8.jar - fixed an issue when swapping back and forth between readers when the auto configuration has been disabled and the applyClearentConfiguration was called at a later time. Expose the verbose logging method for support.
Exposed firmware upgrade methods. New performance change was added so when the framework identifies when the configuration has been applied it will use a SharedPreference to store this, allowing the app to be shutdown and brought back up with this knowledge still retained. So, first time reader connections will take the hit on the reader configuration and every time thereafter should be faster.

clearent-idtech-android-105-1.0.9.jar - fixed an issue when you swipe a chip card. When this happens we want to force the user to try the chip. An error is returned (terminating the transaction) called 'USE CHIP READER'. You need to initiate a new transaction and attempt a dip card. If this fails you will then have an opportunity to perform a fallback swipe. This jar does not support contactless. You will get an error when attempting to use a contactless card. To get around this with a card that also has a chip, you need to first insert the card, then initiate the transaction.

clearent-idtech-android-105-1.0.21.jar - Fixed an issue with cleaning up resources after performing a rest call (HTTP connection). Fixed an issue with identifying when a reader has been previously configured.  

clearent-idtech-android-109-1.0.23.jar - When the idtech framework tells the clearent framework it has connected, and the clearent framework attempts to get the device serial number, sometimes it fails with a Command Not Allowed error. When this happens the Clearent framework will just proceed with a default device serial number of 9999999999, allowing the integrated app to continue processing. Added remote logging to clearent's backend.

clearent-idtech-android-109-1.0.24.jar - http resources were not being cleaned up correctly when returning from the call to clearent to get a transaction token.

clearent-idtech-android-116-1.0.0.jar - Added methods for registerListen and device_startTransaction allowing you to pass in the number of retries and the length of time to wait before retrying the connectivity to bluetooth reader. The no arg registerListen and the current device_startTransaction have a default retry ability of up to 4 times, waiting 500 milliseconds between attempts, Also upgraded to IDTech's latest solution, Universal_SDK_1.00.116.jar. Updated the demo in the scanupgrade branch (haven't merged to master just yet).

clearent-idtech-android-126-1.0.0-beta.jar

:warning: This release has only passed contactless testing on VP3300 bluetooth encrypted and unencrypted readers using idtech firmware version .151. We are still testing the audio jack readers to confirm IDTech's firmware version works with this release for contactless.
If you plan on upgrading to use with the audio jack readers you should keep contactless disabled.

* handles encrypted bluetooth and audio jack readers. This release will work with encrypted audio jack readers on firmware version .064
for DIP and SWIPES only (no contactless).

* Added emv contactless reader support. MSD contactless was not a part of certification and is not supported. EMV Contactless Cards (with the network symbol) and cards in an Apple Wallet should work.

* improved bad swipe handling. The "USE CHIP READER" has been disabled in favor of a retry loop.
Now, if you do a bad swipe the initial transaction is still terminated (you will still get the callback message "TERMINATE") ,
but the framework will start the transaction up again and notify with "FAILED TO READ CARD. TRY INSERT/SWIPE". If you swipe a card
that has a chip you will get the message "CARD HAS CHIP. TRY INSERT". This transaction remains until it times out or you cancel the transaction.

* If a tap is attempted and the reader has trouble reading the card because it was not in the NFC field long enough, the framework will handle this error and start up a new transaction. This retry loop will send back a message of 'RETRY TAP' until either it's successful or the transaction is cancelled or times out.

* Some times when the card and the reader interact an error will happen that does not allow contactless. When this happens the framework will cancel the transaction and start a new contact/swipe transaction. Contactless will be disabled and a message will be retuned to "INSERT/SWIPE".

* emv/contact configuration is now disabled by default. You now have the option of getting readers preconfigured. If you are receiving pre configured readers you should disable contact auto configuration.

* Added contactless configuration.

* Added methods allowing you to control the contactless configuration.

```java

   VP3300

   //If you have already configured contactless and need to clear the cache that stops the framework from performing configuration every time the reader connects.
   void setReaderContactlessConfiguredSharedPreference(boolean flag);

   //By default the framework will not auto configure contactless.
   void setContactlessConfiguration(boolean enable);

   //Independent of the contactless configuration is a flag to enable the use of contactless. By default the framework will throw the same error it did previously.
   void setContactless(boolean enabled);

   //If you want to check if a connected reader has contactless configuration (see Javadocs for more details).
   int isContactlessConfigured();
```

* rerouted "Card inserted" message that was being sent to the timeout callback (idtech bug) to the lcdDisplay method.

* Some readers have been identified as misconfigured. Because of this we've done two things to the framework.
First, if the configuration process fails you will not be able to run transactions.
If you get the error stating the reader has not been configured the configuration process should be performed again,
Second, we've added some logic to attempt to fix a misconfigured reader. If auto (emv/contact) configuration has been enabled ,
the framework will check the reader to see if configuration has been applied (terminal major configuration should be 5C, terminal type 21, etc).
If invalid the cache is cleared which should force configuration. The message "INVALID READER CONFIGURATION. APPLYING ONE TIME UPDATE. PLEASE WAIT (DO NOT DISCONNECT OR CANCEL TRANSACTION" will be sent back when this happens.

* improved retrying of the initial bluetooth connection. A new message "PRESS BUTTON ON BLUETOOTH READER" is sent back when the framework is trying to connect to the bluetooth reader (only sent back after initially trying a couple of times).

* improved handling of initial communication with reader to retrieve device serial number, firmware version, and kernel version.

* fixed an issue retrieving mastercard card data from idtech card results.

* improved remote logging

* disabled multi language support that was resulting in a language prompt message being sent back (ex 1. English)

* Uses IDTech framework version - Universal_SDK_1.00.126.jar

* added isReaderPreconfigured method so you can check if the reader was preconfigured and decide whether or not to enable our configuration feature.

* When a card is read and the reader rejects the chip the framework will now send back a message instructing the user to watch for the green led light because the transaction is being switched over to a swipe. The framework will start up a swipe transaction.

* fixed an issue with the device serial number not being sent during remote logging on initial connections.

* Enhanced remote logging.

* Fix an issue with mapping card data on unencrypted readers.

* Added some small delays and retry logic to the auto configuration process.

* Added some healing logic related to early communication issues with the reader during auto configuration that resulted in duplicate jwt requests being sent to Clearent. If the framework sees you have set auto configuration on it will
check the terminal major configuration (tmc) to make sure it is set to 5C, not 2C (default from manufacturer). It will also check a custom idtech tag that tells the reader what emv entry mode to use as default for inserts.
idtech defaults to 07, which is contactless. If this has not been set to 05 and the terminal major configuration is not 5 the framework will invalidate the cache prior to determining whether or not it should do auto configuration (which forces auto configuration to happen). Because of this issue we have changed auto configuration from being 'lenient' to 'strict', meaning if any part of the configuration fails the reader will be considered not configured and the framework will not allow transactions to run.

* Auto configuration has been tested using the audio jack and bluetooth versions of the VP3300 reader. If you find the configuration failing it's recommended you try plugging in the reader to a power source.

* You can now contribute to Clearent's remote logging which might help us when we field support calls.

```java
    void addRemoteLogRequest(String clientSoftwareVersion, String message)
```

### Known Issues & different behavior ###

* The idtech framework is calling back with a new message when you start a transaction using the device_startTransaction method. "PLEASE SWIPE, TAP,OR INSERT"

* Some times when you attempt contactless the reader sends back a generic response. When this happens the framework doesn't know whether to keep retrying the tap or fallback to a contact/swipe.
A 'Card read error' is returned. A new transaction will need to be tried.

* The idtech framework will sometimes not send back a message we can display to you instructing you there was an error during contactless/tap. Instead it relies on audio beeps. Usually if you hear 3 beeps or two beeps instead of the long beep it indicates an issue. See idtech docs for more details.
