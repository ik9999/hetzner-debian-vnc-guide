# hetzner-debian-vnc
Setting up XFCE4 VNC server on hetzner. OS debian 12.

```
apt-get update
apt-get full-upgrade
apt-get install vim
vim /etc/hosts #edit hostname to a custom one
vim /etc/hostname #edit hostname to a custom one 
reboot
adduser eq
usermod -aG sudo eq
sudo -l -U eq
echo "eq        ALL=(ALL)       NOPASSWD: ALL" > /etc/sudoers.d/eq-nopasswd
echo "AllowUsers eq" >> /etc/ssh/sshd_config
echo "PasswordAuthentication no" >> /etc/ssh/sshd_config
echo "PubkeyAuthentication yes" >> /etc/ssh/sshd_config
sed -i '/^AcceptEnv/d' /etc/ssh/sshd_config
echo "AcceptEnv *" >> /etc/ssh/sshd_config
echo "PermitUserEnvironment yes" >> /etc/ssh/sshd_config
mkdir -p /home/eq/.ssh
cat ~/.ssh/authorized_keys > /home/eq/.ssh/authorized_keys
sudo apt-get install xfce4 xfce4-goodies task-xfce-desktop dbus-x11
sudo systemctl set-default graphical.target
sudo systemctl reboot
sudo apt install tigervnc-standalone-server tigervnc-common tightvncserver tigervnc-tools
sudo systemctl reboot
```

Edit `~/.vnc/xstartup`:

```
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4
```

Edit `/etc/tigervnc/vncserver.users`:

```
:1=eq
```

Edit `~/.vnc/config`:

```
session=xfce
geometry=1920x1080
alwaysshared
localhost=0
```

Run

```
vncserver #Enter password
vncserver -kill hostname:1
sudo systemctl enable --now tigervncserver@:1.service
```
