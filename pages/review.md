
Review
=======

After working through each of the major sections, the threat model should now be _acceptably derisked_ -- _there are always unknown side-channels to consider_.


Closing details
----------------

These are helpful resources and things to consider.

#### Bittorrent 

Transmission continues to be one of the best clients, `❯ brew install transmission`  
Use [iBlocklist](https://www.iblocklist.com/) to ban potentially hostile peers.

#### File extensions

Show all filename extensions (so that "Evil.jpg.app" cannot masquerade easily),  
`❯ sudo defaults write NSGlobalDomain AppleShowAllExtensions -bool true`

#### Secure Input

> iTerm2 > Secure Keyboard Entry
When this is enabled, the operating system will prevent other programs running on your computer from being able to see what you are typing. If you're concerned that untrusted programs might try to steal your passwords, you can turn this on, but it may disable global hotkeys in other programs.

---

Cloud services
---------------

Remember to check any cloud services you may have, and follow best practices during use. 

!> Most importantly, consider what data is being placed with a given provider, the security measures in place to mitigate risks, and if its compromise is within acceptable risk.  
**Scenario:** is a breached google doc with client design specifications acceptable?  
_Is it designs for a marketing webpage? A business intelligence application?_

### Popular services ###

- Dropbox
- Google
- Backblaze
- Keybase
- VPN Providers
- Github
- Bitbucket
- AWS
- Box
- OneDrive
- ...


### Points to consider ###

- [ ] Two-factor authentication  
    _authenticator app or yubikey_
- [ ] Authorization  
    _can asset permissions be controlled_
- [ ] Password strength  
    _high-entropy and maximum length_
- [ ] Recovery policy  
    _do they use common challenge questions, "mothers maiden name"_
- [ ] Zero knowledge encryption  
    _if not, will cryptomator suffice_
- [ ] Audit logging  
    _revision/activity history_
- [ ] Device remediation / Session management  
    _can you revoke compromised access_


### Compromise protocol ###

Based on the features and logs offered by the services in use its good develop a "burn protocol" for revoking permissions and [air-gapping](https://en.wikipedia.org/wiki/Air_gap_(networking)) threats.


#### SSH ####

In the case of SSH keys its useful to keep a manifest of hosts using a particular public key, to be revoked in the event of a private key compromise (for those not derived from our yubikey strategy). There is also [Krypton](https://krypt.co/) for managing multiple SSH key pairs, keeping private keys on a mobile device.


#### Location tracking ####

If traveling, its good to be aware of remote lock and wipe features.

- Apple's [iCloud](https://support.apple.com/kb/ph2701) erase
- Backblaze's [locater](https://www.backblaze.com/lost_computer.html)

