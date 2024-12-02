# Windows AD LDAP search

## Linux

```bash
# * is wildcard
dsquery user dc=example,dc=com -name <username>*
```

## Using with Active Directory

It requires authentication all the time. It uses a search user (ldapauth here) to authenticate and search ldap with. This user should have ldap read only permissions.

```bash
-x  Use basic auth instead of SASL auth
-h Host in host:port format
-H Host in URL format
-w Specify password
-W Prompt for password
-D bind distinguished name
-b search base
-s  Search scope base, sub, one, or children. sub is default.
```

Active directory will take UserPrincipalName `<sAMAccountName>@<domain FQDN>` format or NetBIOS format `<Domain NetBIOS Name>\<sAMAccountName>`

```bash
# Active directory will take user format user@domain.com
ldapsearch -H ldaps://win-ad.example.com -x -W -D "ldapauth@example.com" -b "dc=example,dc=com" -s sub "(objexampleectclass=*)"
ldapsearch -H ldaps://win-ad.example.com -x -W -D "ldapauth@example.com" -b "dc=example,dc=com" "(sAMAccountName=<username>)"
```

```bash
# LDAP user format
ldapsearch -x -H ldaps://lt-win-ad.example.com -D "cn=ldapauth,cn=Users,dc=example,dc=com" -W -b "dc=,dc=com" -s sub "(cn=*)" cn mail s
```
