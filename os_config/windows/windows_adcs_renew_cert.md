# Renew cert in Windows Active Directory Certificate Services

Renew a SSL certificate for a web site by generating a certificate signing request on Linux and have it signed by the Windows Certification Service.

The default template on the Windows Certification Service for web servers has a maximum 2 year expiration.

On Linux

TODO: found out if you pass -cn it uses that for commonName directly and does not do the city/state/etc prompt.

```powershell
openssl genrsa -out patchrepo.viprcenter.com.key 4096
openssl req -new -sha256 -key patchrepo.viprcenter.com.key -out patchrepo.viprcenter.com.csr << EOF
US
FL
City
Organization
Organization Group
Host name
email
EOF

Fill out the locality questions with whatever you want.
```

The CSR doesn't have the template value OID the Windows Certification Service GUI wants to issue the certificate. Use certreq on the command line to issue it.

On Windows

```powershell
certreq.exe -submit -binary -attrib "CertificateTemplate:WebServer" -config LT-WIN-AD\viprcenter-LT-WIN-AD-CA .\patchrepo.viprcenter.com.req .\patchrepo.viprcenter.com.cer
```

Creates the certificate patchrepo.viprcenter.com.cer in DER binary format. Convert it to PEM format.

```powershell
openssl x509 -inform der -in patchrepo.viprcenter.com.cer -out patchrepo.viprcenter.com.pem
```
