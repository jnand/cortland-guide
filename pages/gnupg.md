
GNU Privacy Guard 
==================

> GnuPG is a complete and free implementation of the OpenPGP standard as defined by RFC4880 (also known as PGP). GnuPG allows you to encrypt and sign your data and communications; it features a versatile key management system, along with access modules for all kinds of public key directories.

Using our GPG keys we'll establish: 

* cryptographic identity
* encrypt sensitive files
* authenticate ourselves for SSH and Keybase


Install 
---------

Command line tools.

`❯ brew install gnupg`

GUI suite.

`❯ brew install caskroom/cask/gpg-suite`

Ensure GnuPGv2 is installed.

`❯ gpg --version`

```stdout
gpg (GnuPG) 2.2.1
libgcrypt 1.8.1
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /Users/jnand/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

---

Configure 
-----------

Configure GnuPG to use strong defaults.

Edit `~/.gnupg/gpg.conf` to contain the following:

```~/.gnupg/gpg.conf
auto-key-locate keyserver
keyserver hkps://hkps.pool.sks-keyservers.net
keyserver-options no-honor-keyserver-url

personal-cipher-preferences AES256 AES192 AES CAST5
personal-digest-preferences SHA512 SHA384 SHA256 SHA224

default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 ZLIB BZIP2 ZIP Uncompressed

cert-digest-algo SHA512
s2k-digest-algo SHA512
s2k-cipher-algo AES256

charset utf-8
fixed-list-mode
no-comments
no-emit-version
keyid-format 0xlong
list-options show-uid-validity
verify-options show-uid-validity
with-fingerprint
require-cross-certification
use-agent
```

---

Master key
-----------

We'll follow the off-line master-key pattern for creating out keys, and use subkeys for authenticating, signing, and encrypting data.

`❯ gpg --full-gen-key`

```stdout
gpg (GnuPG) 2.1.11; Copyright (C) 2016 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection?
```

Choose `(1) RSA and RSA (default)` and press `[Return]`

```stdout
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
```

Choose `4096`

```stdout
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
```

Choose `0 = key does not expire`

Fill in the requested info as prompted

```bash
GnuPG needs to construct a user ID to identify your key.

Real name:     _____________
Email address: _____________
Comment:       _____________
You selected this USER-ID:
    "_____ (_______) <____________>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

Enter a password for you **Master key**, don't leave this blank, as the master key is responsible for all sub-keys and represents your cryptographic identity online -- if compromised a password will be your last line of defense if an attacker get hold of your Master key.

?> However, when creating **subkeys** skip password protecting the key since we'll be loading the subkeys onto the Yubikey and setting a pin-code.

```bash
      ┌──────────────────────────────────────────────────────┐
      │ Please enter the passphrase to                       │
      │ protect your new key                                 │
      │                                                      │
      │ Passphrase: ________________________________________ │
      │                                                      │
      │       <OK>                              <Cancel>     │
      └──────────────────────────────────────────────────────┘
```

At this point GPG will generate some random data and create your Master Key and a subkey for encryption `"[E]"`

```stdout
pub   rsa4096 2018-01-01 [SC]
      E2DCD9A862D89D4C1D915469AA78C02FE62BF171
uid                      ----- <-----@---.com>
sub   rsa4096 2018-01-01 [E]
```


### Add picture ###

Adding a picture is optional, though it increase the legitimacy of your signature as a proof of identity.

`❯ gpg --edit-key E2DCD9A862D89D4C1D915469AA78C02FE62BF171`

```bash
gpg (GnuPG) 2.1.15; Copyright (C) 2016 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
sec  rsa4096/AA78C02FE62BF171
     created: 2018-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/E37883053D51C06F
     created: 2018-01-01  expires: never       usage: E   
[ultimate] (1). ----- <-----@---.com>

gpg> 
```

Enter `gpg> addphoto`

```bash
Pick an image to use for your photo ID.  The image must be a JPEG file.
Remember that the image is stored within your public key.  If you use a
very large picture, your key will become very large as well!
Keeping the image close to 240x288 is a good size to use.

Enter JPEG filename for photo ID:
```

Supply the path to a profile photo

```bash
sec  rsa4096/AA78C02FE62BF171
     created: 2018-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/E37883053D51C06F
     created: 2018-01-01  expires: never       usage: E   
[ultimate] (1). ----- <-----@---.com>
[ unknown] (2)  [jpeg image of size 5439]
```

Enter `gpg> save`

---

Encrypt subkey
---------------

If `--full-gen-key` did not already create an encrypt only subkey, `[E]`, then use the commands below as prompted to create one.

`❯ gpg --edit-key AA78C02FE62BF171`

`gpg> addkey`

```bash
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
```

"Your Selection?" **`6`**, **`:RSA (encrypt only)`**

```bash
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 
```

Enter **`4096`**

```stdout
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
```

Choose **`0 = key does not expire`**

?> Skip/blank the password, we'll be using a yubikey pin-code

GPG will generate the key, once done enter `gpg> save`

---

Signing subkey
---------------

Repeat the key generation steps, but this time for a **signing only**, **`[S]`** key.

`❯ gpg --edit-key AA78C02FE62BF171`

At each prompt supply the appropriate configuration:

1. `gpg> addkey`
2. "Please select what kind of key you want" **`:RSA (sign only)`**
3. "What keysize do you want?" **`4096`**
4. "Please specify how long the key should be valid." **`never expires`**
5. Skip/blank password
6. `gpg> save`

---

Authentication subkey
----------------------

Repeat the key generation steps, but this time for an **authentication only**, **`[A]`** key.

`❯ gpg --expert --edit-key AA78C02FE62BF171`

At each prompt supply the appropriate configuration:

1. `gpg> addkey`
2. ... "kind of key you want" **`(8) RSA (set your own capabilities)`**

    ```bash
    Possible actions for a RSA key: Sign Encrypt Authenticate 
    Current allowed actions: Sign Encrypt 

       (S) Toggle the sign capability
       (E) Toggle the encrypt capability
       (A) Toggle the authenticate capability
       (Q) Finished
    ```
3. Toggle off signing, **`S`**
4. Toggle off encrypt, **`E`**
5. Toggle on authentication, **`A`**
5. Finished, **`Q`**
6. "What keysize do you want?" **`4096`**
7. "Please specify how long the key should be valid." **`never expires`**
8. Skip/blank password
9. `gpg> save`

---

Revocation certificate
-----------------------

In the unfortunate event your master key is compromised, youll want to alert the key servers as soon as possible, to do this you'll need a revocation certificate.

`❯ gpg --output master.gpg-revocation-certificate --gen-revoke AA78C02FE62BF171`

```bash
sec  rsa4096/AA78C02FE62BF171 2018-01-01 ----- <-----@---.com>

Create a revocation certificate for this key? (y/N)
```

```bash
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 
```

The default here is good, **`1`**

```bash
Enter an optional description; end it with an empty line:
> 

Reason for revocation: Key has been compromised
revoke master key
Is this okay? (y/N) y
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```

There should now be a file in your current directory named `master.gpg-revocation-certificate`, store this in a safe place, **off-line** preferably.


?> To revoke your master key, invalidating all its subkeys, import your revocation certificate (`gpg --import rev.cert`) and then inform the keyserver (`gpg --send <master-key-id>`).

---

Off-line Master
----------------

Now we'll export and remove the master key for safe keeping off-line.

### Verify keys ###

`❯ gpg --list-secret-keys --with-keygrip`

```bash
~/.gnupg/pubring.kbx
------------------------------
sec   rsa4096 2018-01-01 [SC]
      E2DCD9A862D89D4C1D915469AA78C02FE62BF171
      Keygrip = C87A37B6E94451301A829A0E9ACC3C50CDEB6556
uid           [ultimate] ----- <-----@---.com>
uid           [ultimate] [jpeg image of size 5439]
ssb   rsa4096 2018-01-01 [E]
      Keygrip = F3C60C4C8F09CCB2F204F5E0E35727D0E49AB656
ssb   rsa4096 2018-01-01 [S]
      Keygrip = 3B3FBC948FE59301ED629EFB6AE6D7EE46A871F8
ssb   rsa4096 2018-01-01 [A]
      Keygrip = 82E082EEDE1C516FF9BE500B83D4D89F4A46DCF5
```

### Export public key ###

Create a portable version of your public key.

`❯ gpg --export --armor AA78C02FE62BF171 > public.gpg-key`


### Export secrets ###

Create a portable backup of our private keys.

`❯ gpg --export-secret-keys --armor AA78C02FE62BF171 > private.gpg-key`


### Export subkeys ###

Export only our private subkeys in preparation to evict the private master key.

`❯ gpg --export-secret-subkeys --armor AA78C02FE62BF171 > private.gpg-subkeys`

?> To export selected keys you can use the command **`gpg --export-secret-subkeys E37883053D51C06F! --armor > private.gpg-subkeys`** remember to add the **`!`** after the key-id


### Evict private master key ###

Reset the keyring without the master key.


1. `❯ gpg --delete-secret-key AA78C02FE62BF171`  
2. `❯ gpg --delete-key AA78C02FE62BF171`  
3. `❯ gpg --import public.gpg-key private.gpg-subkeys`  

Ensure the master key is removed

`❯ gpg --keyid-format LONG -k AA78C02FE62BF171`

```bash
~/.gnupg/pubring.kbx
--------------------------------------
sec#  rsa4096/0xAA78C02FE62BF171 2018-01-01 [SC]
      Key fingerprint = E2DC D9A8 62D8 9D4C 1D91  5469 AA78 C02F E62B F171
uid           [ultimate] ----- <-----@---.com>
uid           [ultimate] [jpeg image of size 5439]
ssb>  rsa4096/0xE37883053D51C06F 2018-01-01 [E]
ssb>  rsa4096/0xDCCDCA5907863E7D 2018-01-01 [S]
ssb>  rsa4096/0x7666562DB1D05A07 2018-01-01 [A]
```

You should see **`sec#`** indicating the Master key has no secret present.


!> Ensure any exported keyfiles are securely stored and erased form the working directory. Later we'll cover secure backups and key recovery.


### Importing keys ###

To import previously exported keyfiles follow the procedure:

1. `❯ mkdir ~/gpgtmp`
2. `❯ chmod 0700 ~/gpgtmp`
3. `❯ gpg --homedir ~/gpgtmp --import private.gpg-key public.gpg-key`
4. `❯ gpg --homedir ~/gpgtmp --keyring ~/.gnupg/pubring.kbx --edit-key AA78C02FE62BF171`

This will load the keys into a temporary keyring for creating new subkeys -- this is so we don't accidentally reintroduce the master key into our primary keyring (`~/.gnupg`).


