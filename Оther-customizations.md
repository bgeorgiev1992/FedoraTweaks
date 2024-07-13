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

**Change gnome fonts**
```bash
gsettings set org.gnome.desktop.interface monospace-font-name "Cascadia Mono Bold 11"
gsettings set org.gnome.desktop.interface document-font-name "Cascadia Code Bold 11"
gsettings set org.gnome.desktop.interface font-name "Roboto Medium 11"
```

**Enable minimize and maximize buttons**
```bash
gsettings set org.gnome.desktop.wm.preferences button-layout ":minimize,maximize,close"
```
**Enable system tray icons**
```bash
sudo dnf install -y libappindicator libappindicator-gtk3 libayatana-appindicator-gtk3 gnome-shell-extension-appindicator
```

## Tuning Terminal

**Install zsh and oh-my-zsh**

```bash
sudo dnf install zsh -y 
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
**Install zsh plugins and theme**

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions 
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
**Configure .zshrc**

```bash
sed -i 's/^ZSH_THEME=.*/ZSH_THEME="powerlevel10k\/powerlevel10k"/' ~/.zshrc
sed -i '/^plugins=/ s/)/ zsh-autosuggestions zsh-syntax-highlighting)/' ~/.zshrc
source ~/.zshrc
```
**Install and enable Neofetch**

```bash
sudo dnf install neofetch -y
echo "neofetch" >> ~/.zshrc
```

**Install and enable ruby gem colorls**

```bash
sudo gem install colorls -y
cat << EOF >> ~/.zshrc
alias ll='colorls -lA --sd --gs --group-directories-first'
alias ls='colorls --group-directories-first'
source $(dirname $(gem which colorls))/tab_complete.sh
EOF
```
## System utilities

**Install some system utilities**
```bash
sudo dnf install -y gnome-extensions-app file-roller file-roller-nautilus nautilus-extensions nautilus-python gnome-tweaks
```
## Mail clients

**Evolution**
```bash
sudo dnf install -y evolution evolution-data-server evolution-data-server-langpacks
```
**Thunderbird**
```bash
sudo dnf install -y thunderbird thunderbird-wayland
```
## Tuning Firefox

**This theme follows latest GNOME Adwaita style**
```bash
curl -s -o- https://raw.githubusercontent.com/rafaelmardojai/firefox-gnome-theme/master/scripts/install-by-curl.sh | bash
```
#### Enabling optional features
Optional features can be enabled by creating new `boolean` preferences in `about:config`.

1. Go to the `about:config` page
2. Type the key of the feature you want to enable
3. Set it as a `boolean` and click on the add button
4. Restart Firefox

##### Features

- **Hide single tab** `gnomeTheme.hideSingleTab`

	Hide the tab bar when only one tab is open.

	> **Note:** You should move the new tab button out of the tabbar or it will be hidden when there is only one tab. You can rearrange the toolbars doing a right-click on any toolbar and selecting "Customize Toolbar…".

	> **Note 2:** This feature doesn't work when tabs are hidden, like when using the Simple Tab Groups extension. In this case, there's no way to tell from CSS that there's only one visible tab.

- **Normal width tabs** `gnomeTheme.normalWidthTabs`

	Use normal width tabs as default Firefox.

- **Swap tab close button position** `gnomeTheme.swapTabClose`

	By default the tab close buttons follows the position of the window controls, this preference reverts that behavior.

- **Bookmarks toolbar under tabs** `gnomeTheme.bookmarksToolbarUnderTabs`

	Move Bookmarks toolbar under tabs.

- **Active tab contrast** `gnomeTheme.activeTabContrast`

	Add more contrast to the active tab.

- **Close only selected tabs** `gnomeTheme.closeOnlySelectedTabs`

	Show the close button on the selected tab only.

- **System icons** `gnomeTheme.systemIcons`

	Use system theme icons instead of Adwaita icons included by theme.

	> **Note:** This feature has a [known color bug](#icons-color-broken-with-system-icons).


- **No themed Icons** `gnomeTheme.noThemedIcons`

	Use default Firefox icons instead of the included icons.


- **Symbolic tab icons** `gnomeTheme.symbolicTabIcons`

	Make all tab icons look kinda like symbolic icons.

- **Hide WebRTC indicator** `gnomeTheme.hideWebrtcIndicator`

	Hide redundant WebRTC indicator since GNOME provides their own privacy icons in the top right.

- **Hide unified extensions button** `gnomeTheme.hideUnifiedExtensions`

	Hide unified extensions button from the navbar, you can also use `extensions.unifiedExtensions.enabled` instead, which is only going to work till Firefox 111.

- **Drag window from headerbar buttons** `gnomeTheme.dragWindowHeaderbarButtons`

	Allow dragging the window from headerbar buttons.

	> **Note:** This feature is BUGGED. It can activate the button with unpleasant behavior.

- **Tabs as headerbar** `gnomeTheme.tabsAsHeaderbar`

	Place the tabs on the top of the window, and use the tabs bar to hold the window controls, like Firefox's standard tab bar.
	
	> **Note:** Enabling with `gnomeTheme.hideSingleTab` will replace the single tab with a title bar.

- **OLED black theme** `gnomeTheme.oledBlack`

	Change the dark theme into the black variant.

#### CSD have sharp corners
See upstream [bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1408360).

##### Wayland fix:
1. Go to the `about:config` page
2. Search for the `layers.acceleration.force-enabled` preference and set it to true.
3. Now restart Firefox, and it should look good!

##### X11 fix:
1. Go to the `about:config` page
2. Type `mozilla.widget.use-argb-visuals`
3. Set it as a `boolean` and click on the add button
4. Now restart Firefox, and it should look good!

## Tuning Thunderbird
**This theme follows latest GNOME Adwaita style**
```bash
git clone https://github.com/rafaelmardojai/thunderbird-gnome-theme && cd thunderbird-gnome-theme
./scripts/auto-install.sh
```
### Enabling optional features
Optional features can be enabled by creating new `boolean` preferences in the config editor.

1. Go to the Thunderbird settings
2. Search for `config` and click on the `Config Editor` button when it is displayed
3. Type the key of the feature you want to enable
4. Set it as a `boolean` and click on the add button
5. Restart Thunderbird

#### Features

- **Hide tabbar** `gnomeTheme.hideTabbar`

	Hides tabs bar with the menu bar. If you press `Alt` or enable menu bar it will be visible again.

- **Normal width tabs** `gnomeTheme.normalWidthTabs`

	Use normal width tabs as default Thunderbird.

- **Active tab contrast** `gnomeTheme.activeTabContrast`

	Add more contrast to the active tab.

