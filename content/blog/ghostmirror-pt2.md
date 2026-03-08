+++
title = "ghostmirror-pt2"
date = "2026-03-07"

[taxonomies]
tags = ["arch, cachyos", "update"]
+++

# ghostmirror-pt2 
 ## 17:32

finally settled on a temp solution. ghostmirror is writing an update, looking to be able to pull any mirrorlist in and evaluating for rates/etc. i helped a little re-formating their readme.md - I'm an actual contributer! while i am waiting for their update, i fixed up my little script.

it checks a timestamp file, if it doesnt exist, it does a full pull of cachyos through rate-mirrors and uses a global list with reflector and runs ghostmirror on top of that. if the file exists and its less than 7 days old, it will just run pacman -Syu, if its older than 7 days it will do a minor rebuild:

```shell
#!/bin/bash

# Colors
bold_cyan="\e[1;36m"
bold_white="\e[1;37m"
reset="\e[0m"

# Path to the timestamp file
timestamp_file="$HOME/update_timestamp"

# Current time in seconds since epoch
current_time=$(date +%s)

# Check if the file exists
if [[ -f "$timestamp_file" ]]; then
    # Get the last modified time of the timestamp file
    file_mod_time=$(stat -c %Y "$timestamp_file")
    # Compute the age of the file
    file_age=$((current_time - file_mod_time))

    # Check if the file age is < than 7 days (604800 seconds)
    if [[ $file_age -lt 604800 ]]; then
        echo -e "${bold_cyan}:: ${bold_white}Mirrors < 7 days old...${reset}"

        echo -e "${bold_cyan}:: ${bold_white}Current CachyOS Servers:${reset}"
        cat /etc/pacman.d/cachyos-mirrorlist

	echo -e "${bold_cyan}:: ${bold_white}Current Arch Servers:${reset}"
        cat /etc/pacman.d/mirrorlist

        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu
        echo -e "${bold_cyan}[Done]${reset}"
        exit 0
    else
        echo -e "${bold_cyan}:: ${bold_white}Mirrors older than 7 days, updating...${reset}"

        echo -e "${bold_cyan}==> ${bold_white}Starting Rate-mirrors...${reset}"
        rate-mirrors cachyos --disable-comments | sudo tee /etc/pacman.d/cachyos-mirrorlist
        # Add corrections for proper pacman.conf formatting
        cp -f --backup=simple --suffix="-backup" "/etc/pacman.d/cachyos-mirrorlist" "/etc/pacman.d/cachyos-v3-mirrorlist"
        sed -i 's|/$arch/|/$arch_v3/|g' "/etc/pacman.d/cachyos-v3-mirrorlist"
        echo -e "${bold_cyan}:: ${bold_white}CachyOS Selected Servers:${reset}"
        cat /etc/pacman.d/cachyos-mirrorlist

        echo -e "${bold_cyan}==> ${bold_white}Ranking existing mirrors with ghostmirror...${reset}"
        #ranking with ghostmirror
        sudo ghostmirror -PomulsS /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist light state,uptodate,speed,estimated
        echo -e "${bold_cyan}:: ${bold_white}Arch Selected Servers:${reset}"
        cat /etc/pacman.d/mirrorlist

        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu
    fi
else
    echo -e "${bold_cyan}:: ${bold_white}Timestamp file does not exist...${reset}"

    echo -e "${bold_cyan}==> ${bold_white}Starting Rate-mirrors...${reset}"
    rate-mirrors --disable-comments cachyos | sudo tee /etc/pacman.d/cachyos-mirrorlist
    # Add corrections for proper pacman.conf formatting
    sudo cp -f --backup=simple --suffix="-backup" "/etc/pacman.d/cachyos-mirrorlist" "/etc/pacman.d/cachyos-v3-mirrorlist"
    sudo sed -i 's|/$arch/|/$arch_v3/|g' "/etc/pacman.d/cachyos-v3-mirrorlist"

    #echo -e "${bold_cyan}:: ${bold_white}CachyOS Selected Servers:${reset}"
    #cat /etc/pacman.d/cachyos-mirrorlist

    echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
    sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --number 50 --download-timeout 5 --save /etc/pacman.d/mirrorlist

    echo -e "${bold_cyan}== ${bold_white}Ranking mirrorlist with Ghostmirror...${reset}"
    sudo ghostmirror -PomulsS /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist light state,uptodate,speed,estimated

    echo -e "${bold_cyan}:: ${bold_white}Arch Selected Servers:${reset}"
    cat /etc/pacman.d/mirrorlist

    echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
    sudo pacman -Syu

    # Update timestamp file
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