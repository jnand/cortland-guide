
Review
=======

After working through the major section the threat model should now be acceptably derisked -- _there are always unknown side-channels to be reconsidered_.


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

Remember to check popular cloud services you may be using, and follow best practices during use. 

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
    _if not will cryptomator work_
- [ ] Audit logging  
    _revision/activity history_
- [ ] Device remediation / Session management  
    _can you revoke compromised access_
