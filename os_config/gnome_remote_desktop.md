# Setup gnome-remote-desktop with gdm

From `/usr/share/doc/gnome-remote-desktop/README.md`
`https://discourse.gnome.org/t/headless-remote-desktop-setup-process/20411`
`https://discourse.gnome.org/t/with-ssh-only-access-how-do-you-configure-headless-gnome-remote-desktop/24998`

RDP and VNC can be used for either system or user modes.

## System (RDP example)

Enable rdp for gdm and all users.

```bash
# Key must be readable by gnome-remote-desktop-user.
# A lot of error about TPM not working.
# Init TPM credentials failed because Failed to initialize transmission interface context: tcti:IO failure, using GKeyFile as fallback.
grdctl --system rdp set-tls-key ~gnome-remote-desktop/.local/share/gnome-remote-desktop/tls.key
grdctl --system rdp set-tls-cert ~gnome-remote-desktop/.local/share/gnome-remote-desktop/tls.crt
# Enter credentials via standard input
# grdctl --system rdp set-credentials << EOF
# rdppassword
# rdppassword
# EOF
grdctl --system rdp set-credentials rdpuser rdppassword
grdctl --system rdp enable

# Defaults to view only
grdctl --system rdp disable-view-only
```

```bash
# Enable system remote login service and GDM.
systemctl enable --now gdm.service
systemctl enable --now gnome-remote-desktop.service
```

## User (VNC example)

Unencrypted basic password VNC.

```bash
# Enable for logged in user (remote desktop assistance)
systemctl --user enable --now gnome-remote-desktop.service
```

```bash
grdctl vnc set-auth-method password
grdctl vnc set-password # Enter password via standard input
grdctl vnc disable-view-only
grdctl vnc enable
```

## Headless mode

"""
A single user headless remote desktop means the remote desktop client connects
directly to a GNOME Remote Desktop server running in an independently set up
headless graphical user session.
"""

Not sure what would set that up. xvfb?

```bash
grdctl --headless rdp set-tls-key ~/.local/share/gnome-remote-desktop/tls.key
grdctl --headless rdp set-tls-cert ~/.local/share/gnome-remote-desktop/tls.crt
grdctl --headless rdp set-credentials # Enter credentials via standard input
grdctl --headless rdp disable-view-only
grdctl --headless rdp enable

grdctl --headless vnc set-password # Enter password via standard input
grdctl --headless vnc disable-view-only
grdctl --headless vnc enable

systemctl --user enable --now gnome-remote-desktop-headless.service
```

## Make tls key and cert

The --system cert needs to be accesable by gnome-remote-desktop user.
That user's homedir is /var/lib/gnome-remote-desktop.

`grctl --system status` says cert ok for winpr-makecert and openssl is ok.

winpr-makecert

```bash
dnf install -y freerdp
sudo -u gnome-remote-desktop sh -c 'winpr-makecert -silent -rdp -path ~/.local/share/gnome-remote-desktop tls'
```

certtool

```sh
# Need to use a template file to set expiry
mkdir -p ~/.local/share/gnome-remote-desktop/
certtool --generate-privkey --outfile ~/.local/share/gnome-remote-desktop/tls.key
certtool --generate-self-signed --load-privkey ~/.local/share/gnome-remote-desktop/tls.key
```

openssl req

```bash
mkdir -p ~gnome-remote-desktop/.local/share/gnome-remote-desktop/
chown -Rv gnome-remote-desktop:gnome-remote-desktop ~gnome-remote-desktop/.local
openssl req -new -newkey rsa:4096 -days 3600 -nodes -x509 -subj /C=US/ST=NONE/L=NONE/O=GNOME/CN=gnome.org -out ~gnome-remote-desktop/.local/share/gnome-remote-desktop/tls.crt -keyout ~gnome-remote-desktop/.local/share/gnome-remote-desktop/tls.key
chown -Rv gnome-remote-desktop:gnome-remote-desktop ~gnome-remote-desktop/.local/share/gnome-remote-desktop
```

```bash
mkdir -p ~/.local/share/gnome-remote-desktop/
openssl req -new -newkey rsa:4096 -days 3600 -nodes -x509 -subj /C=US/ST=NONE/L=NONE/O=GNOME/CN=gnome.org -out ~/.local/share/gnome-remote-desktop/tls.crt -keyout ~/.local/share/gnome-remote-desktop/tls.key
```

## Firewalld

```bash
sudo firewall-cmd --permanent --add-service=rdp
sudo firewall-cmd --reload
```

```bash
sudo firewall-cmd --permanent --add-service=vnc
sudo firewall-cmd --reload
```
