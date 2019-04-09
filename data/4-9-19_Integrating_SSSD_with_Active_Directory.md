

### {{ site.time | date: '%B %d, %Y' }} - Integrating SSSD with Active Directory

[back](https://alinruisu.github.io/)

**Enviornment**  
Domain Name Warcraft.local  
Active Directory Windows 2016 (2016 forrest level)  
CentOS 7 Linux - Linux linuxclient.warcraft.local 3.10.0-957.10.1.el7.x86_64 #1 SMP Mon Mar 18 15:06:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux  


- Environmental Requirements
Ensure that from a base configuration perspective the linux server is able to resolve the domain in question. use the following for a quick test
```console
[root@linuxclient sssd]#  dig -t SRV _ldap._tcp.ad.warcraft.local

; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> -t SRV _ldap._tcp.ad.warcraft.local
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 26990
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;_ldap._tcp.ad.warcraft.local.  IN      SRV

;; AUTHORITY SECTION:
warcraft.local.         3600    IN      SOA     org.warcraft.local. hostmaster.warcraft.local. 26 900 600 86400 3600

;; Query time: 0 msec
;; SERVER: 192.168.0.100#53(192.168.0.100)
;; WHEN: Tue Apr 09 11:09:54 PDT 2019
;; MSG SIZE  rcvd: 122

[root@linuxclient sssd]# ping warcraft.local
PING warcraft.local (192.168.0.100) 56(84) bytes of data.
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=1 ttl=128 time=0.163 ms
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=2 ttl=128 time=0.294 ms
```

- Base package
```console
yum install sssd realmd oddjob oddjob-mkhomedir adcli samba-common samba-common-tools krb5-workstation openldap-clients policycoreutils-python -y
```
- Reboot the system
```console
Reboot
```
- Realm Discovery
```console
[root@linuxclient sssd]# realm discover warcraft.local
warcraft.local
  type: kerberos
  realm-name: WARCRAFT.LOCAL
  domain-name: warcraft.local
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: oddjob
  required-package: oddjob-mkhomedir
  required-package: sssd
  required-package: adcli
  required-package: samba-common-tools
  login-formats: %U
  login-policy: allow-realm-logins
```

- Realm Join
```console
 realm join --computer-ou="ou=linux,dc=warcraft,dc=local" --user=administrator warcraft.local
```

- Validate Realm Information
```console
[root@linuxclient sssd]# realm list
warcraft.local
  type: kerberos
  realm-name: WARCRAFT.LOCAL
  domain-name: warcraft.local
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: oddjob
  required-package: oddjob-mkhomedir
  required-package: sssd
  required-package: adcli
  required-package: samba-common-tools
  login-formats: %U
  login-policy: allow-realm-logins

[root@linuxclient sssd]# id rsu@warcraft.local
uid=1159801104(rsu) gid=1159800513(domain users) groups=1159800513(domain users),1159801105(linuxadmins)
```

- Modify SSSD Configuration 
```console
[root@linuxclient sssd]# cat sssd.conf

[sssd]
domains = warcraft.local
config_file_version = 2
services = nss, pam

[domain/warcraft.local]
ad_domain = warcraft.local
krb5_realm = WARCRAFT.LOCAL
realmd_tags = manages-system joined-with-samba
cache_credentials = True
id_provider = ad
krb5_store_password_if_offline = True
default_shell = /bin/bash
ldap_id_mapping = True
use_fully_qualified_names = False
fallback_homedir = /home/%u
access_provider = ad
ad_access_filter = (memberOf=cn=linuxadmins,cn=Users,dc=warcraft,dc=local)

```
- Update Sudoers
```console
[root@freeipa sudoers.d]# cat /etc/sudoers.d/sudoers
%linuxadmins ALL=(ALL)       ALL
```

