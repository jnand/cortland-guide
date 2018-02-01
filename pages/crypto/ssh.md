
Open SSH/SSL
=============

Most day-to-day use will only need a `ssh` client and the server should remain disabled; however, the default `sshd` permissions allow any user in the _admin_ group to connect, and should be hardened a bit should the need to enable it arise.

SSH
----

To mitigate propagation of an account compromise enable obfuscation of your known_hosts list.

Ensure *ssh_config* contains the directives below, `❯ sudo vim /etc/ssh/ssh_config`

```/etc/ssh/ssh_config
Host *
  HashKnownHosts yes
```

---

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

These settings disable password authentication and require a trusted key-pair be used instead.

---

SSL
----

Check the implementation and protocol version of openssl are current, 

`❯ openssl version`

```stdout
LibreSSL 2.2.7
```

`❯ openssl s_client -connect github.com:443 2>&1 | grep -A2 SSL-Session`

```stdout
OpenSSL 1.0.2j  26 Sep 2016
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES128-GCM-SHA256
```

If needed, install a more current version, `❯ brew install openssl`


<div class='center'>
    <br>
    <a href="#/pages/privacy" style="text-decoration: none;">
        <span>Next</span>
        <span><i class="fas fa-arrow-up" aria-hidden="true" style="vertical-align: middle;"></i></span>
        <p><small>Privacy & Anonymity</small>...</p>
    </a>
</div>