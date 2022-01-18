# .config

## My .config

### *Work in Progress*

---
![screenshot](https://github.com/brandon82890/.config/blob/main/screenshots/example.jpg?raw=true)
---

My current setup.
Feel free to look through it.\
It probably won't work out of the box.\
If you want to try something from it, research it first.\
The documentation should contain every step to take to reproduce this set up.

    OS: Arch Linux
    Window Manager: Sway
    Terminal: Foot with Tmux 
    Shell: Zsh
    Editor: Neovim
---
### Features
---
- Tiling Window Manager with Vim-like keybinds

- Vim like keybinds to navigate Firefox

- Seamless pane switching within terminal multiplex panes and neovim panes

- Caps lock remapped to ESC and CTRL

- Easy installation of native language servers for Neovim

- Fully integrated IDE in Neovim

- Light footprint. It would probably run on a potato.

---
# Documentation
- [Install Arch Linux with System-d boot](docs/systemd_arch_install.md)

- [Remap Caps Lock to ESC and CTRL](docs/remap_capslock.md)

- [Setup Development Environment](docs/setup.md)
---
### Bugs
---
- Have to run :PackerSync before :MarkdownPreview in nvim

---
# Credits
### Where I learned all this:

- [Arch Wiki](https://wiki.archlinux.org/)

- [Systemd boot guide from Addictivetips.com](https://www.addictivetips.com/ubuntu-linux-tips/set-up-systemd-boot-on-arch-linux/)

Where I learned Vim:
- [Boost  your Coding FU with VSCode and Vim](https://www.barbarianmeetscoding.com/blog/boost-your-coding-fu-with-vscode-and-vim)

Where I learned how to set up neovim:
- [Tuff Gniuz Neovim Lua Config Playlist](https://www.youtube.com/watch?v=qb6fPgZMRF4&list=PLIHtvvGZ1O3jBXdp9Id02vRuOEOWIGB_w)

Tmux and Neovim setup inspired by:
- [This dev.to article](https://dev.to/hlappa/my-web-development-environment-alacritty-tmux-neovim-4pd2)
