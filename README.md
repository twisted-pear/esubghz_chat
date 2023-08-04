# Enhanced Sub-GHz Chat

This is a plugin for the Flipper Zero that reimplements the Sub-GHz chat
feature that is available on the CLI. In addition it allows for basic
encryption of messages.

The plugin has been tested on the official firmware (version 0.87.0) and on
Unleashed (version unlshd-059).

Currently the use of an external antenna is not supported.

## Warning

This plugin is in the early stages of development. It will inevitably have
bugs. You have been warned.

## Usage

Once opened the plugin will ask for a frequency to operate on which must be
entered in HZ.

On the next screen the plugin will ask for the method of deriving the key. If
"No encryption" is selected, the encryption is disabled. If "Generate Key" is
selected, a random key is generated. Otherwise, the plugin will ask for the
selected input method. Currently a password and a hex key, as well as reading
the key from another Flipper via NFC are supported.

Finally the a message can be input. After the message is confirmed, the plugin
will switch to the chat view, where sent and received messages are displayed.
To view the chat view without entering a message, enter nothing. To go back to
entering a message press the back button.

In the chat view the keyboard can be locked by pressing and holding the OK
button for a few seconds. To unlock the keyboard again quickly press the back
button three times. By pressing the Right button the key display is opened.
Here the currently used key is displayed in hex. This can be used to input the
same key on another flipper. There is also an option to share the current key
via NFC in the key display.

Pressing the back button when entering the frequency, when selecting the method
for deriving the key or when entering a message will terminate the plugin.

## Interoperability

When encryption is disabled, the plugin can interoperate with the Sub-GHz chat
available on the Flipper's CLI. However, the CLI sends a sequence of escape
characters that are not interpreted by this plugin and will be displayed in the
chat view.

## Encryption

Messages are encrypted using 256 bit AES in GCM mode. Each message gets its own
random IV. On reception the tag generated by GCM is verified and the message
discarded if it doesn't match.

A simple mechanism to prevent replay attacks is implemented. Each time the app
enters an encrypted chat an ID is generated by SHA-256 hashing the Flipper's
name with the current system ticks. Each message is prefixed with this ID and a
counter. A receiving Flipper will check if the counter on a received message is
higher than the last counter received with this ID. If it is not, the message
is discarded and an error is displayed. ID and counter are included in the GCM
tag's computation and are therefore authenticated with the used key.

If a password is used, the key for the encryption is derived from the password
by applying SHA-256 to the password once.

Note that deriving the key with SHA-256 means that the security of your
messages depends entirely on the strength of the password. The plugin does not
use an elaborate key derivation function (KDF) to strengthen the password. A
weak passwords means weak encryption.

Furthermore, the key is the same among all participants. That means that each
user with the key can impersonate every other user.

The same key is used for all messages. This means that no forward-secrecy is
provided. If the key is compromised, all previous messages are compromised as
well.

If you do not understand the implications of the caveats mentioned here, do not
expect to gain any security by using encryption.

## Acknowledgements

The implementations of AES and GCM are taken directly from
https://github.com/mko-x/SharedAES-GCM. They were released to the public domain
by Markus Kosmal.

The app icon was made by [xMasterX](https://github.com/xMasterX). Other icons
and graphics were taken from the Flipper Zero firmware.
