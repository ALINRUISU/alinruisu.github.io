

### {{ site.time | date: '%B %d, %Y' }} Integrating SSSD with Active Directory

**Enviornment**
Domain Name Warcraft.local
Active Directory Windows 2016 (2016 forrest level)
CentOS 7 Linux - Linux linuxclient.warcraft.local 3.10.0-957.10.1.el7.x86_64 #1 SMP Mon Mar 18 15:06:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux


- Environmental Requirements

- Base package
'''
yum install  -y
'''

- Realm Discovery
'''
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
'''

- Realm Join
'''
 realm join --user=administrator warcraft.local
'''

- Validate Realm Information
'''
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
'''
- Modify SSSD Configuration 

- 

