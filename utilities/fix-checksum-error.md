## How to fix “ldif_read_file: checksum error”

If you see the following messages when starting `slapd` process:
```bash
544f7291 ldif_read_file: checksum error on "/etc/openldap/slapd.d/cn=config/olcDatabase={1}monitor.ldif"
544f7291 ldif_read_file: checksum error on "/etc/openldap/slapd.d/cn=config/olcDatabase={2}hdb.ldif"
```

This happens when you didn't use `ldapmodify` to update a ldif file in `/etc/ldap/slapd.d/`. When you change the LDIF files in cn=config manually, their contents and checksums won’t match, which is not fatal, but is annoying.

To fix it, you will need to recalculate their checksums. To do so, follow the steps below:

1. Copy the errant file to an temporary directory (for example olcDatabase={2}hdb.ldif).
 ```bash 
# cp /etc/openldap/slapd.d/cn=config/olcDatabase={2}hdb.ldif /tmp
```

2. Remove the first two lines of that file where it is included the old checksum value
```bash
# cd /tmp
# vim olcDatabase={2}hdb.ldif
remove the file two lines
```

3. Install the Perl Check CRC tool:
```bash
# yum install perl-Archive-Zip
or
# apt-get install libarchive-zip-perl
```

4. Calculate the new checksum
```bash
# crc32 /tmp/*ldif
```

8. Replace the new CRC-32 value into the original file.

9. Restart slapd and you are all set.