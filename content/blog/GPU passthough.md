+++
title = "GPU passthough"
date = "2025-12-28"

[taxonomies]
tags = ["GPU", "proxmox", "arch"]
+++

# GPU passthough in Proxmox to Arch linux vm
 ## 14:38

continuing my proxmox install saga...
i got the fans running quietly, now it was time to sort out a few things.  i wanted to migrate my nas container manager data to the new server.  this meant creating a zfs file system raidz1-0 with my big disks (18TBx6) giving me roughly 80gb usable space with redundancy.  i made the zfs /storage with the 6 disks selected and voila!  storage.  now i needed to move files to my server.  for this to work i needed to do a couple things.  i needed nfs on my proxmox, so i could serve the folders to both my nas and my future 