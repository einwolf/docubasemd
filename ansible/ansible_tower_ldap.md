# Ansible tower LDAP user authentication

Add the cert for the LDAP server to /etc/openldap/certs.

If the LDAP server has a self signed cert add `TLS_REQCERT allow` to /etc/openldap/ldap.conf.

## Low Trust

Authentication type LDAP

Name                  |Setting                                   |Note
---                   |---                                       |---
LDAP Server URI       |ldaps:/ /lt-win-ad.viprcenter.com:636     |x
LDAP Bind DN          |cn=ldapauth,cn=Users,dc=viprcenter,dc=com
LDAP Bind Password    |                                          |Show doesn't reveal password
LDAP User DN Template |blank                                     |Overrides LDAP user search field when used
LDAP Group Type       |ActiveDirectoryGroupType                  |Remove member_attr from LDAP Group Type Parameters to avoid save error
LDAP Require Group    |blank
LDAP Deny Group       |blank

The next sections are apparently fed directly to django-ldap-auth.

## LDAP User Search

```json
{
    "DC=viprcenter,DC=com",
    "SCOPE_SUBTREE",
    "(sAMAccountName=%(user)s"
}
```

## LDAP Group Search

```json
{
    "DC=viprcenter,DC=com",
    "SCOPE_SUBTREE",
    "(objectClass=group)"
}
```

## LDAP User Attribute Map

There don't appear to be any other keywords because it complains if you add one.

```json
{
    "first_name": "givenName",
    "last_name": "sn",
    "email": "mail
}
```

## LDAP Group Type Parameters

I don't know what this is for. Authentication works with it blank.

Remove `member_attr` if using LDAP Group Type=ActiveDirectoryGroupType.

```json
{
    "name_attr": "cn"
}
```

## LDAP User Flags by Group

There appears to only be two types of users.

```json
{
    "is_superuser": [
         "CN=Ansible Tower Admins,CN=Users,DC=viprcenter,DC=com"
    ],
    "is_system_auditor": []
}
```

## LDAP Organization Map

```json
{
    "VIPR Center": {
        "admins": "CN=Ansible Tower Admins,CN=Users,DC=viprcenter,DC=com",
        "remove_admins": true,
        "users": false,
        "remove_users": true
    }
}
```

## LDAP Team Map

Not using this currently.

```json
{}
```
