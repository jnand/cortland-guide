Preparing Installation
=======================


Password policy
----------------

![password](images/password-strength.png)

Before setting up a new system we need to decide on a passphrase. We'll use a three level strategy, where each level builds on the last. These three passphrases will only be used for accessing physical devices, **never** for any cloud services or "online" authentication; those will use unique high entropy codes generated and stored by the [keepass](https://keepass.info/) keychain.

*example:*

?>**Level I:**       "horse battery"  
  **Level II:**      "horse battery staple"  
  **Level III:**     "correct horse battery staple"  
  **Static Token:**  "Cx97pQcbmueZDTxeYGL"  


Level I, will be used for user signin *i.e.* laptop account  
Level II, is used for our keychain master password *i.e.* keepass  
Level III, is used when encrypting entire disks *i.e.* Filevault  
Static Token, is programmed into the yubikey and used as a [pepper](https://en.wikipedia.org/wiki/Pepper_(cryptography)) when needed  

These passphrases won't be used as is, but instead will be paired with a salt and/or pepper as needed. For example, the Level I passphrase will be hashed through a [Yubikey](https://www.yubico.com/) HMAC, without the paired Yubikey the naked passphrase is useless. A Level II login will be hashed with the Yubikey HMAC [pepper](https://goo.gl/ybPwmF) and [salted](https://goo.gl/rthrnQ) with a key file when authenticating our Keepass keychain. And finally any whole disk encryption passphrashes will use the Level III phrase in addition to a user supplied pepper and Yubikey token. 

> **Scenario:** *Login to a powered off laptop*  
FileVaule prompts for disk password. User plugs in yubikey, presses button, yubikey fills static token into field, user enters Level III phrase into same field after yubikey token. Press return, and FileVault successfully authenticates. *Note:* this workflow is necessary since FileVault prompts for a passcode before loading the OS' Yubikey [PAM](https://en.wikipedia.org/wiki/Pluggable_authentication_module) drivers.

This strategy will become more clear throughout the guide and during yubikey integration.


Get the installer
-----------------

The preferred method of downloading the macOS installer is via the Mac App Store.
However, if you're using a previously removed distribution, be sure to pay special
attention to the section on verifying the authenticity of installer.

?> *Ensure you have the most current version and build of your particular os, 
checking the tables on Apple's guide:*  
[How to find the macOS version number on your Mac](https://support.apple.com/en-us/HT201260)


> From the Apple () menu in the corner of your screen, choose About This Mac. 
The version of your operating system appears beneath “macOS” or “OS X” in the 
window that opens. If you need to know the build, click the version. This example 
shows macOS High Sierra version 10.13, build 17A365:

![About this mac](https://support.apple.com/library/content/dam/edam/applecare/images/en_US/macos/macos-high-sierra-about-this-mac-overview-version-build.jpg)


Verify the distribution
-----------------------

Before installing a new operating system ensure the distribution is authentic. 

* Check signatures
* Verify hashes 

?> Double check Apple's Root CAs. They're available on their website at:  
<https://www.apple.com/certificateauthority/>


**At the time of this writing Apple's Root CA fingerprints are:**

SHA1 Fingerprint:

`61 1E 5B 66 2C 59 3A 08 FF 58 D1 4A E2 24 52 D1 98 DF 6C 60`

SHA256 Fingerprint:

`B0 B1 73 0E CB C7 FF 45 05 14 2C 49 F1 29 5E 6E DA 6B CA ED 7E 2C 68 C5 BE 91 B5 A1 10 01 F0 24`


Check signatures
-----------------

Ensure that the distribution is signed and Apple's root CA is within the chain.

`❯ pkgutil -v --check-signature Install\ macOS\ High\ Sierra.app `

```stdout
Package "Install macOS High Sierra.app":
   Status: signed by a certificate trusted by Mac OS X
   Certificate Chain:
    1. Software Signing
       SHA1 fingerprint: 01 3E 27 87 74 8A 74 10 3D 62 D2 CD BF 77 A1 34 55 17 C4 82
       -----------------------------------------------------------------------------
    2. Apple Code Signing Certification Authority
       SHA1 fingerprint: 1D 01 00 78 A6 1F 4F A4 69 4A FF 4D B1 AC 26 6C E1 B4 59 46
       -----------------------------------------------------------------------------
    3. Apple Root CA
       SHA1 fingerprint: 61 1E 5B 66 2C 59 3A 08 FF 58 D1 4A E2 24 52 D1 98 DF 6C 60
```


### View signature details ###

Note anything out of the ordinary

`❯ codesign --display --deep --verbose=2  Install\ macOS\ High\ Sierra.app`

```stdout
Executable=~/Downloads/Incoming/Install macOS High Sierra.app/Contents/MacOS/InstallAssistant_springboard
Identifier=com.apple.InstallAssistant.HighSierra
Format=app bundle with Mach-O thin (x86_64)
CodeDirectory v=20100 size=310 flags=0x2000(library-validation) hashes=4+3 location=embedded
Platform identifier=4
Signature size=4535
Authority=Software Signing
Authority=Apple Code Signing Certification Authority
Authority=Apple Root CA
Info.plist entries=31
TeamIdentifier=not set
Sealed Resources version=2 rules=13 files=177
Nested=PlugIns/LegacyFirmware.bundle
Nested=MacOS/InstallAssistant
Nested=PlugIns/IA.bundle
Nested=PlugIns/DiskManagement.IABundle
Nested=PlugIns/ESDSpringboard.bundle
Nested=PlugIns/IADiskPicker.bundle
Nested=MacOS/InstallAssistant_plain
Nested=Frameworks/OSInstallerSetup.framework
Nested=PlugIns/IACoreStorage.IABundle
Internal requirements count=1 size=88
```


### Verify signature ###

Verify the cryptographic integrity of the signed distribution

`❯ codesign --verify --deep --verbose=2 Install\ macOS\ High\ Sierra.app `

```stdout
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/DiskManagement.IABundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/LegacyFirmware.bundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/ESDSpringboard.bundle
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/DiskManagement.IABundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IADiskPicker.bundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IACoreStorage.IABundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/Frameworks/OSInstallerSetup.framework/Versions/Current/.
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IACoreStorage.IABundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IA.bundle
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/ESDSpringboard.bundle
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IADiskPicker.bundle
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/MacOS/InstallAssistant_plain
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/MacOS/InstallAssistant_plain
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/MacOS/InstallAssistant
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/MacOS/InstallAssistant
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/Frameworks/OSInstallerSetup.framework/Versions/Current/Frameworks/OSInstallerSetupInternal.framework/Versions/Current/.
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/LegacyFirmware.bundle
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/Frameworks/OSInstallerSetup.framework/Versions/Current/Frameworks/OSInstallerSetupInternal.framework/Versions/Current/.
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/Frameworks/OSInstallerSetup.framework/Versions/Current/.
--prepared:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IA.bundle/Contents/MacOS/libBaseIA.dylib
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IA.bundle/Contents/MacOS/libBaseIA.dylib
--validated:~/Downloads/Incoming/Install macOS High Sierra.app/Contents/PlugIns/IA.bundle
Install macOS High Sierra.app: valid on disk
Install macOS High Sierra.app: satisfies its Designated Requirement
```


### Social proof ###

Do the SHA1 and/or SHA256 checksums match those calculated by others? The table 
below is an aggregated of the hashes maintained by:

1. [macOS checksums.](https://github.com/notpeter/apple-installer-checksums/blob/master/readme.md)
2. [macOS SHA256.](https://github.com/drduh/macOS-Security-and-Privacy-Guide/blob/master/InstallESD_Hashes.csv)


#### OSX ####

SHA1

`❯ shasum Install*OS*.app/Contents/SharedSupport/InstallESD.dmg`

SHA256

`❯ shasum -a 256 InstallESD.dmg`

#### Linux, etc: ####

`❯ openssl sha1 InstallESD.dmg`

#### Windows 10 (PowerShell 3+) ####

`❯ Get-FileHash -Algorithm SHA1 InstallESD.dmg`

#### macOS Installer hashes ####

| Version          | Build   | InstallESD SHA1                          | BaseSystem SHA1                          | InstallESD SHA256                        |
|:---------------- |:------- |:---------------------------------------- |:---------------------------------------- |:---------------------------------------- |
| 10.13.2          | 17C88   | 49e336085247331ea6033ebd3598a827caa6596e | 209d6a382026115a30c79f0825aec1b7a4cdb2dd | a016570e65a70e23462efdddd845d3a1a5a7cc39aa770a0052af16e3d5f2ac4f |
| 10.13.1          | 17B48   | d815748c242fbbe35754a8f37aea1cfbc7e919f6 | b38e5f4daa014d324f1a78f91c1f30f6d68289ef |                                          |
| 10.13.0          | 17A405  | e78e5f58fa3eeecf8638067902772ce814d1a89d | d6e2514b5c7c7c35b53fb79e245f61eff5d54b8e |                                          |
| 10.13.0          | 17A365  | 4164f0dde7316ad745426438ef013568fe0313ba | 530839420356e6d77b5ff6da3a3753305da26567 |                                          |
| 10.13gmcandidate | 17A362a | ff1b9cef69573a97dccc7997f1f028c02542decf | 70abc4f7240edb2674008fa68e9c7c792aa71463 |                                          |
| 10.13db9         | 17A360a | 0c26ff40fb1d2ac33eb956f375435504f6c82aab | f67a9ef856e9cadc5e72e91df5d74e10bb485993 |                                          |
| 10.13db8         | 17A358a | 671f49b99e5449a5fb33b9b6e79c2578421bf52d | 22e6f0335f2f98a7b0e479aa79591fcaab1505d0 |                                          |
| 10.13db7         | 17A352a | 23de5e8003692d47ebd09eabdffcd6b7f5ddaf6f | 7b41b675611183ecb62087a1951e65b7a07ec970 |                                          |
| 10.13db6         | 17A344b | 52f33ce14e6d743b2eddc40ba3c73d3d37e6838a | 4342b2238f8ced923de2eae48aaeaa68b146fd9f |                                          |
| 10.13db5         | 17A330h | 457bf24edddc9f873542f5d4cad8adb351a8824a | 57121f3d870b4d38c68111a5f5203bbe88e4b4b0 |                                          |
| 10.13db4         | 17A315i | 68e3149a78c27a0b1b62afad83a532ba45d09680 | f4526c750174c1ecf79dacbda7ffeab5c24ca5f9 |                                          |
| 10.13db3         | 17A306f | b85d4359f1b5d11f5aa1585c13da0fa3c937383b | 5d6e3d13b6022b538cc1a853905169b5c037d908 |                                          |
| 10.13db2u1       | 17A291m | 901523d51d18d26b99e5179d72e0413eca253e84 | b6d33822be36008b6107ea85162b886f9e59eacb |                                          |
| 10.13db2         | 17A291j | 48bb76cabe2ff7be61dcd396087bc8c238b8bbee | 63c47f303883473bfef56007cc63033f8547353c |                                          |
| 10.13db1         | 17A264c | dc9e81f0ba874b23ed62a084ac63702bedebc8cd | 20f05fa198d03046d20b17f8617843c4c71b2b8c |                                          |
| 10.12.6          | 16G29   | b53c36706eef6e0e15c1f76ef51d1b552705fc75 |                                          | d93efaaaa9d029b52ac1985043fabf0e6c8d5015841e7338f96ed9e162538b2c |
| 10.12.5          | 16F73   | 51df126965433187403987c9d74d95c26cba9266 |                                          | dae2d71921a737d41df8f00379b7c04653bd35ed8db0f38313f8d86eb7f39f88 |
| 10.12.4          | 16E195  | 30b9245f7c7608c40bbdf4d4a74f3ab84dbac716 |                                          | 30319aeae18c3277919c59fe678201553f5a11022d6966b67a43422996391181 |
| 10.12.3          | 16D32   | 77d354ec06df0d0acc37c105ae524ba96948142b |                                          | 75a288fe6efc0591f757baf08305270f1b843b54cfb66fe6b257049400a0d6e9 |
| 10.12.2          | 16C68   | 94f9e8f7ae2540dee6fe3465f60fc037e2547d16 |                                          | 6e8ccda1849bb49b1acf75f455019fe327adb47c676dbff018ea811c2456dcce |
| 10.12.2          | 16C67   | 1432e3be6222c434b536721076ed8b16b1c6050e |                                          | 6c2b16f248407a3853a9c4a63efadc94813321708f5eed5c09b73f33e5edd855 |
| 10.12.1          | 16B2659 | f7f147c54627c2a9beb1fa318394e1579b30b167 |                                          | 8efa85e12bcc6c2145cce68b6ecaf9ce23e11f58c1452982b1907fe0f9f76fd1 |
| 10.12.1          | 16B2657 | e559e142a4c9ebaaa740c575d5c3c23c6eb3fb06 |                                          | 8608c0cebf689431ad35d37bcb0035aac266c78f95e7e2a3fd8104d153a24e9b |
| 10.12.0          | 16A323  | 139ef35e4af0da8286b2a3af326cb114d774f606 |                                          | 78a2701bb63a0dcb30862314d1a4598522cfe6a2dd2b096a4e30f256909a4446 |
| 10.12GM          | 16A322  | 3e58d8fcff9f941f28fc7ab47b51c5651c2dfd6d |                                          |                                          |
| 10.12GM          | 16A320  | f38a32b512f70ce72fa054f86991ca057ef37f78 |                                          |                                          |
| 10.12dp2         | 16A239m | 2df533dbb6b5af5d8cc8b352de5c2d4c81ce4cf2 |                                          |                                          |
| 10.12dp1         | 16A201w | 6b1368c4be9f043203efb2e6dd7b73541e016dbf |                                          |                                          |
| 10.11.6          | 15G1010 | c3cdf53048a9a99a1d1355ccef09179a0b6a3dee |                                          |                                          |
| 10.11.6          | 15G31   | 7739e3f62080000da5d28efa689c53976112a262 |                                          | 0b8156957236865e170bc7784bf067ba8b5b231ad8ce45790865e16c9c653615 |
| 10.11.5          | 15F34   | 850781fe8cb5d88c5d1bc23e704e6686ff1fcc2f |                                          | 8be0c4144d79dc0ef275d6bea60db4d23ccf83b22b6c22a99ff35261862b0758 |
| 10.11.4          |         | f6292573395b46e8110be6077fd4827409bc948b |                                          |                                          |
| 10.11.3          |         | e4311d93127d0668372b32e5342f3b455b6bc9bd |                                          |                                          |
| 10.11.2          |         | 2b11b8b618a2e5100507c3c432363081db65c4c8 |                                          |                                          |
| 10.11.1          |         | 306a080c07e293b6765ba950bab213572704acec |                                          |                                          |
| 10.11.0          |         | 5e21097f2e98417ecc12574a7bb46a402594ea4a |                                          |                                          |
| 10.10.5          |         | ef5cc8851b893dbe4bc9a5cf5c648c10450af6bc |                                          |                                          |
| 10.10.4          |         | a8da3a4f4499c68559a2bad4ce232f2443a333ca |                                          |                                          |
| 10.10.3          |         | dc4d4d0a7cd4aea4514025d23a58d05107369fa9 |                                          |                                          |
| 10.10.3          |         | 4b93ff2cef88220a116fbce7c5707c9c57442bd0 |                                          |                                          |
| 10.10.2          |         | 059f2603a91465bcee24c864d446da30df920f85 |                                          |                                          |
| 10.10.1          |         | a673c2c6d967f4da2934b7d6cf3736936970b194 |                                          |                                          |
| 10.10.0          |         | eebf02a20ac27665a966957eec6f5e6fe3228a19 |                                          |                                          |
| 10.9.5           |         | 4a0a01806be8676cb39fb0ab5d049a198d255538 |                                          |                                          |
| 10.9.0           |         | e804dea01e38f8cd28d6c1b1697487e50898dbe7 |                                          |                                          |
| 10.8.5           | 12F45   | bd1997666f9786af584bfa0dc1a64d95ab4b42e6 |                                          |                                          |
| 10.8.5           |         | 7bc54f504aa0b769a2d0b8546393a6e0fc24671f |                                          |                                          |
| 10.8.2           |         | eaf54b1b1a630af85547fed8eabbf6fe159f2b42 |                                          |                                          |
| 10.8.0           |         | e5dd2bf5560033cade7dd7d7da5ceec49f701b0e |                                          |                                          |
| 10.7.5           |         | a044fc01fa75b1f255dbdd6ea4fefa30cef147b0 |                                          |                                          |
| 10.6.0           | 10A432  | f8fa177e4be9a69f87be23b83c30e0c8eedacf5b |                                          |                                          |
| 10.5.0           | 9A581   | 67ab755a3604cd767787fed56150bdb566358f69 |                                          |                                          |
|                  |         |                                          |                                          |                                          |


Create install media
--------------------

Using a clean, formatted usb drive, create a bootable installation volume.

See [Apple's guide](https://support.apple.com/en-us/HT201372) for additional details.

> After downloading the installer, mount the USB flash drive or other volume that 
you're using for the bootable installer. Make sure that it has at least 12GB of 
available storage.

```bash
❯ cd /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources
❯ sudo createinstallmedia --volume /Volumes/MyVolume --applicationpath /Applications/Install.app
```


[Next](pages/install.md?id=installing-macos)