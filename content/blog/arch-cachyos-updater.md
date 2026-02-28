+++
title = "arch-cachyos-updater"
date = "2026-02-27"

[taxonomies]
tags = ["pacman", "arch", "cachyos"]
+++

# arch-cachyos-updater scripts
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

    # Check if the file age is less than 12 hours (43200 seconds)
    if [[ $file_age -lt 43200 ]]; then
        echo -e "${bold_cyan}:: ${bold_white}Mirrors < 12hr old...${reset}"
        echo -e "${bold_cyan}:: ${bold_white}CachyOS Servers:${reset}"
        cat /etc/pacman.d/cachyos-mirrorlist
	    echo -e "${bold_cyan}:: ${bold_white}Arch Servers:${reset}"
        cat /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu --noconfirm
        echo -e "${bold_cyan} [Done]${reset}"
        exit 0
    else
        echo -e "${bold_cyan}:: ${bold_white}Mirrors old, updating...${reset}"
        echo -e "${bold_cyan}==> ${bold_white}Starting Rate-mirrors...${reset}"
        sudo cachyos-rate-mirrors
	    cat /etc/pacman.d/cachyos-mirrorlist | sed '/^\s*#/d;/^$/d' | sudo tee /etc/pacman.d/cachyos-mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
        sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --latest 10 --download-timeout 5 --save /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}:: ${bold_white}Selected Servers:${reset}"
        cat /etc/pacman.d/mirrorlist
        echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
        sudo pacman -Syu --noconfirm
    fi
else
    echo -e "${bold_cyan}:: ${bold_white}Timestamp file does not exist...${reset}"
    echo -e "${bold_cyan}==> ${bold_white}Starting Rate-mirrors...${reset}"
    sudo cachyos-rate-mirrors
    cat /etc/pacman.d/cachyos-mirrorlist | sed '/^\s*#/d;/^$/d' | sudo tee /etc/pacman.d/cachyos-mirrorlist
    echo -e "${bold_cyan}==> ${bold_white}Starting Reflector...${reset}"
    sudo reflector --verbose -c CA -c MX -c US --protocol https --sort rate --latest 10 --download-timeout 5 --save /etc/pacman.d/mirrorlist
    echo -e "${bold_cyan}:: ${bold_white}Selected Servers:${reset}"
    cat /etc/pacman.d/mirrorlist
    echo -e "${bold_cyan}==> ${bold_white}Starting pacman -Syu...${reset}"
    sudo pacman -Syu --noconfirm
    # Create the timestamp file
    echo -e "${bold_cyan}==> ${bold_white}Saving timestamp file...${reset}"
    touch "$timestamp_file"
    echo -e "${bold_cyan} [Done]${reset}"
    exit 0
fi

# Update the timestamp file with the current time
echo -e "${bold_cyan}==> Updating timestamp file now...${reset}"
touch "$timestamp_file"
echo -e "${bold_cyan} [Done]${reset}"
```

commenting out the arch repo section in the cachyos-rate-mirror script 
usually found at /usr/bin/cachyos-rate-mirrors

```shell
whereis cachyos-rate-mirrors
sudo nano {above result}
```

cachyos-rate-mirror mods
```shell
# Always insert CDN77 mirror to the start, unless RU and CN region is detected
#if [ "$country" != "RU" ] && [ "$country" != "CN" ]; then
#    sed -i '1iServer = https://archlinux.cachyos.org/repo/$repo/os/$arch' "${MIRRORS_DEFAULT_DIR}/mirrorlist-cach>
#fi

rate_repository_mirrors cachyos "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist"
```

and comment out the CDN77 shit or it'll keep adding to your reflector mirrorlist for arch. took me a minute to see where that was coming from

seems to do the trick... some caveats: this is a very meaty way to have and update look pretty. it is definately easier to use just cachyos-rate-mirrors to do all the above. i would probably do that if i didn't enjoy messing with stuff and making a pretty terminal output. that said cachyos-rate-mirrors is a bit hacky itself. 

rate-mirrors can cleanup output with a cli command --disable-comments but doesn't handle the cachyos-v3 v4 repo nomenclature properly, hence the garbage at the end of the cachyos-rate-mirror file:
```shell
 Always insert local mirrors as first due to blockings in RU region
if [ "$country" = "RU" ]; then
    force_ru_mirrors
fi

cp -f --backup=simple --suffix="-backup" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist" \
    "${MIRRORS_DEFAULT_DIR}/cachyos-v3-mirrorlist"
cp -f --backup=simple --suffix="-backup" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist" \
    "${MIRRORS_DEFAULT_DIR}/cachyos-v4-mirrorlist"

sed -i 's|/$arch/|/$arch_v3/|g' "${MIRRORS_DEFAULT_DIR}/cachyos-v3-mirrorlist"
sed -i 's|/$arch/|/$arch_v4/|g' "${MIRRORS_DEFAULT_DIR}/cachyos-v4-mirrorlist"

#cat /etc/pacman.d/cachyos-mirrorlist | sed '/^\s*#/d' | sudo tee /etc/pacman.d/cachyos-mirrorlist

# In the case of a more restrictive umask setting ( 0077 for example ), give read-permissions back to 'Group/Other>
# This fixes a case where the third party package wrapper Aura ( https://github.com/fosskers/aura ) tries to parse
# /etc/pacman.conf and can't read the "include"ded mirror files. By default, Aura intentionally runs without root
# permissions for most non-modify operations.
chmod go+r "${MIRRORS_DEFAULT_DIR}"/*mirrorlist*
```

i also don't like how cachyos-rate-mirrors passes all comments to the mirrorlist files...hence my use of the below to clean up output:
```shell
cat /etc/pacman.d/cachyos-mirrorlist | sed '/^\s*#/d' | sudo tee /etc/pacman.d/cachyos-mirrorlist
```

this clarifies the mirrorlist to only the server entries. i could do alot of this in the background. using the .service and .timer files for reflector and the same for cachyos-rate-mirrors, but again i like the pretty terminal output ;) 

does this cure that, yes, is it for everyone, probably not
