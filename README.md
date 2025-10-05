# devops

## development environment

windows with wsl installed

### manual configuration

windows powershell as administrator

```ps
wsl --list --verbose
wsl --terminate archlinux
wsl --unregister archlinux
wsl --install archlinux
```

root wsl shell should auto-start. if not, use `wsl --distribution archlinux`

> ⚠️ restart windows if dns networking issues occur

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

back to windows powershell as administrator

```ps
# rebooting from within wsl does not work
wsl --terminate archlinux
```

non-root wsl shell. open arch linux using start menu

```sh
# install system dependencies
sudo pacman -S git python-pipx openssh pass gnupg
pipx ensurepath
source ~/.bashrc
# install ansible
pipx install ansible-core
pipx inject ansible-core linode_api4 ansible_specdoc
ansible-galaxy collection install community.general
# create ssh and gpg keys
ssh-keygen
gpg --full-generate-key
```

```sh
# configure pass
pass init # [gpg key id]
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

### automated configuration

ansible handles everything else

```sh
ansible-playbook ~/devops/ansible/palatine.yml
code-oss ~/devops/
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
