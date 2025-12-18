+++
title = "proxmox"
date = "2025-12-18"
[taxonomies]
tags = ["proxmox", "server"]
+++

# proxmox - playing with setup (updated)
 ## 15:25
 one of the first things i did when setting up my server, was take it out of the box and check everything out.  i've never had a server before, been mostly building hi-end pcs for years and playing with all os's.  but i started looking into homelabbing and ordered what i thought i needed.  i ordered semi-custom and it arrived packaged and ready to go.  i probably could have kept the ram, the cpus, the psus, and the raid card (mini-monolithic), but some initial reading convinced me i needed more.  here's what i ended up with (updated):
	 Dell 730xd chassis with 12x3.5 HDD up front and the rear 2x2.5 HDD
	  - rear slots, 800gb SSD's in raid 0 with zfs for OS, EFI etc
	  - up front, 6x19TB SAS 7gbs HDDs in raidz1-0 for storage
	  - up front, 2x2TB SAS 7gbs HDD in Raidz1-0 for local backup
	  - ASUS Hyper M.2 X16 Gen 4 PCIE 4.0/3.0 x4 NVME RAID Expansion Card mgx w/ 3x 1TB nvme in zfs as storage for vms, and 1TB nfs passthough cache for jellyfin transcoding
	  - Dell HBA330 mini monolithic (=9300-8i) w/ P16 IT mode
	  - 2x SR2JW INTEL XEON E5-2698V4 2.20GHZ 20-CORE 50MB 135W CPU PROCESSOR
	  - 2x 750W PSUs
	  - 