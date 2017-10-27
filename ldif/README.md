## Model Directory Tree
The first thing to do is to decide on a directory structure. One of the very
popular ways of defining the roots of an organization directory tree:
* Use the organization's domain name. For example, if the company has
registered "tomosolutions.com" domain name, then the root DN would be
composed of of three domain component (dc) attributes:
  * dc=tomosolutions,dc=com

Note: This domain based component form is preferred because it is more closely
related to the way much information is referenced on the Internet.

## Define the Base DN Record
After you chose a base DN style, now it's time to build a directory. Keep
in mind that LDIF files are read sequentially, record by record. So the base DN
must come first, since all other records will refer to it in their DNs.

Our base DN looks like this:
```bash
dn: dc=tomosolutions,dc=com
description: tomosolutions.com, your trusted solution consulting company.
dc: tomosolutions
o: tomosolutions.com
objectClass: top
objectClass: dcObject
objectClass: organization
```
* objectClass:
  Neither of "top" or "dcObject" are structural object classes, "top" is
abstract, and "dcObject" is auxiliary, and in LDAP, every record in the
directory must have one object class that is considered the structural object
class for that record. "organization" is a structural object class.
  * top: this object class is the root of the hierarchy of object classes, and
 all records within the directory are in the top object class.
  * dcObject: simply describes domain components, each record that uses the
dcObject can describe only one domain component, because this "dcObject"
just describe a single domain.
  * organization: The organization object class describes an organization, as the name
suggests. It requires one  eld, o (or its synonym, organizationName), which
is used to specify the (legal) name of the organization. Additionally the
organization object class allows twenty-one optional  elds that provide more
detailed information about the organization, such as postalAddress,
telephoneNumber, and location.
