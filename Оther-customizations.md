# Оther customizations

**Harden mount options**
```bash
sudo sed -i \
    -e '/\/var\/tmp/ s/\(defaults\|errors=remount-ro\)/\1,nodev,noexec,nosuid/' \
    -e '/\/tmp/ s/\(defaults\|errors=remount-ro\)/\1,nodev,noexec,nosuid/' \
    -e '/\/var\/log\/audit/ s/\(defaults\|errors=remount-ro\)/\1,nodev,noexec,nosuid/' \
    -e '/\/var\/log/ s/\(defaults\|errors=remount-ro\)/\1,nodev,noexec,nosuid/' \
    -e '/\/var / s/\(defaults\|errors=remount-ro\)/\1,nodev,nosuid/' \
    -e '/\/home/ s/\(defaults\|errors=remount-ro\)/\1,nodev,nosuid/' \
    /etc/fstab
sudo sh -c 'echo -e "tmpfs /dev/shm tmpfs defaults,relatime,inode64,nodev,noexec,nosuid 0 0" >> /etc/fstab'
sudo systemctl daemon-reload
sudo mount -o remount -a
```

## Gnome Tuning
### Change gnome fonts

```bash
gsettings set org.gnome.desktop.interface monospace-font-name "Cascadia Mono Bold 11"
gsettings set org.gnome.desktop.interface document-font-name "Cascadia Code Bold 11"
gsettings set org.gnome.desktop.interface font-name "Roboto Medium 11"
```

### Enable minimize and maximize buttons 
```bash
gsettings set org.gnome.desktop.wm.preferences button-layout ":minimize,maximize,close"
```

## Tuning Terminal

### Install zsh and oh-my-zsh

```bash
sudo dnf install zsh -y 
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
#### Install zsh plugins and theme

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions 
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
#### Configure .zshrc

```bash
sed -i 's/^ZSH_THEME=.*/ZSH_THEME="powerlevel10k\/powerlevel10k"/' ~/.zshrc
sed -i '/^plugins=/ s/)/ zsh-autosuggestions zsh-syntax-highlighting)/' ~/.zshrc
source ~/.zshrc
```
### Install and enable Neofetch

```bash
sudo dnf install neofetch -y
echo "neofetch" >> ~/.zshrc
```

### Install and enable ruby gem colorls

```bash
sudo gem install colorls -y
cat << EOF >> ~/.zshrc
alias ll='colorls -lA --sd --gs --group-directories-first'
alias ls='colorls --group-directories-first'
source $(dirname $(gem which colorls))/tab_complete.sh
EOF
```

