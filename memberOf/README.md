## LDAP MemberOf Feature
In order to easily and efficiently do queries that enables you to see which users are part of which groups, we need to set up the feature in ldap that allows us to do this.

### Check if MemberOf Already Enabled
Log into your LDAP server and run the following command, do a `grep` on `memberof`, make sure it is not enabled.
```bash
$ olcModuleLoad: {1}memberof
olcModuleLoad: {0}memberof.la
olcModuleLoad: {0}memberof.la
dn: olcOverlay={0}memberof,olcDatabase={1}hdb,cn=config
olcOverlay: memberof
olcRefintAttribute: memberof member manager owner
dn: olcOverlay={2}memberof,olcDatabase={1}hdb,cn=config
olcOverlay: {2}memberof
olcRefintAttribute: memberof member manager owner
olcRefintAttribute: memberof member manager owner
```
In the above case, `MemberOf` feature is alrady enabled. You don't need to enable it.

### Create Group with MemberOf attribute
If `Memberof` overlay is already enabled, but you still see "LDAP/AD server does not support memberOf" message, what you need to do is to create a group with `groupOfUniqueNames` attribute, this will trigger the `MemberOf` overlay when the modification is done.

Let's trigger the overlay by the following example:
```bash
cn: cn=mygroup,ou=groups,dc=lixu,dc=com
objectClass: groupOfUniqueNames
cn: mygroup
description: Test user group
uniqueMember: cn=Tony Xu,cn=Users,dc=lixu,dc=com
```

```bash
$ ldapadd -x -D cn=admin,dc=lixu,dc=com -w password -f add_group.ldif
adding new entry "cn=mygroup,ou=groups,dc=lixu,dc=com"
```

Now the `MemberOf` should be shown.
```bash
ldapsearch -D cn=admin,dc=lixu,dc=com -w password -x -b 'dc=lixu,dc=com' 'cn=Tony*' memberof
# extended LDIF
#
# LDAPv3
# base <dc=lixu,dc=com> with scope subtree
# filter: cn=Tony*
# requesting: memberof
#

# Tony Xu, users, lixu.com
dn: cn=Tony Xu,cn=users,dc=lixu,dc=com
memberOf: cn=mygroup,ou=groups,dc=lixu,dc=com

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

```
### Enabling MemberOf
1. Create "memberof_config.ldif" file:
```bash
dn: cn=module,cn=config
objectClass: olcModuleList
cn: module
olcModulePath: /usr/lib/ldap
olcModuleLoad: memberof.la
```
2. Then load this file into LDAP, this will set up the `MemberOf` module and confiure it:
```bash
$ ldapadd -Q -Y EXTERNAL -H ldapi:/// -f memberof_config.ldif
```

3. Check schema again:
```bash
$ ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config  | grep "memberof"
You should see some outputs
```

4. **You might not need to do step 4 and step 5, I suggest you try step 6 first, if doesn't work, comand back here.** Create the following two files `refint1.ldif` and `refint2.ldif`:
```bash
# refint1.ldif
dn: cn=module{1},cn=config
add: olcmoduleload
olcmoduleload: refint
```
```bash
refint2.ldif
dn: olcOverlay={1}refint,olcDatabase={1}hdb,cn=config
objectClass: olcConfig
objectClass: olcOverlayConfig
objectClass: olcRefintConfig
objectClass: top
olcOverlay: {1}refint
olcRefintAttribute: memberof member manager owner
```
5. To load and configure the refint module:
```bash
$ ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f ./refint1.ldif
$ ldapadd -Q -Y EXTERNAL -H ldapi:/// -f ./refint2.ldif
```
Note: Every group created before this module is enabled has to be deleted and remade in order for these changes to take effect. LDAP assigns a "member" attribute behind the scenes to existing users when creating a group.

6. Let's try to create a group, create a file named `add_group.ldif`:
```bash
cn: cn=mygroup,ou=groups,dc=lixu,dc=com
objectClass: groupofnames
cn: mygroup
description: All users
member: cn=Tony Xu,cn=Users,dc=lixu,dc=com
```
Note: The `Tony Xu` user must be an exist user.

7. Add new group:
```bash
$ ldapadd -x -D cn=admin,dc=lixu,dc=com -W -f add_group.ldif
```

8. Verify the new group:
```bash
$ ldapsearch -D cn=admin,dc=lixu,dc=com -w password -x -b 'dc=lixu,dc=com' 'cn=tony*'
dn: cn=mygroup,ou=groups,dc=lixu,dc=com
objectClass: groupOfNames
cn: mygroup
description: All users
member: cn=Tony Xu,cn=Users,dc=lixu,dc=com
```

### References
ref1: https://technicalnotes.wordpress.com/2014/04/19/openldap-setup-with-memberof-overlay/
ref2: https://www.adimian.com/blog/2014/10/how-to-enable-memberof-using-openldap/
https://github.com/osixia/docker-openldap/issues/110