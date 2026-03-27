# Installing gitlab-ce

## Almalinux 10

Docs at <https://docs.gitlab.com/install/package/>

<https://docs.gitlab.com/install/package/almalinux/>

<https://docs.gitlab.com/security/reset_user_password/>

```bash
# Setup yum package repo
curl --location "https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh" | sudo bash

# Install gitlab-ce with site url
EXTERNAL_URL="https://gitlab.example.com" dnf install gitlab-ce

# Use self signed certificate
# Installer will pick up certificates in this file location
mkdir -p /etc/gitlab/ssl
chmod 755 /etc/gitlab/ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/gitlab/ssl/gitlab.example.com.key -out /etc/gitlab/ssl/gitlab.example.com.crt
chmod 600 /etc/gitlab/ssl/gitlab.example.com.key
chmod 644 /etc/gitlab/ssl/gitlab.example.com.crt

cp -v /etc/gitlab/ssl/gitlab.example.com.crt /etc/gitlab/trusted-certs/

# Configure gitlab to not use let's encrypt
nano /etc/gitlab/gitlab.rb
external_url 'https://gitlab.example.com'
nginx['ssl_certificate'] = "/etc/gitlab/ssl/gitlab.example.com.crt"
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/gitlab.example.com.key"
nginx['redirect_http_to_https'] = true
letsencrypt['enable'] = false

gitlab-ctl reconfigure
gitlab-ctl restart

# Firewalld
firewall-cmd --permanent --add-service=https
firewall-cmd --reload 

# Initial password valid for 24 hours
cat /etc/gitlab/initial_root_password
```

## Weak password checking

```bash
/opt/gitlab/embedded/service/gitlab-rails/config/weak_password_digests.yml
/opt/gitlab/embedded/service/gitlab-rails/lib/gitlab/tracking/helpers/weak_password_error_event.rb
/opt/gitlab/embedded/service/gitlab-rails/lib/security/weak_passwords.rb
```
