
OpenSSH
=======

macOS's remote login functionality is built on OpenSSH, and defaults permission to any user in the _admin_ group. Most day-to-day use will only need the client, and the server should remain disabled.

SSH
----

To mitigate propagation of an account compromise enable obfuscation of your known_hosts list.

Ensure *ssh_config* contains the directives below, `❯ sudo vim /etc/ssh/ssh_config`

```/etc/ssh/ssh_config
Host *
  HashKnownHosts yes
```

SSHD
------
Despite previously disabling all services under **System Preferences**, **Sharing**, we still want to ensure `sshd` is secured if later activated.

![ssh](images/ssh.png)


### Configuration ###

Edit the service config file to match the settings below, `❯ sudo vi /etc/ssh/sshd_config`

```/etc/ssh/sshd_config
# To disable tunneled clear text passwords both PasswordAuthentication and
# ChallengeResponseAuthentication must be set to "no".
PasswordAuthentication no
PermitEmptyPasswords no

# Change to no to disable s/key passwords
ChallengeResponseAuthentication no
```


<div class='center'>
    <a href="#/pages/privacy" style="text-decoration: none;">
        <strong><span>Next</span></strong>
        <strong><span style="vertical-align: middle;">&#8679;</span></strong>       
        <small>Privacy & Anonymity</small>
    </a>
    <span>...</span>
</div>