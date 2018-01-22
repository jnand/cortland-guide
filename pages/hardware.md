Hardware
=========

The unibody design of the macbook pro makes tamper proofing much easier than more user serviceable cases. With a few creative tricks and some config changes, the system can easily be audited for tampering if suspected.

Tamper evident 
--------------

The easiest method for tamper proofing a macbook pro is to paint over one or two of the case screws on the underside of the laptop using a coarse flake glitter nail polish. After the polish is dried take a close up squared photograph of the area, preferably with a ruler in view for size refrence. Then use you PGP key to sign the images and store them on dropbox or elsewhere for later auditing.

![tamper](images/tamper-polish.jpg)

To audit the seal, simply take a new photo with a ruler in frame and open the before and after images in a photo editing software and spot check between features. You can also orient and line up the images in layers to perform a ["blink test"](https://en.wikipedia.org/wiki/Blink_comparator).

?> For added security you can mark the first coat with UV ink and apply a second clear-coat to seal it. Then use a uv-penlight for quick spot-checks on the go.

![uv-tamper](images/uv-tamper.jpg)


Firmware
---------

At this point any firmware updates that were bundled with OS updates should have been applied. Lets double check and verify everything is ok, as [some EFI updates fail to be applied](https://duo.com/blog/the-apple-of-your-efi-mac-firmware-security-research), then lock down the firmware.

!> Password protecting the firmware is a critical mitigation at this point, so future packages can't easily modify the system without authentication.


### Check EFI ###

`❯ cd /usr/libexec/firmwarecheckers/`  
`❯ eficheck/eficheck --integrity-check`

```stdout
EFI Version: MBP143.88Z.0167.B00.1708080129
Primary allowlist version match found. No changes detected in primary hashes.
```

### Check ethernet ###

`❯ sudo ethcheck/ethcheck --integrity-check`

```stdout
Could not find any BCM5701Enet devices or device driver is not supported
```

### Payload validation ###

Further EFI checks can be conducted using the [`efivalidate`](https://github.com/dropbox/efivalidate) tool to confirm firmware authorship

### Firmware password ###

