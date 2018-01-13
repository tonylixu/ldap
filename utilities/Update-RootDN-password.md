## LDAP - Updating the RootDN Password
In the event that you have forgotten your LDAP admin password, here is how you update/change it.

1. Log into the LDAP server as root, or have root privilege

2. Get the current RootDN information:
```bash
# RootDN account and the current RootDN password hash are available
# in "cn=config" configuration DIT
# ldapsearch -H ldapi:// -LLL -Q -Y EXTERNAL -b "cn=config" "(olcRootDN=*)" dn olcRootDN olcRootPW | tee ~/new_admin_passwd.ldif
# cat ~/new_admin_passwd.ldif
dn: olcDatabase={0}config,cn=config
olcRootDN: cn=admin,cn=config
olcRootPW: {SSHA}43214321fdasfdsafadsfdasfsafdsaf

dn: olcDatabase={1}hdb,cn=config
olcRootDN: cn=admin,dc=lixu,dc=ca
olcRootPW: {SSHA}ssHm0j9WZBssfdsafadsfdasfsafdsaf
```

3. From step 2, you should get the "HASH"ed RootDN password. If you have multiple values of "dn"s, delete the other "dn", "olcRootDN", "olcRootPW" and only keep the one you need to modify (In our case, dc=lixu,dc=ca). Now let's hasing a new one by using "slappasswd":
```bash
# /usr/sbin/slappasswd -h {SSHA} >> ~/new_admin_passwd.ldif
# cat ~/new_admin_passwd.ldif
dn: olcDatabase={1}hdb,cn=config
olcRootDN: cn=admin,dc=lixu,dc=ca
olcRootPW: {SSHA}ssHm0j9WZBssfdsafadsfdasfsafdsaf

{SSHA}lieJW/YlN5ps6Gn533tJuyY6iRtgSTQw
```
Now you have the newly generated password.

4. Now comment out the "olcRootDN" line matches the account you are trying to modify, and update the file to:
```bash
dn: olcDatabase={1}hdb,cn=config
#olcRootDN: cn=admin,dc=lixu,dc=ca
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}lieJW/YlN5ps6Gn533tJuyY6iRtgSTQw
```

5. Apply the change:
```bash
# ldapmodify -H ldapi:// -Y EXTERNAL -f ~/new_admin_passwd.ldif
```

The administrative password should be updated to the new one.