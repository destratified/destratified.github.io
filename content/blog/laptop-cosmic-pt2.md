+++
title = "laptop-cosmic-pt2"
date = "2026-01-13"

[taxonomies]
tags = ["laptop", "arch", "cosmic"]
+++

# laptop-cosmic-pt2 
 ## 18:29

so i've been doing alot of configuring since the laptop's come back online, some things broke as indicated in the last post, but i hadn't written anything down yet. here's a list of topics:

- bash
- snapper/btrfs-assistant
- backing up (see )
- obsidian (again)
- bash backup and /builds to git repo for easier install

### bash 

after getting the browser working, i decided to venture into souping up the bash prompt, as i was working with fish in the garuda install and liked some of extra features.  a little internet searching and i was starting to see some great options, and i get to stay in the bash work - shebang!

first things first, the bash prompt is boring. teasing you for more, even the defaults in the .bashrc are underwhelming compared to the fish config.  a couple options stood out, starship prompt, ble.sh seemed to cover most of what i wanted with a little tweaking to be done.

so first, i installed starship:
```
sudo pacman -S starship nerd-fonts 
```

this is in the arch repos so no issues there, everything is controlled from the starship.toml in ~/.config and the webpage has install instructions and some presets that can point you in the right direction. i chose the pure preset which is fairly simple.  i highly recommend installing the nerd-fonts package too as seen above as you will then have access to nerd-font symbols and some different fonts if you want it. i added all of them and also set my computer to Firacode which is like an old timer programming /typewriter stlye font.

for starship to work follow the install per your shell, many options even if you use fish or bash, or zsh
<https://starship.rs/guide/#%F0%9F%9A%80-installation>

for bash add the below to the `.bashrc` file:
```
eval "$(starship init bash)"
```

for pure preset you issue the following command at prompt:
```
starship preset pure-preset -o ~/.config/starship.toml
```

if that's all you want it's pretty clean and fresh. i also installed ble.sh which gives me autocomplete and history/search functions at the prompt. works incredibly well. here's that process below:

```
mkdir -p build 
cd build 
git clone --recursive https://github.com/akinomyoga/ble.sh.git
cd ble.sh
make
```

then add the source (plugin) to the `.bashrc.` file:
```
source out/ble.sh
```

i then tweaked colors, added a git icon and a buncha aliases for my (sort-of) final `.bashrc`:

```
# ~/.bashrc
#

# If not running interactively, don't do anything
[[ $- != *i* ]] && return

##PATH##
# set PATH so it includes user's private ~/.local/bin if it exists
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi

#bash time 24hr clock
alias date="date +'%H:%M:%S'"

#unlimited bash history
HISTSIZE=-1
HISTFILESIZE=-1

#ASCII art and greeting
paleofetch

#plugins
source -- ~/.local/share/blesh/ble.sh
eval "$(starship init bash)"

##Set some $ENVIRONMENTAL variable
export VISUAL=nano
export EDITOR=nano

# Replace ls with exa
alias ls='exa -al --color=always --group-directories-first --icons' # preferred listing
alias la='exa -a --color=always --group-directories-first --icons'  # all files and dirs
alias ll='exa -l --color=always --group-directories-first --icons'  # long format
alias lt='exa -aT --color=always --group-directories-first --icons' # tree listing
alias l.="exa -a | egrep '^\.'"                                     # show only dotfiles

# Common use
alias aup="pamac upgrade --aur"
#alias grubup="sudo update-grub"
#alias fixpacman="sudo rm /var/lib/pacman/db.lck"
alias tarnow='tar -acf '
alias untar='tar -zxvf '
alias wget='wget -c '
alias rmpkg="sudo pacman -Rdd"
#alias psmem='ps auxf | sort -nr -k 4'
#alias psmem10='ps auxf | sort -nr -k 4 | head -10'
#alias upd='sudo reflector --latest 5 --age 2 --fastest 5 --protocol https --sort rate --save /etc/pacman.d/mirrorlist && cat /etc/pacman.d/mirrorlist && sudo pacman -Syu && fish_update_completions && sudo updatedb'
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias .....='cd ../../../..'
alias ......='cd ../../../../..'
alias dir='dir --color=auto'
alias vdir='vdir --color=auto'
alias grep='grep --color=auto'
alias fgrep='fgrep --color=auto'
alias egrep='egrep --color=auto'
alias hw='hwinfo --short'                                   # Hardware Info

# Cleanup orphaned packages
alias cleanup='sudo pacman -Rns (pacman -Qtdq)'

# Get the error messages from journalctl
alias jctl="journalctl -p 3 -xb"

# Recent installed packages
alias rip="expac --timefmt='%Y-%m-%d %T' '%l\t%n %v' | sort | tail -200 | nl"

#personal
alias gitup='~/forge/scripts/forge.update.sh'
alias gitpu='~/forge/scripts/forge.push.sh'
alias up='~/forge/scripts/arch/update.sh'
alias src='source ~/.bashrc'
alias tailup='~/forge/scripts/tailup.sh'
alias taildn='tailscale down'
```

and my final `~/.config/startship.toml

```
# "$schema" = 'https://starship.rs/config-schema.json'
# ~/.config/starship.toml

add_newline = true

format = """\
  $directory\
  $git_branch$git_commit$git_state $git_status\
  $line_break $character
"""
right_format = """
$status"""

continuation_prompt = '▶▶ '

# style_choices:
# bold
# italic
# underline
# dimmed
# inverted
# blink
# hidden
# strikethrough
# bg:<color>
# fg:<color>
# <color>
# none

#NOTE: you can customize the symbols, e.g:
[character]
success_symbol = "[  ]($bold fg:bright-blue)"
error_symbol = "[  ]($bold fg:bright-red)"

[directory]
disabled = false
format = " [$path](bold $style)"
style = "cyan"
truncate_to_repo = false
#home_symbol = ""

[hostname]
ssh_only = false
format = '(white bold)[$hostname](#50fa7b bold)'
disabled = false

#[username]
#format = '(white bold)[$username  @ ](#50fa7b bold)'
#disabled= false

[git_branch]
format = " [ $branch]($style)"
style = "red"

[git_status]
ahead = '⇡${count}'
behind = '⇣${count}'
diverged = '⇕⇡${ahead_count}⇣${behind_count}'
format = '[[( $all_status$ahead_behind )](fg:yellow)]($style)'
style = "bg:bright-yellow"

[time]
disabled = false
format = '[[  $time ](fg:#a0a9cb )]($style)'
time_format = "%R"                            # Hour:Minute Format

[python]
format = "[${symbol}(${virtualenv}) ${version}]($style)"
style = "bold bright-yellow"
symbol = "[ ](bold bright-blue)"
version_format = "${raw}"
```

there's alot to play with here, but here's my prompt with the terminal called:

![this is a screenshot](/img/screenshotbash.png)

i also installed paleofetch and added that to the $PATH and `.bashrc ` in place of the depricated neofetch following directions at 
<https://github.com/ss7m/paleofetch.git>

### snapper and btrfs-assistant
