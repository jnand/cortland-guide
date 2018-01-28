
YubiKey 
========

Now that we have our GPG keys, we can move them onto the yubikey, effectively creating an off-system hardware token. Afterwards, we'll add X.509 certificates for use with secure communications -- _e.g. VPN etc._, then finish integrating the device with the rest of the system.


Loading GPG keys
------------------

?> If you need to update a PIN code via the gpg card manager follow the example in the infobox below. Note: the Yubikey PIV tool is the preferred method.

```info
gpg/card> passwd
gpg: OpenPGP card no. D2760001240102010006069405960000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? q
```

### Add card info ###

`❯ gpg --card-edit`

```stdout
Reader ...........: Yubico Yubikey 4 OTP U2F CCID
Application ID ...: D2760001240102010006069405960000
Version ..........: 2.1
Manufacturer .....: Yubico
Serial number ....: 0*******
Name of cardholder: [not set]
Language prefs ...: [not set]
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: not forced
Key attributes ...: rsa4096 rsa4096 rsa4096
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```

Confirm administration is allowed: `gpg/card> admin`

```stdout
Admin commands are allowed
```

Set the owner name: `gpg/card> name`

```gpg
Cardholder's surname: 
Cardholder's given name: 
```

Set the locale: `gpg/card> lang`, to **`en`**

```gpg
Language preferences: 
```

Set the login account: `gpg/card> login`, to your **`email`**

```gpg
Login data (account name): 
```

Verify the card info is correct `gpg/card> (Press Enter)`

```stdout
Application ID ...: D2760001240102010006055532110000
Version ..........: 2.1
Manufacturer .....: unknown
Serial number ....: 0*******
Name of cardholder: First Last
Language prefs ...: en
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: username@email.com
Private DO 4 .....: [not set]
Signature PIN ....: not forced
Key attributes ...: rsa4096 rsa4096 rsa4096
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```

Exit `gpg/card> quit`


### Move GPG keys ###

Start a gpg session `❯ gpg --edit-key AA78C02FE62BF171`

```stdout
gpg (GnuPG) 2.2.1; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  rsa4096/0xAA78C02FE62BF171
     created: 2018-01-01  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/0xE37883053D51C06F
     created: 2018-01-01  expires: never       usage: E
     card-no: 0000 00000000
ssb  rsa4096/0xDCCDCA5907863E7D
     created: 2018-01-01  expires: never       usage: S
     card-no: 0000 00000000
ssb  rsa4096/0x7666562DB1D05A07
     created: 2018-01-01  expires: never       usage: A
     card-no: 0000 00000000
[ultimate] (1). ----- <-----@---.com>
[ultimate] (2)  [jpeg image of size 5439]
```

#### Encrypt subkey ####

Select the **`[E]`** subkey, `gpg> key 1`, where **`1`** is the ordered number of the key as listed by gpg.

```stdout
sec  rsa4096/0xAA78C02FE62BF171
    created: 2018-01-01  expires: never       usage: SC
    trust: ultimate      validity: ultimate
ssb* rsa4096/0xE37883053D51C06F
    created: 2018-01-01  expires: never       usage: E
ssb  rsa4096/0xDCCDCA5907863E7D
    created: 2018-01-01  expires: never       usage: S
ssb  rsa4096/0x7666562DB1D05A07
    created: 2018-01-01  expires: never       usage: A
[ultimate] (1). ----- <-----@---.com>
[ultimate] (2)  [jpeg image of size 5439]
```

Notice how the key in position 1 is marked by an asterix, **`ssb`**. 

Move the key, `gpg> keytocard`

```gpg
Please select where to store the key:   
   (2) Encryption key   
```

Enter: "Your selection?", **`2`**, "Encryption key"


#### Signing subkey ####

Unselect the **`[E]`** subkey, `gpg> key 1`.

Select the **`[S]`** subkey, `gpg> key 2`

```stdout
sec  rsa4096/0xAA78C02FE62BF171
    created: 2018-01-01  expires: never       usage: SC
    trust: ultimate      validity: ultimate
ssb  rsa4096/0xE37883053D51C06F
    created: 2018-01-01  expires: never       usage: E
ssb* rsa4096/0xDCCDCA5907863E7D
    created: 2018-01-01  expires: never       usage: S
ssb  rsa4096/0x7666562DB1D05A07
    created: 2018-01-01  expires: never       usage: A
[ultimate] (1). ----- <-----@---.com>
[ultimate] (2)  [jpeg image of size 5439]
```

Move the key, `gpg> keytocard`

```gpg
gpg> keytocard
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
```

Enter: "Your selection?", **`1`**, "Signature key"

#### Authentication subkey ####

Unselect the **`[S]`** subkey, `gpg> key 2`.

Select the **`[A]`** subkey, `gpg> key 3`

```stdout
sec  rsa4096/0xAA78C02FE62BF171
    created: 2018-01-01  expires: never       usage: SC
    trust: ultimate      validity: ultimate
ssb  rsa4096/0xE37883053D51C06F
    created: 2018-01-01  expires: never       usage: E
ssb  rsa4096/0xDCCDCA5907863E7D
    created: 2018-01-01  expires: never       usage: S
ssb* rsa4096/0x7666562DB1D05A07
    created: 2018-01-01  expires: never       usage: A
[ultimate] (1). ----- <-----@---.com>
[ultimate] (2)  [jpeg image of size 5439]
```

Move the key, `gpg> keytocard`

```gpg
gpg> keytocard
Please select where to store the key:
   (3) Authentication key
```

Enter: "Your selection?", **`3`**, "Authentication key"

**Save**, `gpg> save`

#### Confirm changes ####

Check, `❯ gpg -K AA78C02FE62BF171`

```stdout
sec#  rsa4096/0xAA78C02FE62BF171 2018-01-01 [SC]
      Key fingerprint = E2DC D9A8 62D8 9D4C 1D91  5469 AA78 C02F E62B F171
uid           [ultimate] ----- <-----@---.com>
uid           [ultimate] [jpeg image of size 5439]
ssb>  rsa4096/0xE37883053D51C06F 2018-01-01 [E]
ssb>  rsa4096/0xDCCDCA5907863E7D 2018-01-01 [S]
ssb>  rsa4096/0x7666562DB1D05A07 2018-01-01 [A]
```

Notice how the secret subkeys (ssb) now have a `>` next to them; they now exist off-line on the yubikey.

#### Require key press ####

We want ensure the user is made aware when any private key is read.

`❯ ykman openpgp touch aut on`
`❯ ykman openpgp touch sig on`
`❯ ykman openpgp touch enc on`

### Test run ###

Run a quick end-to-end sanity check.

`❯ echo "$(uname -a)" | gpg --encrypt --sign --armor --recipient AA78C02FE62BF171 | gpg --decrypt --armor`

![require card](images/card-req.png)

![enter pin](images/card-pinentry.png)

```stdout
gpg: using "E2DCD9A862D89D4C1D915469AA78C02FE62BF171" as default secret key for signing
gpg: encrypted with 4096-bit RSA key, ID 0xE37883053D51C06F, created 2018-01-01
      "----- <-----@---.com>"
Darwin zebra.local 17.4.0 Darwin Kernel Version 17.4.0: Sat Jan 01 00:00:01 PST 2018; root:xnu-4570.41.2~1/RELEASE_X86_64 x86_64
gpg: Signature made Sat Jan 01 00:00:01 2018 PST
gpg:                using RSA key 3B3FBC948FE59301ED629EFB6AE6D7EE46A871F8
gpg: Good signature from "----- <-----@---.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5439]" [ultimate]
Primary key fingerprint: E2DC D9A8 62D8 9D4C 1D91  5469 AA78 C02F E62B F171
```

### Troubleshooting

* [Resetting OpenPGP PIN retries](https://www.yubico.com/support/knowledge-base/categories/articles/reset-applet-yubikey/)


---

X.509 Certificates
-------------------

Open the PIV manager, and start the "Certificates" tool.

`❯ open /Applications/YubiKey\ PIV\ Manager.app`

![piv-cert](images/yubikey-piv-cert.png)


Generate certificates or each of the types, exporting a copy for storage in your keychain: 

* [ ] "Authentication"
* [ ] "Digital Signature"
* [ ] "Key Management"
* [ ] "Card Authentication"

Each should be **`RSA(2048 bits)`** and **`self-signed`**.

![piv-certgen](images/yubikey-piv-certgen.png)





