## Import / Export ldap database
Importing data to LDAP server didn't sound obvious for me, so here's how to import / export ldap database. This is usefull if you want to migrate your LDAP database from one server to another.

### Export
Export seems quite easy and well-known:
```bash
# slapcat
This will dump (if run as `root` user) the complete ldap database information on `stdout`.

You can use this command to make backups of your OpenLDAP database, for example:
```bash
# slapcat | bzip2 > mybase.ldif.bz2
```
This solution is not optimum because of the existence of structural attributes in the slapcat dump.

### Structural Attributes
`slapcat` output contains all the LDAP database including some structural information as values associated to attribute `modifyTimestamp, createTimestamp, creatorsName` (and so on ...). These are set automatically by the ldap subsystem when you add/modify info in the ldap database, and thus cannot be set directly. These are similar to meta information stored by filesystems for each files (user, group, permissions, date of creation ...).

### Stripping structural attributes
Applying output of a slapcat output directly to ldapadd will issue this error message (as of openldap 2.4.15):
```bash
# cat mybase.ldif | ldapadd -D cn=admin,dc=example,dc=org -W
Enter LDAP Password:
adding new entry "dc=example,dc=org"
ldap_add: Constraint violation (19)
additional info: structuralObjectClass: no user modification allowed
```
This error message simply states that the attribute structuralObjectClass is not to be modified by any user (even administrator).

These structural information do not carry any information you've stored in the LDAP, so they can be stripped away without any second thought.

Here's how to create a dump stripped out from its structural information:
```bash
slapcat |
    egrep -v  "^(structuralObjectClass|entryUUID|creatorsName|modifiersName|createTimestamp|modifyTimestamp|entryCSN):" |
    bzip2 > mybase.ldif.bz2
```
Note: this egrep filter works because all stripped entries are one-liners.

### Import
Once stripped, the import is quite simple:
```bash
# service slapd stop
# slapadd -l /path/to/ldif/file
# service slapd start
```

### Deleting duplicates
If you have already some entries, you might end up with this error message when trying to import data with the previous command:
```bash
adding new entry "dc=example,dc=org"
ldap_add: Already exists (68)
```
You might want to overwrite all existing data with the imported values. You should probably think about removing all previous data with:
```bash
# ldapdelete -r -D cn=admin,dc=example,dc=org dc=example,dc=org -W
```
This should recursively (thanks to the -r option) delete all entries in `dc=example,dc=org`.
