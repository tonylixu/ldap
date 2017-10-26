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

```bash
$ ldapsearch -x -W -D 'cn=Manager,dc=example,dc=com' -b "" -s base \
      '(objectclass=*)' +

Enter LDAP Password:
# extended LDIF
#
# LDAPv3
# base <> with scope baseObject
# filter: (objectclass=*)
# requesting: +
#
#
dn:
structuralObjectClass: OpenLDAProotDSE
configContext: cn=config
namingContexts: dc=example,dc=com
supportedControl: 1.3.6.1.4.1.4203.1.9.1.1
supportedControl: 2.16.840.1.113730.3.4.18
supportedControl: 2.16.840.1.113730.3.4.2
supportedControl: 1.3.6.1.4.1.4203.1.10.1
supportedControl: 1.2.840.113556.1.4.319
supportedControl: 1.2.826.0.1.334810.2.3
supportedControl: 1.2.826.0.1.3344810.2.3
supportedControl: 1.3.6.1.1.13.2
supportedControl: 1.3.6.1.1.13.1
supportedControl: 1.3.6.1.1.12
supportedExtension: 1.3.6.1.4.1.4203.1.11.1
supportedExtension: 1.3.6.1.4.1.4203.1.11.3
supportedExtension: 1.3.6.1.4.1.4203.1.11.3
supportedFeatures: 1.3.6.1.1.14
supportedFeatures: 1.3.6.1.4.1.4203.1.5.1
supportedFeatures: 1.3.6.1.4.1.4203.1.5.2
supportedFeatures: 1.3.6.1.4.1.4203.1.5.3
supportedFeatures: 1.3.6.1.4.1.4203.1.5.4
supportedFeatures: 1.3.6.1.4.1.4203.1.5.5
supportedLDAPVersion: 3
supportedSASLMechanisms: NTLM
supportedSASLMechanisms: DIGEST-MD5
supportedSASLMechanisms: CRAM-MD5
entryDN:
subschemaSubentry: cn=Subschema
# search result
search: 2
result: 0 Success
# numResponses: 2
# numEntries: 1

+: Search for all operational results
```
