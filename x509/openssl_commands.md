# OpenSSL Commands

## Get certificate from TLS connection.

```bash
openssl s_client -connect ldap-server.com:636 -showcerts 1> client_out.txt << EOF
EOF

openssl x509 -in client_out.txt -noout -text > client_cert_info.txt
```

## Add pem certificate to java key store.

```bash
# Prompts are for a new keystore. If keystore exists keytool will prompt to overwrite existing certificate.
# changeit is a java known default password.
keytool -importcert -keystore keystore.jks -file lt-win-ad2.pem << EOF
changeit
changeit
yes
EOF
```