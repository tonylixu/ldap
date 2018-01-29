## LDAP MemberOf Feature
In order to easily and efficiently do queries that enables you to see which users are part of which groups, we need to set up the feature in ldap that allows us to do this.

### Check if MemberOf Already Enabled
Log into your LDAP server and run the following command, do a `grep` on `memberof`, make sure it is not enabled.
```bash
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config  | grep "memberof"
olcModuleLoad: {1}memberof
dn: olcOverlay={0}memberof,olcDatabase={1}hdb,cn=config
olcOverlay: {0}memberof
```
In the above case, `MemberOf` feature is alrady enabled. You don't need to enable it.

### Enabling MemberOf