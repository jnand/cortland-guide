Installing macOS
=================

While installing macOS skip any propmts to configure networking and/or expose 
serivce outside the localhost. Netwrokign and other serivces should be setup 
after configuring the firewall.


Full Disk Encryption
--------------------

After booting up from the installation media prepared in the last step youll be 
prompted to install the operating system. Before proceeding we want to format the 
system drive for whole disk encryption. We'll use the encrypted version of APFS
or HFS+ depending on your distribution.

1. Close the OS install propmt window

2. Click "Utlities" in the menu bar

3. Select "Disk Utility" from the menu
![Disk Utility](https://support.apple.com/library/content/dam/edam/applecare/images/en_US/macos/highsierra/macos-high-sierra-recovery-mode-reinstall.jpg)

4. Select the system drive from the left hand menu, click erase.

5. Select an **Encrypted** variant of the options in the **Format** dropdown.
![Disk formatting](https://support.apple.com/library/content/dam/edam/applecare/images/en_US/macos/highsierra/macos-high-sierra-disk-utility-erase-internal-drive.png)

    The following prompt will ask for a password and hint. Use the **Level III** passphrase you chose earlier when defining the threat model, skipping the hint.

6. Once the system drive has been formatted, exit Disk Utility and proceed with the 
OS Installer.


New mac setup
--------------

Proceed through the new mac setup, skipping all steps -- we want to defer network
and other setup until after the firewall is in place.

!> Try to avoid selecting a wireless network, if unavoidable (due to newer activation
requirements) connect to a secure local/dummy wifi SSID.

![Select wifi](images/select-wifi.png)

When prompted to create your account, use your **Level I** password, without a hint.

!> Uncheck, "Allow my Apple ID to reset this password"

![User account](images/create-new-account.png)


