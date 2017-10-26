## ldapsearch commands
```bash
$ ldapsearch -x -W -D 'cn=Manager,dc=example,dc=com' -b "" -s base
# extended LDIF
#
# LDAPv3
# base <> with scope baseObject
# filter: (objectclass=*)
# requesting: ALL
#
#
dn:
objectClass: top
objectClass: OpenLDAProotDSE
# search result
search: 2
result: 0 Success
# numResponses: 2
# numEntries: 1

-x: This tells the server to use simple authentication
     (instead of the more complicated, but more secure, SASL authentication).
-W: This tells the client to prompt us for an interactive password. The client will give the following prompt:
Enter LDAP Password:
-D 'cn=Manager,dc=example,dc=com': This speci es the DN that we want to use to
 connect to the directory. In this case, we are using the directory manager account.
-b "": This sets the base DN for the search. In the ldap.conf file we set the default base to be dc=example,dc=com. 
But to get the root DSE, which is not under dc=example,dc=com, we need to specify an empty search base.
-s base: This indicates that we want to search for just one (base) entry — the 
entry with the DN speci ed in the -b parameter (the empty DN of the root DSE).
```
