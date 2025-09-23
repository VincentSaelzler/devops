# devops

## development environment

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
# locale configuration
sed '/^#en_US.UTF-8 UTF-8/s/^#//' /etc/locale.gen -i
locale-gen
ln -sf /etc/locale.conf /etc/default/locale
echo 'LANG=en_US.UTF-8' > /etc/default/locale
useradd --create-home --groups wheel marcus
# wsl configuration
cat > /etc/wsl.conf << 'EOF'
[boot]
systemd=true
[user]
default=marcus
[network]
hostname=palatine
EOF
# passwordless sudo
pacman -Syu sudo vi
visudo
# uncomment this line (near the end of the file)
# %wheel ALL=(ALL:ALL) NOPASSWD: ALL
exit
```

back to windows powershell as administrator

```ps
# rebooting from within wsl does NOT work
wsl --terminate archlinux
```

non-root wsl shell. open arch linux using start menu

```sh
sudo pacman -S git ansible openssh
ssh-keygen
cat ~/.ssh/id_ed25519.pub
```

manually add ssh key to cloud infrastructure platforms.

- github.com > pic > settings > ssh and gpg keys

```sh
git clone git@github.com:VincentSaelzler/devops.git
# trust this ssh key fingerprint
SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU
ansible-playbook ~/devops/ansible/palatine.yml
```

## under construction...

need ansible locally in order to make the remote server do anything  
dev setup for python projects on arch  
install ansible-core on arch

```yml
{
    "editor.formatOnSave": true
}
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
