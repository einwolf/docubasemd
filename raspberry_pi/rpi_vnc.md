# Enable the VNC server

Raspbian has a RealVNC server.

```bash
# Do as root
vncpasswd -service
# Edit /root/.vnc/config.d/vncserver-x11
# Add Authentication=VncAuth
systemctl enable --now vncserver-x11-serviced.service
```

## Example /root/.vnc/config.d/vncserver-x11

```bash
Password=dbd83cfd727a1458
Authentication=VncAuth
```
