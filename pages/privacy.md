Privacy and Anonymity
======================

Despite optimizing system configuration for security, information may still be leaking outside trust boundaries through other channels. In particular, search histories and web traffic. This section will attempt to mitigate those threats through `pf` firewall rules, filters, and adaptive measures.


Spotlight search
-----------------

> Disable Spotlight Suggestions in both the Spotlight preferences and Safari's Search preferences to avoid your search queries being sent to Apple.

![spotlight](images/spotlight.png)

![safari](images/safari-spotlight.png)


Homebrew
---------

Disable homebrew's analytics reporting if you'd like.

`❯ brew analytics off`

`❯ brew analytics`

```stdout
Analytics is disabled.
```


Emerging threats
-----------------

Many threats can be blocked at the network level using `pf` rules. For a more robust privacy and malware guard you can install the [macOS-Fortress](https://github.com/essandess/macOS-Fortress) suite, which includes a proxy and automatically updated block lists. To keep the dev's env sane we'll take a simpler approach, adding `pf` rules form the lists maintained by emerging-threats.net.


### Configure `pf` ###

Add the following to `/etc/pf.conf`

```/etc/pf.conf
# emerging-threats anchor point
anchor "emerging-threats"
load anchor "emerging-threats" from "/etc/pf.anchors/emerging-threats"

# compromised-ips anchor point
anchor "compromised-ips"
load anchor "compromised-ips" from "/etc/pf.anchors/compromised-ips"
```

Create the file `/etc/pf.anchors/emerging-threats`

```/etc/pf.anchors/emerging-threats
table <emerging_threats> persist file "/opt/pf/emerging-Block-IPs.txt"
block out log quick to <emerging_threats>
```

Create the file `/etc/pf.anchors/compromised-ips`

```/etc/pf.anchors/compromised-ips
table <emerging_threats> persist file "/opt/pf/compromised-ips.txt"
block out log quick to <emerging_threats>
```

Create an update script to get the latest block lists.

```bash
#!/bin/sh

wc -l /opt/pf/emerging-Block-IPs.txt | logger -t pf -p 5
curl http://rules.emergingthreats.net/fwrules/emerging-Block-IPs.txt -o /tmp/emerging-Block-IPs.txt
cp /tmp/emerging-Block-IPs.txt /opt/pf
chmod 444 /opt/pf/emerging-Block-IPs.txt
wc -l /opt/pf/emerging-Block-IPs.txt | logger -t pf -p 5
rm /tmp/emerging-Block-IPs.txt
pfctl -f /etc/pf.conf
```

Test the new config:

`❯ sudo pfctl -v -n -f /etc/pf.conf`

Load the config and enable the pf firewall:

`❯ sudo pfctl -f -e /etc/pf.conf`

Update launch config to include the `-e` flag, to run on boot.

```plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Disabled</key>
    <false/>
    <key>Label</key>
    <string>com.apple.pfctl</string>
    <key>WorkingDirectory</key>
    <string>/var/run</string>
    <key>Program</key>
    <string>/sbin/pfctl</string>
    <key>ProgramArguments</key>
    <array>
        <string>pfctl</string>
        <string>-e</string>
        <string>-f</string>
        <string>/etc/pf.conf</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

### Confirm settings ###

Test the new rule set

`❯ sudo pfctl -sr`

```stdout
No ALTQ support in kernel
ALTQ related functions disabled
block drop log from any to <emerging_threats>
```

Check the table has been populated:

`❯ sudo pfctl -a 'emerging-threats' -t 'emerging_threats' -Tshow`

Create the `pflog0` interface

`❯ sudo ifconfig pflog0 create`

*Use Wireshark to view the log entries written to pflog0*

