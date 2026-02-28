+++
title = "arch-cachyos-updater"
date = "2026-02-27"

[taxonomies]
tags = ["pacman", "arch", "cachyos"]
+++

# arch-cachyos-updater 
 ## 20:01

two scripts i've setup for updating arch or cachyos respectively.
setting some cool colors and icons, mimic-ing pacman...and some feedback/letting you know what its doing notifications.

i setup a file reflector_timestamp that won't run reflector in the execution if the mirrors were updated within last 12 hours, just pacman -Syu. runs reflector and pacman -Syu and creates the timestamp file if it isn't detected...

## arch updater script

update.sh
```shell
#!/bin/bash

# Colors
bold_cyan="\e[1;36m"
bold_white="\e[1;37m"
reset="\e[0m"

echo -e "${bold_cyan}:: ${bold_white}Starting Update..."

# Path to the timestamp file
timestamp_file="$HOME/reflector_timestamp"

# Current time in seconds since epoch
current_time=$(date +%s)

# Check if the file exists
if [[ -f "$timestamp_file" ]]; then
    # Get the last modified time of the timestamp file
    file_mod_time=$(stat -c %Y "$timestamp_file")
    # Compute the age of the file
    file_age=$((current_time - file_mod_time))

    # Check if the file age is less than 12 hours (43200 seconds)
    if [[ $file_age -lt 43200 ]]; then
        echo -e "${bold_cyan}:: ${bold_white}Reflector mirrors < 12hr old...${reset}"
        cat /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu --noconfirm
        echo -e "${bold_cyan}[Done]${reset}"
        exit 0
    else
        echo -e "${bold_cyan}:: ${bold_white}Reflector mirrors old...${reset}"
        echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
        sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --latest 10 --download-timeout 5 --save /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}:: ${bold_white}Selected Servers:${reset}"
        cat /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu --noconfirm
    fi
else
    echo -e "${bold_cyan}:: ${bold_white}Timestamp file does not exist...${reset}"
    echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
    sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --latest 10 --download-timeout 5 --save /etc/pacman.d/mirrorlist
    echo -e "${bold_cyan}:: ${bold_white}Selected Servers:${reset}"
    cat /etc/pacman.d/mirrorlist
    echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
    sudo pacman -Syu --noconfirm
    # Create the timestamp file
    echo -e "${bold_cyan}==> ${bold_white}Saving timestamp file...${reset}"
    touch "$timestamp_file"
    echo -e "${bold_cyan}[Done]${reset}"
    exit 0
fi

# Update the timestamp file with the current time
echo -e "${bold_cyan}==> ${bold_white}Updating timestamp file now...${reset}"
touch "$timestamp_file"
echo -e "${bold_cyan}[Done]${reset}"
```
## cachyos updater script

similar, but added in the cachyos-rate-mirrors script as well for the cachyos specific mirrors.  i also edited the cachyos-rate-mirrors script to block it trying to use rate-mirror method to update arch repos, as i prefer how reflector does that...

update.sh
```shell
#!/bin/bash

# Colors
bold_cyan="\e[1;36m"
bold_white="\e[1;37m"
reset="\e[0m"

echo -e "${bold_cyan}:: ${bold_white}Starting Rate-mirrors...${reset}"
sudo cachyos-rate-mirrors

# Path to the timestamp file
timestamp_file="$HOME/reflector_timestamp"

# Current time in seconds since epoch
current_time=$(date +%s)

# Check if the file exists
if [[ -f "$timestamp_file" ]]; then
    # Get the last modified time of the timestamp file
    file_mod_time=$(stat -c %Y "$timestamp_file")
    # Compute the age of the file
    file_age=$((current_time - file_mod_time))

    # Check if the file age is less than 12 hours (43200 seconds)
    if [[ $file_age -lt 43200 ]]; then
        echo -e "${bold_cyan}:: ${bold_white}Reflector mirrors < 12hr old...${reset}"
        cat /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu --noconfirm
        echo -e "${bold_cyan}[Done]${reset}"
        exit 0
    else
        echo -e "${bold_cyan}:: ${bold_white}Reflector mirrors old...${reset}"
        echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
        sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --latest 10 --download-timeout 5 --save /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}:: ${bold_white}Selected Servers:${reset}"
        cat /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu --noconfirm
    fi
else
    echo -e "${bold_cyan}:: ${bold_white}Timestamp file does not exist...${reset}"
    echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
    sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --latest 10 --download-timeout 5 --save /etc/pacman.d/mirrorlist
    echo -e "${bold_cyan}:: ${bold_white}Selected Servers:${reset}"
    cat /etc/pacman.d/mirrorlist
    echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
    sudo pacman -Syu --noconfirm
    # Create the timestamp file
    echo -e "${bold_cyan}==> ${bold_white}Saving timestamp file...${reset}"
    touch "$timestamp_file"
    echo -e "${bold_cyan}[Done]${reset}"
    exit 0
fi

# Update the timestamp file with the current time
echo -e "${bold_cyan}==> Updating timestamp file now...${reset}"
touch "$timestamp_file"
echo -e "${bold_cyan}[Done]${reset}"
```

commenting out the arch repo section in the cachyos-rate-mirror script 
found at /usr/bin

```shell
whereis cachyos-rate-mirrors
sudo nano {above result}
```

cachyos-rate-mirror
```shell
# Rate Arch Linux mirrors anyway
#rate_repository_mirrors arch "${MIRRORS_DEFAULT_DIR}/mirrorlist"
```

seems to do the trick...