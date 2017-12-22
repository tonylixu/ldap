### ldapmodify command
The ldapmodify command is what you use to change an existing ldap entry. The basic usage is a bit different than the ldapadd command. The ldapmodify command can be seen as an almost interactive command and requires these steps:
* Issue the ldapmodify command (with appropriate options).
* Inform ldapmodify what you are modifying.
* Modify your data.
* Escape with CTRL-d.
* ldapmodify will make the changes.

### Example
To do this start out by issuing the command:
```bash
ldapmodify -h localhost -x -W -D "cn=admin,dc=tony,dc=com"
```
Now you need to indicate to LDAP what entry it is you are modifing by entering:
```bash
dn: uid=txu,ou=People,dc=tony,dc=com
```
Now hit the Enter key to move to the next step, wich is indicating to ldapmodify what you plan to do:
```bash
changetype: modify
```
After you type the above hit the Enter key. Now you inform LDAP what you plan to modify in this entry by entering:
```bash
replace: department
```

As you would expect, hit Enter when you've typed the above. Finally, you enter the text you want to replace the gecos entry with by typing:
```bash
department: Software engineering
```
That is the last string of text to type for the modification. Now hit the Enter key and then the CTRL-d combination to escape the LDAP prompt. Your LDAP entry has officially been modified.
