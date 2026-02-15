+++
title = "z790-dasharo"
date = "2026-01-19"

[taxonomies]
tags = ["desktop", "dasharo"]
+++

#  MSI z790p with dasharo 
 ## 11:58
well i'm looking at throwing in the towel on devices that aren't fully free software based. not just from the operating system, but also bios. i have a system76 lemur13 laptop which is the system76 version of coreboot, and my next objective is to do the same with the desktop and my server, or perhaps a new server. right now i've been pouring over forums and manuals, and found surprisingly that another version of the coreboot os - dasharo - is available on msi-z790 mb. i am currently running a msi-z790 carbon, not the pro version but it looks like my ram and cpu are on the HCL's so i ordered a pre-flashed mobo to give it a shot.  3mdeb provides the pre-flashed boards as well as full cpus, and links to laptops, desktops, mini's etc for those who are interested.

looking forward to giving it a shot. hope the RAM works too as RAM is hella expensive rn. there is another question of whether the dGPU i have, NVIDIA 4070ti will work. thankfully i should be able to use some other options AMD6950 etc and different RAM if needed. but looking to get near to the same capability i have rn with existing box. we shall see (im)patiently till the packages arrive.

research the EFF and total ownership of your devices!

(UPDATE 02/25/2026)
installed and working! i had to switch ram to a different one on the dasharo HCl. then change bios to XMP#1 profile and do a CMOS reset to get to post. happily my 4070ti GPU works out of the box! only small issue is that the fan profile doesnt work from bios, but a quick install of lm_sensors and using pwmconfig i was able to get control. after a little more fooling around i installed the fancontrol app from the cosmic apps and setup a profile with targets for a little better GUI control and responsiveness. everything else works as expected. 

now i'm going to try and setup my ollama instance again and try using podman this time, was having issues getting docker to utilize the gpu passthru.  

i also was able to restore my win11 vmm in virt-manager so i have access to my work stuff remotely again.