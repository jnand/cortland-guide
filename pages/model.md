Defining a threat model
=======================


We want to mitigate risks in day-to-day consulting work; possibly including some 
that are adversarial in nature. The level of sensitivity of client information may range from derisked trust positions (*i.e.* confidential IP, HIPAA, etc.), to low-level SAPs.


### Trust boundaries ###

**Hardware.** The laptop will be used remotely from various locations. Public exposure
is likely during travel and off-site work. Therefore, the endpoint should be secured
in case of third party access and/or theft.

**Networking.** All access to and from external resources will be controlled at both the
packet level and application layer. Communication between project containers is tightly
controlled. Moreover, external access by containers will be virtually "NATed" to the host.

**Virtualization.** Client projects will exist on the same system-drive and will need 
to be run in isolation from one another during development. Containers should be encrypted
and run exclusively. No client projects will be readable by the host system while at rest.

**Internal.** Trust boundaries will exist between all projects; common services and 
architecture will be duplicated as needed. On hand-off the containers will be passed 
to the client for review and are subject to their sysadmin's security policies.


### Actors ###

**Primary.** The only actor here is the system user. External actors are beyond the scope of this document. Projects deployed for client testing, review, or production will not 
happen on this host.

**Client.** End users will interact with their respective projects on a third party host, 
secured under a separate model. 

**Recovery.** Backup services will store endpoint encrypted data at rest, transported to 
their facility via VPN/TLS+SSL or other secure channel.


### Information flows ###

**Authentication.** The firmware, system drive encryption, OS logon, OS services, 
and keychain all require a valid user to access protected data. User identity is 
maintained through a set of off-system PGP keys.

**Transit.** Data in transit will include web traffic, chat communication,
cloud services, remote VPN, and directory lookups.

**Rest.** Data not in use falls into categories of: host system files/documents, client files, software projects, backup and recovery. Whole drive images will **not** be kept.

**Processing.** Runtime data is confined to the host system, where any external access is 
controlled through the virtualization layer's NAT and host OS firewall.


### Threat agents ###

*Authentication.* Susceptible to second party login, thieves, adversarial malware, eavesdroppers.

*Transit.* Eavesdroppers, rouge access points, repudiated directory services.

*Rest.* Theft and decryption, damage and destruction, tampering.

*Processing.* Data corruption, compromised software dependencies, injected 
malware payloads.


### Attack vectors ###

*Authentication.* Over-the-shoulder skimming, weak passwords, password reuse, key 
theft, single-user-mode side channel attacks. 

*Transit.* Man-in-the-middle attacks, Identity spoofing, DNS cache poisoning,
Wifi Key re-installation attacks.

*Rest.* Decryption, data-corruption, key-theft.

*Processing.* Code injection, privilege escalation, persistent threat installation,
end-user data leaks.


### Assessment ###


#### *Authentication* ####

The risk involved with lost, stolen, or otherwise compromised credentials is high and requires critical priority as a threat would be able to impersonate an authenticated user and act with their privileges throughout the system and within any authorized external resources. Risks include: deployment systems, production servers, backup systems, cloud services and documents. Compromised authentication creates further risks of persistent threats being installed on endpoints or servers, leaking privileged information outside trust boundaries.

#### *Transit* ####

Data in transit is lower risk as many common attack vectors are mitigated by using HTTPS and/or VPN tunneling. Exposed services can be secured via an encrypted channel *e.g* DNS over HTTPS, and identities verified via DNSsec. If data is end-to-end encrypted at the protocol layer, network level re-installation attacks no longer pose immediate threats.

#### *Rest* ####

Data at rest is vulnerable to tampering, corruption, and/or theft if proper measures aren't taken to secure its integrity. Any mobile system, laptops, or tablets, should fail secure on power-loss under the assumption that backups and recovery are functioning correctly. At no time should any system disks or external drives be unencrypted. 

Cloud storage of documents and sensitive files may also pose a risk if we only rely on the providers encryption. Data should be kept in a user controlled encrypted wrappers in the event a service is connected to another account, crossing boundaries. *e.g* Connecting folders between user accounts on dropbox.

#### *Processing* ####

"Clean room" development is not necessary, unless client specified. The use of third party libraries does present an internal exposure to escalation and hijacking. However, the use of virtualized containers and sand-boxing should reduce risk to within acceptable levels. Thorough code auditing and traffic monitoring should surface any potential data leakage.


### Derisk ###

Fully derisking this model involves:

1. Tight hardware controls  
    1.1. Tamper evident seals  
    1.2. Firmware passwords  
    1.3. Hardware based MFA tokens  
    1.4. Automated system audits  

2. Regular system updates  
    2.1. OS and software patches  
    2.2. Routine codesign validation  
    2.3. Monitoring security announcements  

3. Strong password policy  
    3.1. Tiered passphrase strategy  
    3.2. PGP cryptographic identity  
    3.3. Hardware token  
    3.4. Multi-factor authentication  
    3.5. Secure keychain  
    3.6. Independent high entropy passwords  

4. Encryption  
    4.1. Full disk encryption  
    4.2. Encrypted project containers  
    4.3. Document wrappers  

5. Communication  
    5.1. Secure VPN  
    5.2. DNScrypt & DNSsec  
    5.3. Keybase secure chat  

6. Sandboxing  
    6.1. Virtual machines  
    6.2. Containers  

7. Boundary control & Logging  
    7.1. System Integrity Protection +  
    7.2. Threat monitoring  
    7.3. Network Firewall  

8. Backup & Recovery  
    8.1. Encrypted cloud backups  
    8.2. Versioned backups  
    8.3. Automated new system provisioning  
    8.4. Key recovery protocol  


[Next](pages/prepare.md?id=preparing-installation)
