+++
title = "after proxmox reboot, found a couple issues"
date = 2025-08-11
+++

had some kernel updates go through on my latest dist upgrade for my proxmox host.
after a clean shutdown I noticed my metrics from telegraf lxc weren't getting to my grafana graphs (ipmi-ensor data).  so poking around the lxc it looks like I lost my export INFLUX_TOKEN=xxxx after reboot boot. in hindsight not surprising as I didnt save it anywhere.
first things first I had to login to my influx browser instance http://<local_IP>:8086 and regen a token and executable command for telegraf (should be writing these down somewhere I suppose 😁)..next, sort out the easy way to boot those and start telegraf 
