# devops

create a local developement environment for python projects and websites

provision cloud resources on linode to host websites

## operating system and ide

arch linux on windows subsystem for linux

ide is the fully open source version of visual studio code from arch package manager

**not** using the propriatary remote-wsl visual studio code extension from microsoft

## install arch linux

windows powershell as administrator

```ps
wsl --list --verbose
wsl --terminate archlinux
wsl --unregister archlinux
wsl --install archlinux
```

root wsl shell should auto-start and run an inital script related to pacman keys

type `exit` twice once that finishes to close the powershell window

## arch bootstrapping: locale, user, hostname

start **archlinux** app from start menu

> ⚠️ restart windows if dns networking issues occur  
> ⚠️ paste by right clicking the blank area in the wsl/archlinux app  
> ⚠️ copy/pasting within a wsl prompt of a powershell window can be inconsistent

```sh
# locale
sed '/^#en_US.UTF-8 UTF-8/s/^#//' /etc/locale.gen -i
locale-gen
ln -sf /etc/locale.conf /etc/default/locale
echo 'LANG=en_US.UTF-8' > /etc/default/locale
# wsl
cat > /etc/wsl.conf << 'EOF'
[boot]
systemd=true
[user]
default=marcus
[network]
hostname=palatine
EOF
# non-root user and passwordless sudo
useradd --create-home --groups wheel marcus
pacman -Syu sudo vi
visudo
# uncomment this line (near the end of the file)
# %wheel ALL=(ALL:ALL) NOPASSWD: ALL
exit
```

back to windows powershell (admin not required)

```ps
# rebooting from within wsl does not work
wsl --terminate archlinux
```

## dependencies

open **archlinux** app using start menu

prompt should now say `marcus@palatine`

```sh
# install system packages
sudo pacman -S git python-pipx openssh pass gnupg
pipx ensurepath
source ~/.bashrc
# install ansible and supporting python libraries
pipx install ansible-core
pipx inject ansible-core linode_api4 ansible_specdoc
ansible-galaxy collection install community.general
```

## secrets

> ⚠️ hard-coded name and email address  
> ⚠️ ansible vault password must be stored/managed externally

```sh
# create ssh and gpg keys
ssh-keygen
gpg --quick-gen-key "Vincent Saelzler <vincent.saelzler@gmail.com>"
# configure pass
pass init vincent.saelzler@gmail.com
pass insert ansible/vault
# [enter ansible vault password]
# show gpg and ssh key info
gpg --armor --export
cat ~/.ssh/id_ed25519.pub
```

add ssh and gpg keys to cloud infrastructure platforms.

- github.com > pic top right > settings > ssh and gpg keys
- linode.com > name top right > ssh keys

```sh
git clone git@github.com:VincentSaelzler/devops.git
# trust this ssh key fingerprint
# SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU
```

## automated configuration

ansible handles everything else to set up the development environment and clone project repositories

```sh
ansible-playbook ~/devops/ansible/palatine.yml
```

## open visual studio code

```sh
code-oss ~/devops/
code-oss ~/websites/
```

## useful commands

```ps
# list each distro’s name and base path
# appears to be saved in
# %USERPROFILE%\AppData\Local\wsl\[guid]

Get-ChildItem HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss |
  ForEach-Object {
    Get-ItemProperty -Path $_.PSPath |
      Select-Object DistributionName, BasePath
  }
```
