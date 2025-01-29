# hetzner-debian-vnc
Setting up XFCE4 VNC server on hetzner. OS debian 12.

```
apt-get update
apt-get full-upgrade
apt-get install vim
systemctl enable ssh
systemctl restart ssh
vim /etc/hosts #edit hostname to a custom one
vim /etc/hostname #edit hostname to a custom one
sudo dpkg-reconfigure tzdata # set UTC
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
su eq
mkdir -p /home/eq/.ssh
sudo cat /root/.ssh/authorized_keys > /home/eq/.ssh/authorized_keys
sudo apt-get install xfce4 xfce4-goodies task-xfce-desktop dbus-x11
sudo systemctl set-default graphical.target
sudo systemctl reboot
sudo apt install tigervnc-standalone-server tigervnc-common tightvncserver tigervnc-tools
sudo systemctl reboot
su eq
```

Edit `/etc/gai.conf` (use ipv4 for outgoing connections):

```
precedence ::ffff:0:0/96  100
scopev4 ::ffff:0.0.0.0/96       14
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
alwaysshared/etc/gai.conf
localhost=0
```

Run

```
sudo chmod 777 ~/.vnc/xstartup
vncserver #Enter password
vncserver -kill hostname:1
sudo systemctl enable --now tigervncserver@:1.service
sudo systemctl status tigervncserver@:1.service
```

# Installing wine

```
sudo apt update && sudo apt upgrade
sudo apt install software-properties-common apt-transport-https curl -y
sudo dpkg --add-architecture i386
curl -fSsL https://dl.winehq.org/wine-builds/winehq.key | gpg --dearmor | sudo tee /usr/share/keyrings/winehq.gpg > /dev/null
echo deb [signed-by=/usr/share/keyrings/winehq.gpg] http://dl.winehq.org/wine-builds/debian/ $(lsb_release -cs) main | sudo tee /etc/apt/sources.list.d/winehq.list
sudo apt update
sudo apt install winehq-stable --install-recommends
wine --version
WINEPREFIX="$HOME/.wine" WINEARCH=win64 wine wineboot
```

# Installing winetricks

```
cd ~
sudo apt-get install zenity cabextract
wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks
chmod 777 winetricks
```

# Installing pyenv python3 + pipenv

```
sudo apt-get install git
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev
curl -fsSL https://pyenv.run | bash
```

Add to `~/.bashrc`:

```
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init - bash)"
```

# Install nodejs + pm2

```
sudo su
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
apt-get install nodejs -y
sudo npm install -g pm2
pm2 startup
```
