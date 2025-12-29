+++
title = "NFS pass-through"
date = "2025-12-28"

[taxonomies]
tags = ["NFS", "proxmox", "arch"]
+++

# NFS pass-through in proxmox to arch linux vm
 ## 14:38

continuing my proxmox install saga...
## setting up nfs

i got the fans running quietly, now it was time to sort out a few things.  i wanted to migrate my nas container manager data to the new server.  this meant creating a zfs file system raidz1-0 with my big disks (18TBx6) giving me roughly 80gb usable space with redundancy.  i made the zfs /storage with the 6 disks selected and voila!  storage.  now i needed to move files to my server.  for this to work i needed to do a couple things.  i needed nfs on my proxmox, so i could serve the folders to both my nas and my future jellyfin instance.  i created a single disk zfs with my 4tb nvme for just transcoding and setup the nfs server on pve host:

```
apt install nfs-common
```
this downloads the drivers to host, then edit exports file

```
nano /etc/exports
```

then add your directories in the file:
```
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/storage/ 192.168.50.0/24(rw,sync,all_squash,no_subtree_check,anonuid=1000,anongid=1000)
/transcode/ 192.168.50.0/24(rw,sync,all_squash,no_subtree_check,anonuid=1000,anongid=1000)
```

then i reload nfs with systemctl and export the directories:
```
systemctl daemon-reload
exportfs -arv
```

next you need to chmod and chown your directories:
```
chmod 775 -R /transcode/ /storage/
chown -R joe:nfsgroup /transcode/ /storage/
```

the chmod sets to uid1000 basically and i had to create an nfs group nfsgroup and adduser joe so i had gid1000 so the permissions match the export details...this allows easy read writes on jellyfin and all other apps have same permission structure.  the arrs have the storage dir mapped on the client side as well.

now for the arch client, you have to add the nfs-utils package:
```
sudo pacman -Syu nfs-utils
```

then you can add the dirs and edit the etc/fstab with `nano /etc/fstab/`:
```
mkdir -p /transcode /storage
```

```
192.168.50.2:/storage /storage nfs rw,defaults,nofail  0 0
192.168.50.2:/transcode/ /transcode nfs rw,defaults,nofail  0 0
```
add the no fail, so if the nfs blows up or something the mount won't bonk the vm boot (i use this on my laptop and desktop too

at this point you should have the directories setup, so do another `systemctl daemon-reload` and `sudo mount -a` to check and make sure everything works.  i like to just put a test file on the server-side before the mount on the client, using `touch test.file` and that way you should see the file on the clinet side in the directories you just mounted.  

now, some people may have multiple users on the client and the server.  you need to make sure these match.  i also had to make sure the permissions matched on the NAS so when i ran the rsync command all permissions match between server and 2 clients.  at this point i also ran the chmod and chown commands on the clients to assure permission uniformity.

### notes
at this point all should be ready to copy files.  i used a rsync command on my synology NAS that I was ssh'd into to move all my files over to the server.  i followed trashguides directory structure when i originally setup the *arrs* on the nas so it looks like this:
```
---storage
   - data
     - media
       |- books
       |- movies
       |- music
       |- tv
```