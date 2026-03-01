+++
title = "cachyos-updater-options-pt2"
date = "2026-02-28"

[taxonomies]
tags = ["cachyos", "pacman"]
+++

# cachyos-updater-options-pt2 
 ## 20:05

working through this a bit in a discussion with CSCS on cachyos forum...i have a few options:

- the cachyos-rate-mirrors script with tweaks
- rate-mirror options with some tweaks from cachyos-rate-mirrors
- my script from the last post

## cachyos-rate-mirrors

```shells
#!/usr/bin/env bash
# Copyright (C) 2022-2025 CachyOS team
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along
# with this program; if not, write to the Free Software Foundation, Inc.,
# 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
set -e

declare -r MIRRORS_DEFAULT_DIR="/etc/pacman.d"

# Use https only protocol for rating
export RATE_MIRRORS_PROTOCOL=https
export RATE_MIRRORS_FETCH_MIRRORS_TIMEOUT="${RATE_MIRRORS_FETCH_MIRRORS_TIMEOUT:-30000}"
export RATE_MIRRORS_ALLOW_ROOT=true
export RATE_MIRRORS_MAX_DELAY="${RATE_MIRRORS_MAX_DELAY:-10000}"

disable_colors() {
    unset ALL_OFF BOLD BLUE GREEN RED YELLOW
}

enable_colors() {
    # prefer terminal safe colored and bold text when tput is supported
    if tput setaf 0 &>/dev/null; then
        ALL_OFF="$(tput sgr0)"
        BOLD="$(tput bold)"
        RED="${BOLD}$(tput setaf 1)"
        GREEN="${BOLD}$(tput setaf 2)"
        YELLOW="${BOLD}$(tput setaf 3)"
        BLUE="${BOLD}$(tput setaf 4)"
    else
        ALL_OFF="\e[0m"
        BOLD="\e[1m"
        RED="${BOLD}\e[31m"
        GREEN="${BOLD}\e[32m"
        YELLOW="${BOLD}\e[33m"
        BLUE="${BOLD}\e[34m"
    fi
    readonly ALL_OFF BOLD BLUE GREEN RED YELLOW
}

if [[ -t 2 ]]; then
    enable_colors
else
    disable_colors
fi

msg() {
    local mesg="$1"; shift
    printf "${GREEN}==>${ALL_OFF}${BOLD} ${mesg}${ALL_OFF}\n" "$@" >&2
}

info() {
    local mesg="$1"; shift
    printf "${YELLOW} -->${ALL_OFF}${BOLD} ${mesg}${ALL_OFF}\n" "$@" >&2
}

error() {
    local mesg="$1"; shift
    printf "${RED}==> ERROR:${ALL_OFF}${BOLD} ${mesg}${ALL_OFF}\n" "$@" >&2
}

cleanup() {
    exit "${1:-0}"
}

die() {
    (($#)) && error "$@"
    cleanup 255
}

if [ "$EUID" -ne 0 ]; then
    die "Please, run script as root"
fi

declare -r TMPFILE="$(mktemp)"
trap 'rm -f -- "$TMPFILE"' EXIT

rate_repository_mirrors() {
    local repo="$1"
    local path="$2"

    info "Ranking mirrors for ${repo} repository in ${path}..."

    if rate-mirrors --save "$TMPFILE" "$repo"; then
        if [ ! -f "${path}" ]; then
            die "${path} doesn't exist! You must install package $(pacman -Fq "$path" 2>/dev/null || echo "that contains $path")"
        fi
        cp -f --backup=simple --suffix="-backup" "${TMPFILE}" "${path}"
        msg "Done [${repo}] ${path}"
    else
        die "rate-mirrors failed [errcode=$?]."
    fi
}

force_ru_mirrors() {
    local mirror domain index
    local -a ru_mirrors=(
        'https://archlinux.gay/cachy/repo/$arch/$repo'
        'https://mirror.yandex.ru/cachyos/repo/$arch/$repo'
    )

    local -i line=1
    for ((index=0; index < ${#ru_mirrors[@]}; index++)); do
        mirror="${ru_mirrors[$index]}"
        domain="${mirror##*://}"
        domain="${domain%%/*}"
        if curl -sL --connect-timeout 5 "https://${domain}" &>/dev/null; then
            sed -i "$((line))iServer = ${mirror}" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist"
            ((line++))
        else
            error "Failed to connect with ${domain} mirror"
        fi
    done
}

# Prefer mirrors that are geographically close, especially in countries that
# have regional blockages
country="$(curl --connect-timeout 10 -sSL 'https://geoip.kde.org/v1/ubiquity' | grep -Po '<CountryCode>\K([A-Z]{2})')"

if [ -n "$country" ]; then
    export RATE_MIRRORS_ENTRY_COUNTRY="$country"
fi

# China only tests its own mirrors, so disable neighbors
# and increase the number of tested mirrors to get more results
if [ "$country" = "CN" ]; then
    export RATE_MIRRORS_COUNTRY_NEIGHBORS_PER_COUNTRY=0
    export RATE_MIRRORS_COUNTRY_TEST_MIRRORS_PER_COUNTRY=50
fi

# Rate Arch Linux mirrors anyway
rate_repository_mirrors arch "${MIRRORS_DEFAULT_DIR}/mirrorlist"

#added for mirrorlist file cleanup/clarity
cat /etc/pacman.d/mirrorlist | sed '/^\s*#/d;/^$/d' | sudo tee /etc/pacman.d/mirrorlist


# Always insert CDN77 mirror to the start, unless RU and CN region is detected
if [ "$country" != "RU" ] && [ "$country" != "CN" ]; then
    sed -i '1iServer = https://archlinux.cachyos.org/repo/$repo/os/$arch' "${MIRRORS_DEFAULT_DIR}/mirrorlist"
fi

rate_repository_mirrors cachyos "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist"

#added for mirrorlist file cleanup/clarity
cat /etc/pacman.d/cachyos-mirrorlist | sed '/^\s*#/d;/^$/d' | sudo tee /etc/pacman.d/cachyos-mirrorlist

# Always insert local mirrors as first due to blockings in RU region
if [ "$country" = "RU" ]; then
    force_ru_mirrors
fi

cp -f --backup=simple --suffix="-backup" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist" \
    "${MIRRORS_DEFAULT_DIR}/cachyos-v3-mirrorlist"
cp -f --backup=simple --suffix="-backup" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist" \
    "${MIRRORS_DEFAULT_DIR}/cachyos-v4-mirrorlist"

sed -i 's|/$arch/|/$arch_v3/|g' "${MIRRORS_DEFAULT_DIR}/cachyos-v3-mirrorlist"
sed -i 's|/$arch/|/$arch_v4/|g' "${MIRRORS_DEFAULT_DIR}/cachyos-v4-mirrorlist"

# In the case of a more restrictive umask setting ( 0077 for example ), give read-permissions back to 'Group/Other'.
# This fixes a case where the third party package wrapper Aura ( https://github.com/fosskers/aura ) tries to parse
# /etc/pacman.conf and can't read the "include"ded mirror files. By default, Aura intentionally runs without root
# permissions for most non-modify operations.
chmod go+r "${MIRRORS_DEFAULT_DIR}"/*mirrorlist*
```

talking with the developer, some of the meat could be pruned from this depending on your geo. those obviously not concerned with RU and CN mirrors could delete these sections, but this would really only benefit with clarity, as its not really affecting speeds or anything.

also there is the bottom of the file chmod and sed commands that if you use rate-mirrors instead will need to be sure to include. my thought process for the second option is to use rate-mirrors with the above tweaks inside an update.sh or similar bash script and include pacman -Syu

## the rate-mirror option

```shell
#!/bin/bash

MIRRORS_DEFAULT_DIR=/etc/pacman.d

rate-mirrors --disable-comments cachyos | sudo tee /etc/pacman.d/cachyos-mirrorlist

rate-mirrors --disable-comments arch | sudo tee /etc/pacman.d/mirrorlist

sudo cp -f --backup=simple --suffix="-backup" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist" \
    "${MIRRORS_DEFAULT_DIR}/cachyos-v3-mirrorlist"
sudo cp -f --backup=simple --suffix="-backup" "${MIRRORS_DEFAULT_DIR}/cachyos-mirrorlist" \
    "${MIRRORS_DEFAULT_DIR}/cachyos-v4-mirrorlist"

sudo sed -i 's|/$arch/|/$arch_v3/|g' "${MIRRORS_DEFAULT_DIR}/cachyos-v3-mirrorlist"
sudo sed -i 's|/$arch/|/$arch_v4/|g' "${MIRRORS_DEFAULT_DIR}/cachyos-v4-mirrorlist"

sudo pacman -Syu
```

this is what i would call a work in progress...

