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

> Setting a firmware password prevents your Mac from starting up from any device other than your startup disk. It may also be set to be required on each boot.

> This feature [can be helpful if your laptop is lost or stolen](https://www.ftc.gov/news-events/blogs/techftc/2015/08/virtues-strong-enduser-device-controls), protects against Direct Memory Access (DMA) attacks which can read your FileVault passwords and inject kernel modules such as [pcileech](https://github.com/ufrisk/pcileech), as the only way to reset the firmware password is through an Apple Store, or by using an [SPI programmer](https://reverse.put.as/2016/06/25/apple-efi-firmware-passwords-and-the-scbo-myth/), such as [Bus Pirate](http://ho.ax/posts/2012/06/unbricking-a-macbook/) or other flash IC programmer.

Check firmware password

`❯ sudo firmwarepasswd -check`

```stdout
Password Enabled: Yes
```

Set firmware password

`❯ sudo firmwarepasswd -setpasswd -setmode command`

```stdout
Setting Firmware Password
Enter password:
```


Standby, Sleep, & Hibernate
---------------------------

To take full advantage of FileVault, we'll need to configure the system power settings to evict our key on standby. Evicting the FileVault key ensures the drive is protected by its encryption scheme, if left unattended.

### Energy settings ###

?> The following configuration allows the system to sleep the display and drives after a specified idle time, prompting the user for a password or touchID on wake to re-enter the session. If the lid is closed the system will hibernate after 60 seconds, evicting the FV-key; when the user wakes the system they'll be prompted for the disk password, then greeted with the user login screen, which may or may not allow touchID depending on how much time has passed in hibernation.

```bash
❯ sudo pmset -a destroyfvkeyonstandby      1
❯ sudo pmset -a standbydelay         0
❯ sudo pmset -a standby              1
❯ sudo pmset -a powernap             0
❯ sudo pmset -a disksleep            10
❯ sudo pmset -a sleep                0
❯ sudo pmset -a autopoweroffdelay    0
❯ sudo pmset -a hibernatemode        3
❯ sudo pmset -a autopoweroff         1
❯ sudo pmset -a displaysleep         2
❯ sudo pmset -a tcpkeepalive         1
❯ sudo pmset -a lidwake              1
```

### Quick Sleep ###

In addition to the lid close event, which triggers FVkey eviction in 60 seconds, we can add other quick actions to put the system in a locked state.

Adding a sleep option to the touch bar is one. From **System Preferences**, **Keyboard**, **Customize Control Strip**, drag the "lock" and "sleep" buttons to the touch bar.

![sleep icon](images/touch-bar.png)


Next, set one of the hotcorners to immediately put the display to sleep, requiring user auth on wake.

Set hot-corner:

`❯ sudo defaults write com.apple.dock wvous-tl-corner -int 10`  
`❯ sudo defaults write com.apple.dock wvous-tl-modifier -int 0`  

Go to, **System Preferences**, **Security & Privacy**, **General**, and set the option to "Require password 'immediately' after sleep or screen saver begins"

![screensaver-pass](images/screensaver-pass.png)



