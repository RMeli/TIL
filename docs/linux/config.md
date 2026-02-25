# Configuration

## OEM Installation

Since Ubuntu `24.04`, the OEM installer doesn't show up in the GRUB menu by default.

From the live USB, you can install the OEM version by pressing using the following:

```bash
sudo mkdir -p /usr/share/desktop-provision
sudo bash -c 'echo "mode: oem" > /usr/share/desktop-provision/whitelabel.yaml' # (1)!
/snap/bin/ubuntu-desktop-bootstrap --try-or-install # (2)!
```

1. Creates a configuration file that tells the installer to run in OEM mode.
2. Runs the installer in OEM mode, allowing you to set up the system for end users.

## Nerd Fonts

Installing [Nerd Fonts] (Hack Nerd Font, in this case):

```bash
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.4.0/Hack.zip
unzip Hack.zip
mkdir -p ~/.fonts/
cp *.ttf ~/.fonts/
fc-cache -fv
```

Once the fonts are installed, they can be used or selected (for example, in the terminal).

[Nerd Fonts]: https://www.nerdfonts.com/ ()
