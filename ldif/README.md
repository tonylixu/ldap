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

## Define Organizational Units
There are many ways of structuring LDAP OUs, two of them are prominent.
### Directory as Organizational Chart
The LDAP directory should be structured to represent the organizational chart.
For example, if the orgnization has three main units—Accounting, Human
Resources (HR), and Information Technology (IT)—then you should have three OUs.

This way, the directory will server as a tool for understanding how
organization is structured. Organizational relationships between people or
records in the directory will be more easily ascertained. For example, a glance
at the record (or just the DN) of uid=Marvin,ou=Accounting,dc=tomosolutions,dc=com,
and you will know that Marvin works in the Accounting department.

But this structure has three things to consider:
* First, while organizational structures change—sometimes too frequently—relocating
DNs within the directory is not an easy task.
* Second, If user records are spread throughout the directory tree, then
applications will need to be smart enough to search all over the tree for
user records.
* A third consideration has to do with what other sorts of information you want
to store in your directory. If you are using the directory primarily as a
tool for modeling the organizational chart, then this particular method
of structuring the directory will be ideal for you. You can track employees,
assets ( eet vehicles, computers, and so on), and other resources within the
directory, and locate their position in the organization. But if the main
purpose of the directory is to create a directory of users of IT services, then
this structure will be less than ideal, requiring applications to do much more
work to locate users.
### Directory as IT service
The second theory is that the directory should be structured to represent the
way your system (networks, servers, user applications) will need to access the
records. In this case the structure of the LDAP directory should be optimized
for use by such IT services.

One common way to structure the directory is to split it into a unit for users,
a unit for groups, and a unit for system-level records that applications need,
but users will not require access to.

For example, you will have three OUs in this presentation: "System", "Users",
"Groups".

This structure also has some drawbacks:
* First, the directory structure does not, by design, provide any overt
clues to the structure of the organization. Of course organizational
information, such as department IDs, can be stored in individual records, and so can be retrieved that way.
* More importantly though, if the directory supports a large number of users,
the ou=Users branch is going to have a lot of records. But this problem can be
mitigated by adding additional subtrees under the "Users" branch.

So it is your choice of which one to use, the mirroring the organization, or
the one facilitating IT services. In our example, we will use the IT service
structure.

### Example
We will create three OUs - Users, Groups and System:
```bash
# Subtree for users
dn: ou=Users,dc=tomosolutions,dc=com
ou: Users
description: tomosolutions.com Users
objectClass: organizationalUnit

# Subtree for groups
dn: ou=Groups,dc=tomosolutions,dc=com
ou: Groups
description: tomosolutions.com Groups
objectClass: organizationalUnit

# Subtree for system accounts
dn: ou=System,dc=tomosolutions,dc=com
ou: System
description: Special accounts used by software applications.
objectClass: organizationalUnit
```
