### ldapmodify command
The ldapmodify command is what you use to change an existing ldap entry. The basic usage is a bit different than the ldapadd command. The ldapmodify command can be seen as an almost interactive command and requires these steps:
* Issue the ldapmodify command (with appropriate options).
* Inform ldapmodify what you are modifying.
* Modify your data.
* Escape with CTRL-d.
* ldapmodify will make the changes.