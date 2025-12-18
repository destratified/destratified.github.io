+++
title = "proxmox"
date = "2025-12-18"
[taxonomies]
tags = ["proxmox", "server"]
+++

# proxmox - playing with setup and install (updated)
 ## 15:25
 one of the first things i did when setting up my server, was take it out of the box and check everything out.  i've never had a server before, been mostly building hi-end pcs for years and playing with all os's.  but i started looking into homelabbing and ordered what i thought i needed.  i ordered semi-custom and it arrived packaged and ready to go.  i probably could have kept the ram, the cpus, the psus, and the raid card (mini-monolithic), but some initial reading convinced me i needed more.  here's what i ended up with (updated):
 
	 Dell 730xd chassis with 12x3.5 HDD up front and the rear 2x2.5 HDD
	  - rear slots, 800gb SAS 12gbs HDD's in raid 0 with zfs for OS, EFI etc
	  - up front, 6x19TB SAS 10gbs HDDs in raidz1-0 for storage
	  - up front, 2x2TB SAS 7gbs HDD in Raidz1-0 for local backup
	  - ASUS Hyper M.2 X16 Gen 4 PCIE 4.0/3.0 x4 NVME RAID Expansion Card mgx
	  - Dell HBA330 mini monolithic (=9300-8i) w/ P16 IT mode
	  - 2x SR2JW INTEL XEON E5-2698V4 2.20GHZ 20-CORE 50MB 135W CPU PROCESSOR
	  - 2x 750W PSUs
	  - 512GB (8x 64gb ) DDR4 2400T 2400Mhz ECC REG RAM
	  - NVIDIA Quadro P2000 5GB GDDR5 Quad PCIe graphics card
	  - 4 port NIC 2x10gb 2x1GB (kept)

knowing what my average use is rn, i'd say i overkilled the ram by about 2x...ill move 256GB over to the next build, and I could have kept the processors, but it never hurts and im not sure how many lxc/vms i'll end up with.

one of the most fun parts of the initial boot, which took me about 1 month to prep with given all the upgrades above, was the lack of screen/mouse/keyboard and monitor...i patched this together and after being very frustrated bought an old used console for 50 bucks - shipped! this made startup easier.  the second most interesting thing was the fans absolutely screaming at me.  apparently after adding non-compatible pcie devices the bios defaults to 80% fan.  this is fixable with a script, but made initial boot and install super annoying!

# to be continued...
