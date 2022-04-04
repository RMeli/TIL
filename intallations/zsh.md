# ZSH

Many HPC clusters do not come with [ZSH](https://www.zsh.org/) installed.

## Install ZSH Locally

Installation of ZSH without `sudo`:

```bash
cd && mkdir software && cd software
wget https://sourceforge.net/projects/zsh/files/latest/download/zsh-X.Y.tar.xz
./configure --prefix=$HOME/zsh
make && make install
```

## Set ZSH as Default Shell

```bash
export PATH=$HOME/zsh/bin:$PATH
```

Add the following to `.bash_profile`:

```bash
[ -f $HOME/zsh/bin/zsh ] && exec $HOME/zsh/bin/zsh -l
```

### Notes

Do not switch to `zsh` in `.bashrc` since it is not compatible with some command line tools like `scp`. `.bash_profile` is only used for interactive shells.

## Install Oh My ZSH

Install [Oh My ZSH](https://ohmyz.sh/):

```bash
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```
