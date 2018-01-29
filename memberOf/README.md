## LDAP MemberOf Feature
In order to easily and efficiently do queries that enables you to see which users are part of which groups, we need to set up the feature in ldap that allows us to do this.

### Check if MemberOf Already Enabled
Log into your LDAP server and run the following command, do a `grep` on `memberof`, make sure it is not enabled.
```bash
$ ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config  | grep "memberof"
olcModuleLoad: {1}memberof
dn: olcOverlay={0}memberof,olcDatabase={1}hdb,cn=config
olcOverlay: {0}memberof
```
In the above case, `MemberOf` feature is alrady enabled. You don't need to enable it.

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

4. Create the following two files `refint1.ldif` and `refint2.ldif`:
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