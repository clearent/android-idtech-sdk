![Screenshot](clearent_logo.jpg)

# Release Notes

clearent-idtech-android-137-2.0.0-beta.jar - Use with idtech jar Universal_SDK_1.00.137_Test3.jar (release pending). In this
release we've added a new method that allows you to start a transaction and pass to it how you would like to connect to the idtech reader. This means you do not have to handle bluetooth connectivity. It will also allow you to toggle between reader interface modes ("3 in 1" mode, which will enable contactless, and "2 in 1 " mode which will only enable insert/icc and swipe).

More details can be found in the Clearent_Android_IDTech_Framework_Version2.doc document.

```java
ClearentResponse startTransaction(PaymentRequest paymentRequest, Connection connection);

```
To connect to bluetooth, create a Bluetooth object. Here's an example from our demo

```java
private Bluetooth getBluetooth(ReaderInterfaceMode readerInterfaceMode) {

    Bluetooth bluetooth;
    String bluetoothFriendlyName = settingsViewModel.getBluetoothFriendlyName().getValue();
    String last5 = settingsViewModel.getLast5OfBluetoothReader().getValue();

    if (bluetoothFriendlyName != null && !"".equals(bluetoothFriendlyName)) {
        bluetooth = new Bluetooth(readerInterfaceMode, BluetoothSearchType.FRIENDLY_NAME, bluetoothFriendlyName);
    } else if (last5 != null && !"".equals(last5)) {
        bluetooth = new Bluetooth(readerInterfaceMode, BluetoothSearchType.LAST_5_OF_DEVICE_SERIAL_NUMBER, last5);
    } else {
        bluetooth = new Bluetooth(readerInterfaceMode, BluetoothSearchType.CONNECT_TO_FIRST_FOUND);
    }
    return bluetooth;

}

### Known Issues & different behavior ###

* Some times when you attempt contactless the reader sends back a generic response. When this happens the framework doesn't know whether to keep retrying the tap or fallback to a contact/swipe.
A 'Card read error' is returned. A new transaction will need to be tried.
