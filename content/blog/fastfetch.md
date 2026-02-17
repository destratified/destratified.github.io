+++
title = "fastfetch"
date = "2026-02-16"

[taxonomies]
tags = ["tag1", "tag2"]
+++

# fastfetch 
 ## 09:06
inspired by the cachyos default terminal splash i started digging in to the defaults, configs to tweak and make it my own.
<https://github.com/fastfetch-cli/fastfetch/blob/dev/README.md>
following the instructions from above, the install was easy 
`sudo pacman -Syu fastfetch`
the config file is a default so if you wanna start editing you can use the `fastfetch --gen-config` or using one of the examples in the example folder of the repo.
i liked the 07.jsonc and based the below off of that:
```shell
// Load with --config examples/2.jsonc
// Note that you must replace the image path to an existing image to display it.

{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json",
    "logo": {
        "type": "auto",
	"source": "arch",
        "padding": {
            "top": 3,
            "left" : 5,
            "right": 6
        }
    },
    "display": {
        "separator": " -> "
    },
    "modules": [
        "title",
        "separator",
        {
            "type": "os",
            "key": "OS",
            "keyColor": "cyan",
            "format": "{2}"
        },
        {
            "type": "os",
            "key": "├─{icon}", // Just get your distro's logo off nerdfonts.com
            "keyColor": "cyan"
        },
        {
            "type": "kernel",
            "key": "├─Kernel",
            "keyColor": "cyan"
        },
        {
            "type": "packages",
            "key": "├─Packages",
            "keyColor": "cyan"
        },
        {
            "type": "shell",
            "key": "╰─Shell",
            "keyColor": "cyan"
        },
        "break",

        {
            "type": "wm",
            "key": " DE/WM",
            "keyColor": "cyan"
        },
        {
            "type": "lm",
            "key": "├─LM",
            "keyColor": "cyan"
        },
        {
            "type": "wmtheme",
            "key": "├─WM",
            "keyColor": "cyan"
        },
        {
            "type": "icons",
            "key": "├─Icons",
            "keyColor": "cyan"
        },
        {
            "type": "terminal",
            "key": "╰─Terminal",
            "keyColor": "cyan"
        },

        "break",
        {
            "type": "host",
            "key": "󰌢 PC",
            "keyColor": "red"
        },
        {
            "type": "cpu",
            "key": "├─CPU",
            "keyColor": "red"
        },
        {
            "type": "gpu",
            "key": "├─GPU",
            "keyColor": "red"
        },
        {
            "type": "disk",
            "key": "├─{mount-from}-{mountpoint}",
            "keyColor": "red"
        },
	{
            "type": "memory",
            "key": "├─Memory",
            "keyColor": "red"
        },
        {
            "type": "swap",
            "key": "├─Swap",
            "keyColor": "red"
        },
        {
            "type": "display",
            "key": "├─Display",
            "keyColor": "red"
        },
	{
            "type": "localip",
            "key": "├─IP",
            "compact": true,
            "keyColor": "red"
        },
        {
            "type": "wifi",
            "key": "├─Wifi-network",
            "format": "{ssid}",
            "keyColor": "red"
        },
        {
            "type": "uptime",
            "key": "╰─Uptime",
            "keyColor": "red"
        },

        "break",
        {
            "type": "sound",
            "key": " SOUND",
            "keyColor": "cyan"
        },
        {
            "type": "player",
            "key": "├󰥠",
            "keyColor": "cyan"
        },
        {
            "type": "media",
            "key": "└󰝚",
            "keyColor": "cyan"
        },
    ]
}
```
deleted the item i didnt care about, set icons and words to help readibility and got rid of the cachyos logo in place of the arch default

i spent a good amount of time futzing with the Disk(s) section as it would just print without telling me the disk or mountpoint...if you need to see what the format options are you can use the following command:
```shell
fastfetch -h {modulename}-format
```

this gave me the details so i could insert it into the json.  it will then do that for any disks
```shell
    {
            "type": "disk",
            "key": "├─{mount-from}-{mountpoint}",
            "keyColor": "red"
        },
```
