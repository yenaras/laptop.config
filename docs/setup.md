# Setup 
## Sway
#### A Tiling Window Manager For Wayland
---
Install the following packages:
```
sudo pacman -S sway swaylock-effects wofi waybar tmux alacritty x-org-xwayland \
pulseaudio pulseaudio-alsa alsa-utils pavucontrols
```

When the install is finished, start the Sway GUI by typing ```sway``` in the terminal.
```
sway
```
---
## Zsh
This is where I like to switch shells from bash to zsh.  I also like to keep my home directory
clear of dot files. Before installing zsh, I like to make the .zshenv file to set environmental variables.
The .zshenv has to be in the home directory, but the rest of your dot files can be configured from the .zshenv
to be stored in the .config directory.

- Make this in your home directory
```
nvim .zshenv
```
Here is my .zshenv file.  It is also copied to my .config/zsh directory from the repo if you want to ```cp``` it.
```
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_DATA_HOME="$XDG_CONFIG_HOME/local/share"
export XDG_CACHE_HOME="$XDG_CONFIG_HOME/cache"
export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/ssh-agent.socket"

export EDITOR="nvim"
export VISUAL="nvim"

export ZDOTDIR="$XDG_CONFIG_HOME/zsh"
export HISTFILE="$ZDOTDIR/.zhistory"
export HISTSIZE=10000
export SAVEHIST=10000
```

- Now you can install zsh
```
sudo pacman -S zsh
```
---
## Paru
Paru is an AUR helper that lets you check the package builds before installing.  I really like this program
and find it very intuitive.  To learn about it, check out [their github page](https://github.com/Morganamilo/paru).

- Install ```git``` to be able to clone the Paru repository.
```
sudo pacman -S git
```
- Clone the repository and install the package with makepkg:

```
sudo pacman -S --needed base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```
---
## Foot
Foot is a terminal emulator that I really like. I keep Alacritty as a backup and like to use it for some things
because it is GPU accelerated, but I find foot has better font and color compatibility out of the box. I also like alacritty
because it is built in Rust, so it is very memory efficient.  My sway config uses Win+Enter to launch foot instead of Alacritty.
You can use whichever terminal you want, but this is just a guide to reproduce my setup. I will also install nvim-packer-git, which
is a package manager for neovim, and zsh-theme-powerlevel10k, which is the theme I am using for zsh.
- I think you need to install these
one at a time.
```
paru
paru foot
paru foot-terminfo
paru nvim-packer-git
paru ttf-meslo-nerd-font-powerlevel10k
paru zsh-theme-powerlevel10k
```
- Now we can switch our shell to zsh
```
chsh -s $(which zsh)
```
---
## Clone the .config
Now I can clone my .config and everything should mostly work after rebooting.
```
git clone git@github.com:brandon82890/.config.git
```
---
## Firefox Tridactyl
[Tridactyl](https://github.com/tridactyl/tridactyl) is a program that allows you to use vim like keybinds to navigate Firefox.
With it, I can seamlessly navigate between the web and other open windows without using my mouse or my hands ever leaving the home row.
```
sudo pacman -S firefox firefox-tridactyl
```
---
## Neovim setup
Now I will navigate to ~/.config/nvim/lua/packer-config/init.lua and run these in the neovim : menu
```
:luafile %
:PackerSync
```
This guide is a work in progress, and for my own documentation to reproduce my development environment incase I forget anything.

