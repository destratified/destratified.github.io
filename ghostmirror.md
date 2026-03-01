+++
title = "ghostmirror"
date = "2026-03-01"

[taxonomies]
tags = ["ghostmirror", "cachyos"]
+++

# ghostmirror 
 ## 16:51
 
so i've been playing around with ghostmirror a bit, first i got my rate-mirror script working with the help of CSCS at cachyos forum, this was used to test the command in the update.sh script

update-mirrors.sh
```shell
!/bin/bash

rate-mirrors --disable-comments cachyos | sudo tee /etc/pacman.d/cachyos-mirrorlist

rate-mirrors --disable-comments arch | sudo tee /etc/pacman.d/mirrorlist

sudo cp -f --backup=simple --suffix="-backup" "/etc/pacman.d/cachyos-mirrorlist" "/etc/pacman.d/cachyos-v3-mirrorlist"
#sudo cp -f --backup=simple --suffix="-backup" "/etc/pacman.d/cachyos-mirrorlist" "/etc/pacman.d/cachyos-v4-mirrorlist"

sudo sed -i 's|/$arch/|/$arch_v3/|g' "/etc/pacman.d/cachyos-v3-mirrorlist"
#sudo sed -i 's|/$arch/|/$arch_v4/|g' "/etc/pacman.d/cachyos-v4-mirrorlist"

sudo pacman -Syyu
```

here's the output after running my update script on top of the rate-mirror provided mirrorlists:
```shell
==> Starting Update...
:: Building CachyOS Servers List:
[sudo] password for joe:
 --> Ranking mirrors for cachyos repository in /etc/pacman.d/cachyos-mirrorlist...
# STARTED AT: 2026-03-01 15:22:32.177893597 -07:00
# VERSION: 0.27.0
# ARGS: rate-mirrors --save /tmp/tmp.oVENCYT6oG cachyos
# MIRRORS LEFT AFTER FILTERING: 24
# JUMP #1
# EXPLORING US
# VISITED US
# BLANK ITERATION
# COUNTRY JUMPING YIELDED TOO FEW MIRRORS (0), ADDING OTHERS TO UNLABELED
#

# TESTING UNLABELED MIRRORS
# SpeedTestResult { speed: 6.4 MB/s; elapsed: 920ms; connection_time: 278ms }
# SpeedTestResult { speed: 866.8 KB/s; elapsed: 987ms; connection_time: 224ms }
# SpeedTestResult { speed: 418.7 KB/s; elapsed: 934ms; connection_time: 277ms }
# SpeedTestResult { speed: 655.1 KB/s; elapsed: 931ms; connection_time: 280ms }
# SpeedTestResult { speed: 1.3 MB/s; elapsed: 965ms; connection_time: 328ms }
# SpeedTestResult { speed: 1.1 MB/s; elapsed: 979ms; connection_time: 404ms }
# SpeedTestResult { speed: 586.1 KB/s; elapsed: 977ms; connection_time: 508ms }
# SpeedTestResult { speed: 894.0 KB/s; elapsed: 952ms; connection_time: 525ms }
# SpeedTestResult { speed: 396.9 KB/s; elapsed: 990ms; connection_time: 563ms }
# SpeedTestResult { speed: 272.6 KB/s; elapsed: 885ms; connection_time: 589ms }
# SpeedTestResult { speed: 523.8 KB/s; elapsed: 1.00s; connection_time: 716ms }
# SpeedTestResult { speed: 444.9 KB/s; elapsed: 992ms; connection_time: 739ms }
# SpeedTestResult { speed: 202.0 KB/s; elapsed: 950ms; connection_time: 731ms }
# SpeedTestResult { speed: 516.9 KB/s; elapsed: 982ms; connection_time: 730ms }
# SpeedTestResult { speed: 903.3 KB/s; elapsed: 981ms; connection_time: 727ms }
# SpeedTestResult { speed: 686.7 KB/s; elapsed: 978ms; connection_time: 732ms }
# SpeedTestResult { speed: 796.8 KB/s; elapsed: 945ms; connection_time: 776ms }
# SpeedTestResult { speed: 540.2 KB/s; elapsed: 962ms; connection_time: 814ms }
# SpeedTestResult { speed: 575.0 KB/s; elapsed: 997ms; connection_time: 950ms }
# SpeedTestResult { speed: 284.0 KB/s; elapsed: 993ms; connection_time: 1.02s }
# SpeedTestResult { speed: 273.7 KB/s; elapsed: 896ms; connection_time: 1.21s }
# SpeedTestResult { speed: 231.2 KB/s; elapsed: 991ms; connection_time: 1.23s }
# SpeedTestResult { speed: 156.2 KB/s; elapsed: 837ms; connection_time: 1.44s }
# SpeedTestResult { speed: 150.1 KB/s; elapsed: 959ms; connection_time: 1.51s }
#

# RE-TESTING TOP MIRRORS
# SpeedTestResult { speed: 6.4 MB/s; elapsed: 641ms; connection_time: 193ms }
# SpeedTestResult { speed: 2.8 MB/s; elapsed: 991ms; connection_time: 393ms }
# SpeedTestResult { speed: 1.8 MB/s; elapsed: 990ms; connection_time: 448ms }
# SpeedTestResult { speed: 1.1 MB/s; elapsed: 984ms; connection_time: 885ms }
# SpeedTestResult { speed: 2.6 MB/s; elapsed: 954ms; connection_time: 415ms }
# ==== RESULTS (top re-tested) ====
#   1. SpeedTestResult { speed: 6.4 MB/s; elapsed: 641ms; connection_time: 193ms } -> https://us.cachyos.org/repo/
#   2. SpeedTestResult { speed: 2.8 MB/s; elapsed: 991ms; connection_time: 393ms } -> https://ca.mirror.cx/cachyos/repo/
#   3. SpeedTestResult { speed: 2.6 MB/s; elapsed: 954ms; connection_time: 415ms } -> https://mirror.allthingslinux.org/cachyos/repo/
#   4. SpeedTestResult { speed: 1.8 MB/s; elapsed: 990ms; connection_time: 448ms } -> https://cachyos.doridian.net/repo/
#   5. SpeedTestResult { speed: 1.1 MB/s; elapsed: 984ms; connection_time: 885ms } -> https://mirror.cachyos.org/repo/
#   6. SpeedTestResult { speed: 866.8 KB/s; elapsed: 987ms; connection_time: 224ms } -> https://us-mnz.soulharsh007.dev/cachyos/repo/
#   7. SpeedTestResult { speed: 796.8 KB/s; elapsed: 945ms; connection_time: 776ms } -> https://at.cachyos.org/repo/
#   8. SpeedTestResult { speed: 686.7 KB/s; elapsed: 978ms; connection_time: 732ms } -> https://de-nue.soulharsh007.dev/cachyos/repo/
#   9. SpeedTestResult { speed: 655.1 KB/s; elapsed: 931ms; connection_time: 280ms } -> https://cdn.lansing2600.org/cachyos/repo/
#  10. SpeedTestResult { speed: 586.1 KB/s; elapsed: 977ms; connection_time: 508ms } -> https://cdn77.cachyos.org/repo/
#  11. SpeedTestResult { speed: 575.0 KB/s; elapsed: 997ms; connection_time: 950ms } -> https://mirror.jura12.ru/repo/
#  12. SpeedTestResult { speed: 540.2 KB/s; elapsed: 962ms; connection_time: 814ms } -> https://mirror.lesviallon.fr/cachy/repo/
#  13. SpeedTestResult { speed: 523.8 KB/s; elapsed: 1.00s; connection_time: 716ms } -> https://mirror.hb9hil.org/cachyos/repo/
#  14. SpeedTestResult { speed: 516.9 KB/s; elapsed: 982ms; connection_time: 730ms } -> https://nl.mirror.cx/cachyos/repo/
#  15. SpeedTestResult { speed: 444.9 KB/s; elapsed: 992ms; connection_time: 739ms } -> https://no.mirror.cx/cachyos/repo/
#  16. SpeedTestResult { speed: 418.7 KB/s; elapsed: 934ms; connection_time: 277ms } -> https://mirrors.lansing2600.org/cachyos/repo/
#  17. SpeedTestResult { speed: 396.9 KB/s; elapsed: 990ms; connection_time: 563ms } -> https://cachyos.next-works.it/repo/
#  18. SpeedTestResult { speed: 284.0 KB/s; elapsed: 993ms; connection_time: 1.02s } -> https://mirror.zyner.org/mirror/cachyos/repo/
#  19. SpeedTestResult { speed: 273.7 KB/s; elapsed: 896ms; connection_time: 1.21s } -> https://mirror.nju.edu.cn/cachyos/repo/
#  20. SpeedTestResult { speed: 272.6 KB/s; elapsed: 885ms; connection_time: 589ms } -> https://cdn.cachyos.org/repo/
#  21. SpeedTestResult { speed: 231.2 KB/s; elapsed: 991ms; connection_time: 1.23s } -> https://mirrors.ustc.edu.cn/cachyos/repo/
#  22. SpeedTestResult { speed: 202.0 KB/s; elapsed: 950ms; connection_time: 731ms } -> https://archlinux.gay/cachy/repo/
#  23. SpeedTestResult { speed: 156.2 KB/s; elapsed: 837ms; connection_time: 1.44s } -> https://mirror.limda.net/cachy/repo/
#  24. SpeedTestResult { speed: 150.1 KB/s; elapsed: 959ms; connection_time: 1.51s } -> https://mirror.meowsmp.net/cachyOS/repo/
# FINISHED AT: 2026-03-01 15:22:42.491985366 -07:00
Server = https://us.cachyos.org/repo/$arch/$repo
Server = https://ca.mirror.cx/cachyos/repo/$arch/$repo
Server = https://mirror.allthingslinux.org/cachyos/repo/$arch/$repo
Server = https://cachyos.doridian.net/repo/$arch/$repo
Server = https://mirror.cachyos.org/repo/$arch/$repo
Server = https://us-mnz.soulharsh007.dev/cachyos/repo/$arch/$repo
Server = https://at.cachyos.org/repo/$arch/$repo
Server = https://de-nue.soulharsh007.dev/cachyos/repo/$arch/$repo
Server = https://cdn.lansing2600.org/cachyos/repo/$arch/$repo
Server = https://cdn77.cachyos.org/repo/$arch/$repo
Server = https://mirror.jura12.ru/repo/$arch/$repo
Server = https://mirror.lesviallon.fr/cachy/repo/$arch/$repo
Server = https://mirror.hb9hil.org/cachyos/repo/$arch/$repo
Server = https://nl.mirror.cx/cachyos/repo/$arch/$repo
Server = https://no.mirror.cx/cachyos/repo/$arch/$repo
Server = https://mirrors.lansing2600.org/cachyos/repo/$arch/$repo
Server = https://cachyos.next-works.it/repo/$arch/$repo
Server = https://mirror.zyner.org/mirror/cachyos/repo/$arch/$repo
Server = https://mirror.nju.edu.cn/cachyos/repo/$arch/$repo
Server = https://cdn.cachyos.org/repo/$arch/$repo
Server = https://mirrors.ustc.edu.cn/cachyos/repo/$arch/$repo
Server = https://archlinux.gay/cachy/repo/$arch/$repo
Server = https://mirror.limda.net/cachy/repo/$arch/$repo
Server = https://mirror.meowsmp.net/cachyOS/repo/$arch/$repo
==> Done [cachyos] /etc/pacman.d/cachyos-mirrorlist
# STARTED AT: 2026-03-01 15:22:32.177893597 -07:00
# VERSION: 0.27.0
# ARGS: rate-mirrors --save /tmp/tmp.oVENCYT6oG cachyos
# MIRRORS LEFT AFTER FILTERING: 24
# JUMP #1
# EXPLORING US
# VISITED US
# BLANK ITERATION
# COUNTRY JUMPING YIELDED TOO FEW MIRRORS (0), ADDING OTHERS TO UNLABELED
#

# TESTING UNLABELED MIRRORS
# SpeedTestResult { speed: 6.4 MB/s; elapsed: 920ms; connection_time: 278ms }
# SpeedTestResult { speed: 866.8 KB/s; elapsed: 987ms; connection_time: 224ms }
# SpeedTestResult { speed: 418.7 KB/s; elapsed: 934ms; connection_time: 277ms }
# SpeedTestResult { speed: 655.1 KB/s; elapsed: 931ms; connection_time: 280ms }
# SpeedTestResult { speed: 1.3 MB/s; elapsed: 965ms; connection_time: 328ms }
# SpeedTestResult { speed: 1.1 MB/s; elapsed: 979ms; connection_time: 404ms }
# SpeedTestResult { speed: 586.1 KB/s; elapsed: 977ms; connection_time: 508ms }
# SpeedTestResult { speed: 894.0 KB/s; elapsed: 952ms; connection_time: 525ms }
# SpeedTestResult { speed: 396.9 KB/s; elapsed: 990ms; connection_time: 563ms }
# SpeedTestResult { speed: 272.6 KB/s; elapsed: 885ms; connection_time: 589ms }
# SpeedTestResult { speed: 523.8 KB/s; elapsed: 1.00s; connection_time: 716ms }
# SpeedTestResult { speed: 444.9 KB/s; elapsed: 992ms; connection_time: 739ms }
# SpeedTestResult { speed: 202.0 KB/s; elapsed: 950ms; connection_time: 731ms }
# SpeedTestResult { speed: 516.9 KB/s; elapsed: 982ms; connection_time: 730ms }
# SpeedTestResult { speed: 903.3 KB/s; elapsed: 981ms; connection_time: 727ms }
# SpeedTestResult { speed: 686.7 KB/s; elapsed: 978ms; connection_time: 732ms }
# SpeedTestResult { speed: 796.8 KB/s; elapsed: 945ms; connection_time: 776ms }
# SpeedTestResult { speed: 540.2 KB/s; elapsed: 962ms; connection_time: 814ms }
# SpeedTestResult { speed: 575.0 KB/s; elapsed: 997ms; connection_time: 950ms }
# SpeedTestResult { speed: 284.0 KB/s; elapsed: 993ms; connection_time: 1.02s }
# SpeedTestResult { speed: 273.7 KB/s; elapsed: 896ms; connection_time: 1.21s }
# SpeedTestResult { speed: 231.2 KB/s; elapsed: 991ms; connection_time: 1.23s }
# SpeedTestResult { speed: 156.2 KB/s; elapsed: 837ms; connection_time: 1.44s }
# SpeedTestResult { speed: 150.1 KB/s; elapsed: 959ms; connection_time: 1.51s }
#

# RE-TESTING TOP MIRRORS
# SpeedTestResult { speed: 6.4 MB/s; elapsed: 641ms; connection_time: 193ms }
# SpeedTestResult { speed: 2.8 MB/s; elapsed: 991ms; connection_time: 393ms }
# SpeedTestResult { speed: 1.8 MB/s; elapsed: 990ms; connection_time: 448ms }
# SpeedTestResult { speed: 1.1 MB/s; elapsed: 984ms; connection_time: 885ms }
# SpeedTestResult { speed: 2.6 MB/s; elapsed: 954ms; connection_time: 415ms }
# ==== RESULTS (top re-tested) ====
#   1. SpeedTestResult { speed: 6.4 MB/s; elapsed: 641ms; connection_time: 193ms } -> https://us.cachyos.org/repo/
#   2. SpeedTestResult { speed: 2.8 MB/s; elapsed: 991ms; connection_time: 393ms } -> https://ca.mirror.cx/cachyos/repo/
#   3. SpeedTestResult { speed: 2.6 MB/s; elapsed: 954ms; connection_time: 415ms } -> https://mirror.allthingslinux.org/cachyos/repo/
#   4. SpeedTestResult { speed: 1.8 MB/s; elapsed: 990ms; connection_time: 448ms } -> https://cachyos.doridian.net/repo/
#   5. SpeedTestResult { speed: 1.1 MB/s; elapsed: 984ms; connection_time: 885ms } -> https://mirror.cachyos.org/repo/
#   6. SpeedTestResult { speed: 866.8 KB/s; elapsed: 987ms; connection_time: 224ms } -> https://us-mnz.soulharsh007.dev/cachyos/repo/
#   7. SpeedTestResult { speed: 796.8 KB/s; elapsed: 945ms; connection_time: 776ms } -> https://at.cachyos.org/repo/
#   8. SpeedTestResult { speed: 686.7 KB/s; elapsed: 978ms; connection_time: 732ms } -> https://de-nue.soulharsh007.dev/cachyos/repo/
#   9. SpeedTestResult { speed: 655.1 KB/s; elapsed: 931ms; connection_time: 280ms } -> https://cdn.lansing2600.org/cachyos/repo/
#  10. SpeedTestResult { speed: 586.1 KB/s; elapsed: 977ms; connection_time: 508ms } -> https://cdn77.cachyos.org/repo/
#  11. SpeedTestResult { speed: 575.0 KB/s; elapsed: 997ms; connection_time: 950ms } -> https://mirror.jura12.ru/repo/
#  12. SpeedTestResult { speed: 540.2 KB/s; elapsed: 962ms; connection_time: 814ms } -> https://mirror.lesviallon.fr/cachy/repo/
#  13. SpeedTestResult { speed: 523.8 KB/s; elapsed: 1.00s; connection_time: 716ms } -> https://mirror.hb9hil.org/cachyos/repo/
#  14. SpeedTestResult { speed: 516.9 KB/s; elapsed: 982ms; connection_time: 730ms } -> https://nl.mirror.cx/cachyos/repo/
#  15. SpeedTestResult { speed: 444.9 KB/s; elapsed: 992ms; connection_time: 739ms } -> https://no.mirror.cx/cachyos/repo/
#  16. SpeedTestResult { speed: 418.7 KB/s; elapsed: 934ms; connection_time: 277ms } -> https://mirrors.lansing2600.org/cachyos/repo/
#  17. SpeedTestResult { speed: 396.9 KB/s; elapsed: 990ms; connection_time: 563ms } -> https://cachyos.next-works.it/repo/
#  18. SpeedTestResult { speed: 284.0 KB/s; elapsed: 993ms; connection_time: 1.02s } -> https://mirror.zyner.org/mirror/cachyos/repo/
#  19. SpeedTestResult { speed: 273.7 KB/s; elapsed: 896ms; connection_time: 1.21s } -> https://mirror.nju.edu.cn/cachyos/repo/
#  20. SpeedTestResult { speed: 272.6 KB/s; elapsed: 885ms; connection_time: 589ms } -> https://cdn.cachyos.org/repo/
#  21. SpeedTestResult { speed: 231.2 KB/s; elapsed: 991ms; connection_time: 1.23s } -> https://mirrors.ustc.edu.cn/cachyos/repo/
#  22. SpeedTestResult { speed: 202.0 KB/s; elapsed: 950ms; connection_time: 731ms } -> https://archlinux.gay/cachy/repo/
#  23. SpeedTestResult { speed: 156.2 KB/s; elapsed: 837ms; connection_time: 1.44s } -> https://mirror.limda.net/cachy/repo/
#  24. SpeedTestResult { speed: 150.1 KB/s; elapsed: 959ms; connection_time: 1.51s } -> https://mirror.meowsmp.net/cachyOS/repo/
# FINISHED AT: 2026-03-01 15:22:42.491985366 -07:00
Server = https://us.cachyos.org/repo/$arch/$repo
Server = https://ca.mirror.cx/cachyos/repo/$arch/$repo
Server = https://mirror.allthingslinux.org/cachyos/repo/$arch/$repo
Server = https://cachyos.doridian.net/repo/$arch/$repo
Server = https://mirror.cachyos.org/repo/$arch/$repo
Server = https://us-mnz.soulharsh007.dev/cachyos/repo/$arch/$repo
Server = https://at.cachyos.org/repo/$arch/$repo
Server = https://de-nue.soulharsh007.dev/cachyos/repo/$arch/$repo
Server = https://cdn.lansing2600.org/cachyos/repo/$arch/$repo
Server = https://cdn77.cachyos.org/repo/$arch/$repo
Server = https://mirror.jura12.ru/repo/$arch/$repo
Server = https://mirror.lesviallon.fr/cachy/repo/$arch/$repo
Server = https://mirror.hb9hil.org/cachyos/repo/$arch/$repo
Server = https://nl.mirror.cx/cachyos/repo/$arch/$repo
Server = https://no.mirror.cx/cachyos/repo/$arch/$repo
Server = https://mirrors.lansing2600.org/cachyos/repo/$arch/$repo
Server = https://cachyos.next-works.it/repo/$arch/$repo
Server = https://mirror.zyner.org/mirror/cachyos/repo/$arch/$repo
Server = https://mirror.nju.edu.cn/cachyos/repo/$arch/$repo
Server = https://cdn.cachyos.org/repo/$arch/$repo
Server = https://mirrors.ustc.edu.cn/cachyos/repo/$arch/$repo
Server = https://archlinux.gay/cachy/repo/$arch/$repo
Server = https://mirror.limda.net/cachy/repo/$arch/$repo
Server = https://mirror.meowsmp.net/cachyOS/repo/$arch/$repo
:: Building Arch Servers List:
[ pass ] https://berlin.mirror.pkgbuild.com
[ pass ] https://fr.mirrors.cicku.me/archlinux
[ pass ] https://arch.jsc.mx
[ pass ] https://mirror.moson.org/arch
[ pass ] https://ru.mirrors.cicku.me/archlinux
[ pass ] https://nl.mirrors.cicku.me/archlinux
[ pass ] https://es.mirrors.cicku.me/archlinux
[ pass ] https://kr.mirrors.cicku.me/archlinux
[ pass ] https://mirror.rackspace.com/archlinux
[ pass ] https://fastly.mirror.pkgbuild.com
[ pass ] https://gluttony.sin.cvut.cz/arch
[ pass ] https://ro.arch.niranjan.co
[ pass ] https://lidsol.fi-b.unam.mx/archlinux
[ pass ] https://mirror.osbeck.com/archlinux
[ pass ] https://br.mirrors.cicku.me/archlinux
[ pass ] https://geo.mirror.pkgbuild.com
[ pass ] https://mirror.quantum5.ca/archlinux
[ pass ] https://it.mirrors.cicku.me/archlinux
[ pass ] https://mirrors.dotsrc.org/archlinux
[ fail ] https://mirror.1ago.be/archlinux
[ fail ] https://mirrors.uni-plovdiv.net/archlinux
[ fail ] https://arch.mirror.hyperbit.it
[ fail ] https://mirror.gi.co.id/archlinux
[ fail ] https://repo.hyron.dev/archlinux
[ fail ] https://at.arch.niranjan.co
[ fail ] https://ftp.sh.cvut.cz/arch
[ fail ] https://ch.mirrors.cicku.me/archlinux
[ fail ] https://singapore.mirror.pkgbuild.com
[ fail ] https://london.mirror.pkgbuild.com
[ pass ] https://mirrors.xtom.ee/archlinux
[ fail ] http://arch.softver.org.mk/archlinux
[ fail ] https://glua.ua.pt/pub/archlinux
[ fail ] https://mirror.cyberbits.eu/archlinux
[ fail ] https://arch.midov.pl/arch
[ pass ] https://mirror.tux.si/arch
[ fail ] https://us.arch.niranjan.co
[ pass ] https://archlinux.koyanet.lv/archlinux
[ pass ] https://mirror.sahil.world/archlinux
[ pass ] https://mirrors.jlu.edu.cn/archlinux
[ pass ] https://mirrors.ustc.edu.cn/archlinux
[ fail ] https://sg.arch.niranjan.co
[ pass ] https://mirror.scd31.com/arch
[ pass ] https://mirror.raiolanetworks.com/archlinux
[ pass ] https://mirrors.pidginhost.com/arch
[ pass ] https://arch.mirror.constant.com
[ fail ] https://mirror.neuf.no/archlinux
[ pass ] https://edgeuno-bog2.mm.fcix.net/archlinux
[ fail ] http://elmirror.cl/archlinux
[ fail ] https://ftp.energotel.sk/pub/linux/arch
[ fail ] https://archlinux.uk.mirror.allworldit.com/archlinux
[ pass ] http://tux.rainside.sk/archlinux
[ pass ] https://at.arch.mirror.kescher.at
[ pass ] https://mirror.ufscar.br/archlinux
[ pass ] https://jp.mirrors.cicku.me/archlinux
[ pass ] https://archlinux.kushwanthreddy.com
[ pass ] https://mirror.group.one/archlinux
[ pass ] https://repository.su/archlinux
[ fail ] https://mirror.ditatompel.com/archlinux
[ fail ] http://mirrors.udenar.edu.co/archlinux
[ pass ] https://mirror.trapmaid.org/archlinux
[ pass ] https://no.mirror.cx/archlinux
[ fail ] http://archlinux.iskon.hr
[ fail ] https://mirror.anquan.cl/archlinux
[ fail ] https://umea.mirror.pkgbuild.com
[ fail ] https://mirror.barata.pt/archlinux
[ fail ] https://ftp.linux.edu.lv/archlinux
[ fail ] https://mirror.juniorjpdj.pl/archlinux
[ fail ] https://mirrors.teamcloud.am/archlinux
[ pass ] https://mirror.abderraziq.com/archlinux
[ fail ] https://mirror2.keiminem.com/archlinux
[ fail ] https://mirrors.atviras.lt/archlinux
[ fail ] https://mirror.twds.com.tw/archlinux
[ fail ] https://mirror.tiguinet.net/arch
[ pass ] https://fi.arch.niranjan.co
[ fail ] https://ftpmirror.infania.net/mirror/archlinux
[ fail ] https://mirror.sinirlan.net/archlinux
[ fail ] https://theswissbay.ch/archlinux
[ fail ] https://mirror.t-home.mk/archlinux
[ fail ] https://mirror.telepoint.bg/archlinux
[ fail ] https://www.miraa.jp/archlinux
[ fail ] https://mirror.archlinux.si
[ fail ] https://arch.yhtez.xyz
[ fail ] https://taipei.mirror.pkgbuild.com
[   4.2MiB/s ] https://arch.jsc.mx
[   6.5MiB/s ] https://nl.mirrors.cicku.me/archlinux
[   2.9MiB/s ] https://mirror.rackspace.com/archlinux
[   3.2MiB/s ] https://lidsol.fi-b.unam.mx/archlinux
[   4.2MiB/s ] https://br.mirrors.cicku.me/archlinux
[   5.1MiB/s ] https://it.mirrors.cicku.me/archlinux
[   2.2MiB/s ] https://ru.mirrors.cicku.me/archlinux
[   4.3MiB/s ] https://es.mirrors.cicku.me/archlinux
[   1.4MiB/s ] https://fastly.mirror.pkgbuild.com
[   7.4MiB/s ] https://mirror.osbeck.com/archlinux
[   3.9MiB/s ] https://mirror.quantum5.ca/archlinux
[   1.3MiB/s ] https://mirrors.dotsrc.org/archlinux
[   0.7MiB/s ] https://mirror.moson.org/arch
[   2.9MiB/s ] https://kr.mirrors.cicku.me/archlinux
[   0.3MiB/s ] https://mirror.scd31.com/arch
[   1.8MiB/s ] https://arch.mirror.constant.com
[   1.9MiB/s ] https://at.arch.mirror.kescher.at
[   2.8MiB/s ] https://jp.mirrors.cicku.me/archlinux
[   2.9MiB/s ] https://fr.mirrors.cicku.me/archlinux
[      error ] https://arch.midov.pl/arch
[      error ] https://us.arch.niranjan.co
[   0.7MiB/s ] https://mirror.ufscar.br/archlinux
[      error ] https://mirror.anquan.cl/archlinux
[      error ] https://mirror.barata.pt/archlinux
[   0.4MiB/s ] https://geo.mirror.pkgbuild.com
[   1.1MiB/s ] https://mirrors.xtom.ee/archlinux
[   1.6MiB/s ] https://archlinux.koyanet.lv/archlinux
[   0.8MiB/s ] https://mirrors.pidginhost.com/arch
[      error ] https://mirrors.atviras.lt/archlinux
[      error ] https://mirror.twds.com.tw/archlinux
[   0.8MiB/s ] https://ro.arch.niranjan.co
[      error ] http://elmirror.cl/archlinux
[      error ] https://archlinux.uk.mirror.allworldit.com/archlinux
[      error ] https://mirror.sinirlan.net/archlinux
[      error ] https://theswissbay.ch/archlinux
[      error ] https://taipei.mirror.pkgbuild.com
[      error ] https://arch.mirror.hyperbit.it
[      error ] https://ch.mirrors.cicku.me/archlinux
[   0.9MiB/s ] https://mirrors.jlu.edu.cn/archlinux
[   1.4MiB/s ] https://mirror.raiolanetworks.com/archlinux
[   0.7MiB/s ] http://tux.rainside.sk/archlinux
[   0.9MiB/s ] https://mirror.group.one/archlinux
[      error ] https://mirrors.uni-plovdiv.net/archlinux
[      error ] https://singapore.mirror.pkgbuild.com
[      error ] https://mirror.ditatompel.com/archlinux
[      error ] http://mirrors.udenar.edu.co/archlinux
[   2.6MiB/s ] https://mirror.trapmaid.org/archlinux
[      error ] https://www.miraa.jp/archlinux
[   0.9MiB/s ] https://gluttony.sin.cvut.cz/arch
[      error ] https://mirror.neuf.no/archlinux
[      error ] https://ftp.energotel.sk/pub/linux/arch
[   0.8MiB/s ] https://repository.su/archlinux
[   0.8MiB/s ] https://no.mirror.cx/archlinux
[   0.7MiB/s ] https://fi.arch.niranjan.co
[   0.7MiB/s ] https://berlin.mirror.pkgbuild.com
[      error ] https://glua.ua.pt/pub/archlinux
[      error ] https://mirror.cyberbits.eu/archlinux
[   1.4MiB/s ] https://edgeuno-bog2.mm.fcix.net/archlinux
[      error ] https://mirror.tiguinet.net/arch
[      error ] https://ftpmirror.infania.net/mirror/archlinux
[      error ] https://mirror.1ago.be/archlinux
[      error ] https://repo.hyron.dev/archlinux
[   0.8MiB/s ] https://mirror.tux.si/arch
[      error ] http://archlinux.iskon.hr
[      error ] https://mirror.juniorjpdj.pl/archlinux
[   0.8MiB/s ] https://mirror.abderraziq.com/archlinux
[      error ] https://at.arch.niranjan.co
[      error ] https://london.mirror.pkgbuild.com
[   3.1MiB/s ] https://mirror.sahil.world/archlinux
[      error ] https://umea.mirror.pkgbuild.com
[      error ] https://ftp.linux.edu.lv/archlinux
[      error ] https://mirror.archlinux.si
[      error ] https://mirror.gi.co.id/archlinux
[      error ] http://arch.softver.org.mk/archlinux
[   1.5MiB/s ] https://mirrors.ustc.edu.cn/archlinux
[      error ] https://mirrors.teamcloud.am/archlinux
[      error ] https://mirror2.keiminem.com/archlinux
[      error ] https://arch.yhtez.xyz
[      error ] https://ftp.sh.cvut.cz/arch
[      error ] https://sg.arch.niranjan.co
[   0.7MiB/s ] https://archlinux.kushwanthreddy.com
[      error ] https://mirror.t-home.mk/archlinux
[      error ] https://mirror.telepoint.bg/archlinux
┌───────────────┬────────────────────────────────────────────────────┬─────┬─────────┬─────────┬─────────┬──────────┬────────────┬─────────┬─────────┐
│    country    │                       mirror                       │proxy│  state  │outofdate│uptodate │morerecent│   speed    │  ping   │estimated│
├───────────────┼────────────────────────────────────────────────────┼─────┼─────────┼─────────┼─────────┼──────────┼────────────┼─────────┼─────────┤
│Sweden         │https://mirror.osbeck.com/archlinux                 │false│success  │   0.00% │  99.99% │   0.01%  │   7.4MiB/s │  38.6ms │   1gg   │
│Canada         │https://mirror.quantum5.ca/archlinux                │false│success  │   0.00% │  99.99% │   0.01%  │   3.9MiB/s │  54.9ms │   1gg   │
│India          │https://mirror.sahil.world/archlinux                │false│success  │   0.00% │  99.99% │   0.01%  │   3.1MiB/s │  33.7ms │   1gg   │
│Ukraine        │https://mirror.trapmaid.org/archlinux               │false│success  │   0.00% │  99.99% │   0.01%  │   2.6MiB/s │ 175.5ms │   1gg   │
│Austria        │https://at.arch.mirror.kescher.at                   │false│success  │   0.00% │  99.99% │   0.01%  │   1.9MiB/s │ 201.3ms │   1gg   │
│United States  │https://arch.mirror.constant.com                    │false│success  │   0.00% │  99.99% │   0.01%  │   1.8MiB/s │ 169.6ms │   1gg   │
│China          │https://mirrors.ustc.edu.cn/archlinux               │false│success  │   0.00% │  99.99% │   0.01%  │   1.5MiB/s │ 277.1ms │   1gg   │
│Worldwide      │https://fastly.mirror.pkgbuild.com                  │false│success  │   0.00% │  99.99% │   0.01%  │   1.4MiB/s │  26.8ms │   1gg   │
│Spain          │https://mirror.raiolanetworks.com/archlinux         │false│success  │   0.00% │  99.99% │   0.01%  │   1.4MiB/s │ 193.8ms │   1gg   │
│Romania        │https://ro.arch.niranjan.co                         │false│success  │   0.00% │  99.99% │   0.01%  │   0.8MiB/s │ 181.4ms │   1gg   │
│Germany        │https://berlin.mirror.pkgbuild.com                  │false│success  │   0.00% │  99.99% │   0.01%  │   0.7MiB/s │ 229.8ms │   1gg   │
│Finland        │https://fi.arch.niranjan.co                         │false│success  │   0.00% │  99.99% │   0.01%  │   0.7MiB/s │ 163.6ms │   1gg   │
│Germany        │https://mirror.moson.org/arch                       │false│success  │   0.00% │  99.99% │   0.01%  │   0.7MiB/s │ 230.5ms │   1gg   │
│Brazil         │https://mirror.ufscar.br/archlinux                  │false│success  │   0.00% │  99.99% │   0.01%  │   0.7MiB/s │ 187.2ms │   1gg   │
│Worldwide      │https://geo.mirror.pkgbuild.com                     │false│success  │   0.00% │  99.99% │   0.01%  │   0.4MiB/s │ 158.2ms │   1gg   │
│Russia         │https://ru.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   2.2MiB/s │  94.1ms │  10gg   │
│Denmark        │https://mirrors.dotsrc.org/archlinux                │false│success  │   0.00% │ 100.00% │   0.00%  │   1.3MiB/s │ 185.0ms │  10gg   │
│Denmark        │https://mirror.group.one/archlinux                  │false│success  │   0.00% │ 100.00% │   0.00%  │   0.9MiB/s │ 188.4ms │  10gg   │
│China          │https://mirrors.jlu.edu.cn/archlinux                │false│success  │   0.00% │ 100.00% │   0.00%  │   0.9MiB/s │ 289.0ms │  10gg   │
│Romania        │https://mirrors.pidginhost.com/arch                 │false│success  │   0.00% │ 100.00% │   0.00%  │   0.8MiB/s │ 184.6ms │  10gg   │
│Norway         │https://no.mirror.cx/archlinux                      │false│success  │   0.00% │ 100.00% │   0.00%  │   0.8MiB/s │ 161.9ms │  10gg   │
│Russia         │https://repository.su/archlinux                     │false│success  │   0.00% │ 100.00% │   0.00%  │   0.8MiB/s │ 187.5ms │  10gg   │
│Slovenia       │https://mirror.tux.si/arch                          │false│success  │   0.00% │ 100.00% │   0.00%  │   0.8MiB/s │ 227.4ms │  10gg   │
│Morocco        │https://mirror.abderraziq.com/archlinux             │false│success  │   0.00% │ 100.00% │   0.00%  │   0.8MiB/s │ 247.0ms │  10gg   │
│India          │https://archlinux.kushwanthreddy.com                │false│success  │   0.00% │ 100.00% │   0.00%  │   0.7MiB/s │ 303.6ms │  10gg   │
│Canada         │https://mirror.scd31.com/arch                       │false│success  │   0.00% │ 100.00% │   0.00%  │   0.3MiB/s │  error  │  10gg   │
│France         │https://fr.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   2.9MiB/s │  99.2ms │   9gg   │
│South Korea    │https://kr.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   2.9MiB/s │ 193.5ms │   9gg   │
│Japan          │https://jp.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   2.8MiB/s │  31.1ms │   9gg   │
│Spain          │https://es.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   4.3MiB/s │  38.5ms │   7gg   │
│Mexico         │https://arch.jsc.mx                                 │false│success  │   0.00% │ 100.00% │   0.00%  │   4.2MiB/s │  94.2ms │   7gg   │
│Italy          │https://it.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   5.1MiB/s │  87.2ms │   6gg   │
│Netherlands    │https://nl.mirrors.cicku.me/archlinux               │false│success  │   0.00% │ 100.00% │   0.00%  │   6.5MiB/s │  25.1ms │   4gg   │
│Latvia         │https://archlinux.koyanet.lv/archlinux              │false│success  │   0.01% │  99.99% │   0.00%  │   1.6MiB/s │ 210.2ms │   4gg   │
│Colombia       │https://edgeuno-bog2.mm.fcix.net/archlinux          │false│success  │   0.01% │  99.99% │   0.00%  │   1.4MiB/s │ 141.3ms │   4gg   │
│Estonia        │https://mirrors.xtom.ee/archlinux                   │false│success  │   0.01% │  99.99% │   0.00%  │   1.1MiB/s │ 199.1ms │   4gg   │
│Czechia        │https://gluttony.sin.cvut.cz/arch                   │false│success  │   0.01% │  99.99% │   0.00%  │   0.9MiB/s │ 231.2ms │   4gg   │
│Brazil         │https://br.mirrors.cicku.me/archlinux               │false│success  │   0.01% │  99.99% │   0.00%  │   4.2MiB/s │  46.8ms │   3gg   │
│Mexico         │https://lidsol.fi-b.unam.mx/archlinux               │false│success  │   0.09% │  99.91% │   0.00%  │   3.2MiB/s │  85.0ms │   1gg   │
│Slovakia       │http://tux.rainside.sk/archlinux                    │false│success  │   0.14% │  99.86% │   0.00%  │   0.7MiB/s │ 170.5ms │   1gg   │
│Worldwide      │https://mirror.rackspace.com/archlinux              │false│success  │   0.19% │  99.81% │   0.00%  │   2.9MiB/s │  96.6ms │   1gg   │
│Poland         │https://arch.midov.pl/arch                          │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 191.1ms │   1gg   │
│United States  │https://us.arch.niranjan.co                         │false│error    │   err   │   err   │   err    │   0.0MiB/s │  60.4ms │   1gg   │
│Chile          │https://mirror.anquan.cl/archlinux                  │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 204.9ms │   1gg   │
│Portugal       │https://mirror.barata.pt/archlinux                  │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 167.7ms │   1gg   │
│Lithuania      │https://mirrors.atviras.lt/archlinux                │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 196.8ms │   1gg   │
│Taiwan         │https://mirror.twds.com.tw/archlinux                │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 176.2ms │   1gg   │
│Chile          │http://elmirror.cl/archlinux                        │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 225.0ms │   1gg   │
│United Kingdom │https://archlinux.uk.mirror.allworldit.com/archlinux│false│error    │   err   │   err   │   err    │   0.0MiB/s │ 180.4ms │   1gg   │
│Lithuania      │https://mirror.sinirlan.net/archlinux               │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │  error  │   1gg   │
│Switzerland    │https://theswissbay.ch/archlinux                    │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 157.0ms │   1gg   │
│Taiwan         │https://taipei.mirror.pkgbuild.com                  │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 226.6ms │   1gg   │
│Italy          │https://arch.mirror.hyperbit.it                     │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 218.2ms │   1gg   │
│Switzerland    │https://ch.mirrors.cicku.me/archlinux               │false│error    │   err   │   err   │   err    │   0.0MiB/s │  57.8ms │   1gg   │
│Bulgaria       │https://mirrors.uni-plovdiv.net/archlinux           │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 231.5ms │   1gg   │
│Singapore      │https://singapore.mirror.pkgbuild.com               │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 283.0ms │   1gg   │
│Indonesia      │https://mirror.ditatompel.com/archlinux             │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 233.1ms │   1gg   │
│Colombia       │http://mirrors.udenar.edu.co/archlinux              │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 132.5ms │   1gg   │
│Japan          │https://www.miraa.jp/archlinux                      │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 156.6ms │   1gg   │
│Norway         │https://mirror.neuf.no/archlinux                    │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 174.2ms │   1gg   │
│Slovakia       │https://ftp.energotel.sk/pub/linux/arch             │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 202.0ms │   1gg   │
│Portugal       │https://glua.ua.pt/pub/archlinux                    │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 222.9ms │   1gg   │
│France         │https://mirror.cyberbits.eu/archlinux               │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 148.0ms │   1gg   │
│Belgium        │https://mirror.tiguinet.net/arch                    │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 175.1ms │   1gg   │
│Worldwide      │https://ftpmirror.infania.net/mirror/archlinux      │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 154.5ms │   1gg   │
│Belgium        │https://mirror.1ago.be/archlinux                    │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 232.7ms │   1gg   │
│Ukraine        │https://repo.hyron.dev/archlinux                    │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 173.3ms │   1gg   │
│Croatia        │http://archlinux.iskon.hr                           │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 289.0ms │   1gg   │
│Poland         │https://mirror.juniorjpdj.pl/archlinux              │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 188.1ms │   1gg   │
│Austria        │https://at.arch.niranjan.co                         │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 219.2ms │   1gg   │
│United Kingdom │https://london.mirror.pkgbuild.com                  │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 215.1ms │   1gg   │
│Sweden         │https://umea.mirror.pkgbuild.com                    │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 196.2ms │   1gg   │
│Latvia         │https://ftp.linux.edu.lv/archlinux                  │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 173.4ms │   1gg   │
│Slovenia       │https://mirror.archlinux.si                         │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 236.2ms │   1gg   │
│Indonesia      │https://mirror.gi.co.id/archlinux                   │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 266.1ms │   1gg   │
│North Macedonia│http://arch.softver.org.mk/archlinux                │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 218.7ms │   1gg   │
│Armenia        │https://mirrors.teamcloud.am/archlinux              │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 282.5ms │   1gg   │
│South Korea    │https://mirror2.keiminem.com/archlinux              │false│error    │   err   │   err   │   err    │   0.0MiB/s │  error  │   1gg   │
│Finland        │https://arch.yhtez.xyz                              │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 165.7ms │   1gg   │
│Czechia        │https://ftp.sh.cvut.cz/arch                         │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 218.3ms │   1gg   │
│Singapore      │https://sg.arch.niranjan.co                         │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 278.6ms │   1gg   │
│North Macedonia│https://mirror.t-home.mk/archlinux                  │false│error    │   0.00% │   0.00% │   0.00%  │   0.0MiB/s │ 203.7ms │   1gg   │
│Bulgaria       │https://mirror.telepoint.bg/archlinux               │false│error    │   err   │   err   │   err    │   0.0MiB/s │ 271.1ms │   1gg   │
└───────────────┴────────────────────────────────────────────────────┴─────┴─────────┴─────────┴─────────┴──────────┴────────────┴─────────┴─────────┘
# lastsync<dd.mm.yyyy> 01.03.2026
## Sweden
Server=https://mirror.osbeck.com/archlinux/$repo/os/$arch
## Canada
Server=https://mirror.quantum5.ca/archlinux/$repo/os/$arch
## India
Server=https://mirror.sahil.world/archlinux/$repo/os/$arch
## Ukraine
Server=https://mirror.trapmaid.org/archlinux/$repo/os/$arch
## Austria
Server=https://at.arch.mirror.kescher.at/$repo/os/$arch
## United States
Server=https://arch.mirror.constant.com/$repo/os/$arch
## China
Server=https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
## Worldwide
Server=https://fastly.mirror.pkgbuild.com/$repo/os/$arch
## Spain
Server=https://mirror.raiolanetworks.com/archlinux/$repo/os/$arch
## Romania
Server=https://ro.arch.niranjan.co/$repo/os/$arch
## Germany
Server=https://berlin.mirror.pkgbuild.com/$repo/os/$arch
## Finland
Server=https://fi.arch.niranjan.co/$repo/os/$arch
## Germany
Server=https://mirror.moson.org/arch/$repo/os/$arch
## Brazil
Server=https://mirror.ufscar.br/archlinux/$repo/os/$arch
## Worldwide
Server=https://geo.mirror.pkgbuild.com/$repo/os/$arch
## Russia
Server=https://ru.mirrors.cicku.me/archlinux/$repo/os/$arch
## Denmark
Server=https://mirrors.dotsrc.org/archlinux/$repo/os/$arch
Server=https://mirror.group.one/archlinux/$repo/os/$arch
## China
Server=https://mirrors.jlu.edu.cn/archlinux/$repo/os/$arch
## Romania
Server=https://mirrors.pidginhost.com/arch/$repo/os/$arch
## Norway
Server=https://no.mirror.cx/archlinux/$repo/os/$arch
## Russia
Server=https://repository.su/archlinux/$repo/os/$arch
## Slovenia
Server=https://mirror.tux.si/arch/$repo/os/$arch
## Morocco
Server=https://mirror.abderraziq.com/archlinux/$repo/os/$arch
## India
Server=https://archlinux.kushwanthreddy.com/$repo/os/$arch
## Canada
Server=https://mirror.scd31.com/arch/$repo/os/$arch
## France
Server=https://fr.mirrors.cicku.me/archlinux/$repo/os/$arch
## South Korea
Server=https://kr.mirrors.cicku.me/archlinux/$repo/os/$arch
## Japan
Server=https://jp.mirrors.cicku.me/archlinux/$repo/os/$arch
## Spain
Server=https://es.mirrors.cicku.me/archlinux/$repo/os/$arch
## Mexico
Server=https://arch.jsc.mx/$repo/os/$arch
## Italy
Server=https://it.mirrors.cicku.me/archlinux/$repo/os/$arch
## Netherlands
Server=https://nl.mirrors.cicku.me/archlinux/$repo/os/$arch
## Latvia
Server=https://archlinux.koyanet.lv/archlinux/$repo/os/$arch
## Colombia
Server=https://edgeuno-bog2.mm.fcix.net/archlinux/$repo/os/$arch
## Estonia
Server=https://mirrors.xtom.ee/archlinux/$repo/os/$arch
## Czechia
Server=https://gluttony.sin.cvut.cz/arch/$repo/os/$arch
## Brazil
Server=https://br.mirrors.cicku.me/archlinux/$repo/os/$arch
## Mexico
Server=https://lidsol.fi-b.unam.mx/archlinux/$repo/os/$arch
## Slovakia
Server=http://tux.rainside.sk/archlinux/$repo/os/$arch
## Worldwide
Server=https://mirror.rackspace.com/archlinux/$repo/os/$arch
## Poland
Server=https://arch.midov.pl/arch/$repo/os/$arch
## United States
Server=https://us.arch.niranjan.co/$repo/os/$arch
## Chile
Server=https://mirror.anquan.cl/archlinux/$repo/os/$arch
## Portugal
Server=https://mirror.barata.pt/archlinux/$repo/os/$arch
## Lithuania
Server=https://mirrors.atviras.lt/archlinux/$repo/os/$arch
## Taiwan
Server=https://mirror.twds.com.tw/archlinux/$repo/os/$arch
## Chile
Server=http://elmirror.cl/archlinux/$repo/os/$arch
## United Kingdom
Server=https://archlinux.uk.mirror.allworldit.com/archlinux/$repo/os/$arch
## Lithuania
Server=https://mirror.sinirlan.net/archlinux/$repo/os/$arch
## Switzerland
Server=https://theswissbay.ch/archlinux/$repo/os/$arch
## Taiwan
Server=https://taipei.mirror.pkgbuild.com/$repo/os/$arch
## Italy
Server=https://arch.mirror.hyperbit.it/$repo/os/$arch
## Switzerland
Server=https://ch.mirrors.cicku.me/archlinux/$repo/os/$arch
## Bulgaria
Server=https://mirrors.uni-plovdiv.net/archlinux/$repo/os/$arch
## Singapore
Server=https://singapore.mirror.pkgbuild.com/$repo/os/$arch
## Indonesia
Server=https://mirror.ditatompel.com/archlinux/$repo/os/$arch
## Colombia
Server=http://mirrors.udenar.edu.co/archlinux/$repo/os/$arch
## Japan
Server=https://www.miraa.jp/archlinux/$repo/os/$arch
## Norway
Server=https://mirror.neuf.no/archlinux/$repo/os/$arch
## Slovakia
Server=https://ftp.energotel.sk/pub/linux/arch/$repo/os/$arch
## Portugal
Server=https://glua.ua.pt/pub/archlinux/$repo/os/$arch
## France
Server=https://mirror.cyberbits.eu/archlinux/$repo/os/$arch
## Belgium
Server=https://mirror.tiguinet.net/arch/$repo/os/$arch
## Worldwide
Server=https://ftpmirror.infania.net/mirror/archlinux/$repo/os/$arch
## Belgium
Server=https://mirror.1ago.be/archlinux/$repo/os/$arch
## Ukraine
Server=https://repo.hyron.dev/archlinux/$repo/os/$arch
## Croatia
Server=http://archlinux.iskon.hr/$repo/os/$arch
## Poland
Server=https://mirror.juniorjpdj.pl/archlinux/$repo/os/$arch
## Austria
Server=https://at.arch.niranjan.co/$repo/os/$arch
## United Kingdom
Server=https://london.mirror.pkgbuild.com/$repo/os/$arch
## Sweden
Server=https://umea.mirror.pkgbuild.com/$repo/os/$arch
## Latvia
Server=https://ftp.linux.edu.lv/archlinux/$repo/os/$arch
## Slovenia
Server=https://mirror.archlinux.si/$repo/os/$arch
## Indonesia
Server=https://mirror.gi.co.id/archlinux/$repo/os/$arch
## North Macedonia
Server=http://arch.softver.org.mk/archlinux/$repo/os/$arch
## Armenia
Server=https://mirrors.teamcloud.am/archlinux/$repo/os/$arch
## South Korea
Server=https://mirror2.keiminem.com/archlinux/$repo/os/$arch
## Finland
Server=https://arch.yhtez.xyz/$repo/os/$arch
## Czechia
Server=https://ftp.sh.cvut.cz/arch/$repo/os/$arch
## Singapore
Server=https://sg.arch.niranjan.co/$repo/os/$arch
## North Macedonia
Server=https://mirror.t-home.mk/archlinux/$repo/os/$arch
## Bulgaria
Server=https://mirror.telepoint.bg/archlinux/$repo/os/$arch
```

here's the update script using ghostmirror working off the arch rate-mirror lists - i feel the rate-mirror or the forked cachyos-rate-mirrors perform pretty much the same.
```shell
#!/bin/bash

# Colors
bold_cyan="\e[1;36m"
bold_white="\e[1;37m"
reset="\e[0m"

echo -e "${bold_cyan}==> ${bold_white}Starting Update...${reset}"

echo -e "${bold_cyan}:: ${bold_white}Building CachyOS Servers List:${reset}"
sudo ~/forge/scripts/cachyos/cachyos-update
cat /etc/pacman.d/cachyos-mirrorlist

echo -e "${bold_cyan}:: ${bold_white}Building Arch Servers List:${reset}"
sudo ghostmirror -PmuolsS  /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist light state,outofdate,morerecent,estimated,speed
cat /etc/pacman.d/mirrorlist

echo -e "${bold_cyan}==> ${bold_white}Starting Pacman:${reset}"
sudo pacman -Syyu
```

it will work off whatever the existing mirrorlist is. add the following line to the code or run it separately:

```shell
sudo ghostmirror -PoclLS "United States",Canada,Mexico /etc/pacman.d/mirrorlist 20 state,outofdate,morerecent,ping
```

this produced this mirrorlist:
```shell
# lastsync<dd.mm.yyyy> 01.03.2026
## Mexico
Server=https://arch.jsc.mx/$repo/os/$arch
## United States
Server=https://ord.mirror.rackspace.com/archlinux/$repo/os/$arch
Server=http://ord.mirror.rackspace.com/archlinux/$repo/os/$arch
## Canada
Server=http://mirror.csclub.uwaterloo.ca/archlinux/$repo/os/$arch
## United States
Server=http://mirrors.lug.mtu.edu/archlinux/$repo/os/$arch
Server=http://arch.mirror.constant.com/$repo/os/$arch
Server=https://arlm.tyzoid.com/$repo/os/$arch
Server=http://arlm.tyzoid.com/$repo/os/$arch
## Canada
Server=http://mirror.quantum5.ca/archlinux/$repo/os/$arch
## United States
Server=https://mirror.theash.xyz/arch/$repo/os/$arch
Server=http://mirrors.sonic.net/archlinux/$repo/os/$arch
Server=https://arch.hu.fo/archlinux/$repo/os/$arch
Server=https://losangeles.mirror.pkgbuild.com/$repo/os/$arch
Server=https://arch.mirror.constant.com/$repo/os/$arch
Server=https://us.arch.niranjan.co/$repo/os/$arch
Server=http://mirrors.rit.edu/archlinux/$repo/os/$arch
Server=https://mirror.givebytes.net/archlinux/$repo/os/$arch
## Canada
Server=https://mirror.franscorack.com/archlinux/$repo/os/$arch
## United States
Server=http://arch.hu.fo/archlinux/$repo/os/$arch
Server=https://mirrors.rit.edu/archlinux/$repo/os/$arch
```

and with the update.sh script executed again, here's the ghostmirror results:

```shell
:: Building Arch Servers List:
[ pass ] https://arlm.tyzoid.com
[ pass ] http://mirrors.sonic.net/archlinux
[ pass ] http://ord.mirror.rackspace.com/archlinux
[ pass ] https://mirrors.rit.edu/archlinux
[ pass ] https://arch.jsc.mx
[ pass ] https://arch.mirror.constant.com
[ pass ] http://arch.hu.fo/archlinux
[ pass ] http://mirror.quantum5.ca/archlinux
[ pass ] http://mirrors.rit.edu/archlinux
[ pass ] https://mirror.givebytes.net/archlinux
[ pass ] http://mirror.csclub.uwaterloo.ca/archlinux
[ pass ] https://arch.hu.fo/archlinux
[ pass ] http://arlm.tyzoid.com
[ pass ] https://ord.mirror.rackspace.com/archlinux
[ pass ] https://mirror.theash.xyz/arch
[ pass ] https://us.arch.niranjan.co
[ pass ] https://mirror.franscorack.com/archlinux
[ pass ] https://losangeles.mirror.pkgbuild.com
[ pass ] http://mirrors.lug.mtu.edu/archlinux
[ pass ] http://arch.mirror.constant.com
[   3.2MiB/s ] https://arch.jsc.mx
[   2.2MiB/s ] https://ord.mirror.rackspace.com/archlinux
[   3.6MiB/s ] http://ord.mirror.rackspace.com/archlinux
[   5.6MiB/s ] http://mirror.csclub.uwaterloo.ca/archlinux
[   0.4MiB/s ] http://mirrors.lug.mtu.edu/archlinux
[   2.6MiB/s ] http://arch.mirror.constant.com
[   3.3MiB/s ] https://arlm.tyzoid.com
[   2.4MiB/s ] http://arlm.tyzoid.com
[   2.3MiB/s ] http://mirror.quantum5.ca/archlinux
[   1.8MiB/s ] https://mirror.theash.xyz/arch
[   3.7MiB/s ] http://mirrors.sonic.net/archlinux
[   3.3MiB/s ] https://arch.hu.fo/archlinux
[   0.3MiB/s ] https://losangeles.mirror.pkgbuild.com
[   2.6MiB/s ] https://arch.mirror.constant.com
[   0.4MiB/s ] https://us.arch.niranjan.co
[   2.3MiB/s ] http://mirrors.rit.edu/archlinux
[   3.6MiB/s ] https://mirror.givebytes.net/archlinux
[   0.5MiB/s ] https://mirror.franscorack.com/archlinux
[   4.8MiB/s ] http://arch.hu.fo/archlinux
[   3.8MiB/s ] https://mirrors.rit.edu/archlinux
┌─────────────┬───────────────────────────────────────────┬─────┬─────────┬─────────┬─────────┬──────────┬────────────┬─────────┬─────────┐
│   country   │                  mirror                   │proxy│  state  │outofdate│uptodate │morerecent│   speed    │  ping   │estimated│
├─────────────┼───────────────────────────────────────────┼─────┼─────────┼─────────┼─────────┼──────────┼────────────┼─────────┼─────────┤
│United States│http://arch.hu.fo/archlinux                │false│success  │   0.00% │  99.99% │   0.01%  │   4.8MiB/s │  73.7ms │   4gg   │
│United States│https://mirrors.rit.edu/archlinux          │false│success  │   0.00% │  99.99% │   0.01%  │   3.8MiB/s │  82.1ms │   4gg   │
│United States│http://mirrors.sonic.net/archlinux         │false│success  │   0.00% │  99.99% │   0.01%  │   3.7MiB/s │  80.3ms │   4gg   │
│United States│https://mirror.givebytes.net/archlinux     │false│success  │   0.00% │  99.99% │   0.01%  │   3.6MiB/s │  93.2ms │   4gg   │
│United States│http://ord.mirror.rackspace.com/archlinux  │false│success  │   0.00% │  99.99% │   0.01%  │   3.6MiB/s │  64.3ms │   4gg   │
│United States│https://arlm.tyzoid.com                    │false│success  │   0.00% │  99.99% │   0.01%  │   3.3MiB/s │  91.4ms │   4gg   │
│United States│https://arch.hu.fo/archlinux               │false│success  │   0.00% │  99.99% │   0.01%  │   3.3MiB/s │  74.9ms │   4gg   │
│Mexico       │https://arch.jsc.mx                        │false│success  │   0.00% │  99.99% │   0.01%  │   3.2MiB/s │  46.0ms │   4gg   │
│United States│http://arch.mirror.constant.com            │false│success  │   0.00% │  99.99% │   0.01%  │   2.6MiB/s │  66.2ms │   4gg   │
│United States│https://arch.mirror.constant.com           │false│success  │   0.00% │  99.99% │   0.01%  │   2.6MiB/s │  84.2ms │   4gg   │
│United States│http://arlm.tyzoid.com                     │false│success  │   0.00% │  99.99% │   0.01%  │   2.4MiB/s │ 100.0ms │   4gg   │
│Canada       │http://mirror.quantum5.ca/archlinux        │false│success  │   0.00% │  99.99% │   0.01%  │   2.3MiB/s │  62.2ms │   4gg   │
│United States│http://mirrors.rit.edu/archlinux           │false│success  │   0.00% │  99.99% │   0.01%  │   2.3MiB/s │  82.1ms │   4gg   │
│United States│https://ord.mirror.rackspace.com/archlinux │false│success  │   0.00% │  99.99% │   0.01%  │   2.2MiB/s │  55.4ms │   4gg   │
│United States│https://mirror.theash.xyz/arch             │false│success  │   0.00% │  99.99% │   0.01%  │   1.8MiB/s │  70.0ms │   4gg   │
│Canada       │https://mirror.franscorack.com/archlinux   │false│success  │   0.00% │  99.99% │   0.01%  │   0.5MiB/s │  87.0ms │   4gg   │
│United States│http://mirrors.lug.mtu.edu/archlinux       │false│success  │   0.00% │  99.99% │   0.01%  │   0.4MiB/s │  74.5ms │   4gg   │
│United States│https://us.arch.niranjan.co                │false│success  │   0.00% │  99.99% │   0.01%  │   0.4MiB/s │  80.1ms │   4gg   │
│United States│https://losangeles.mirror.pkgbuild.com     │false│success  │   0.00% │  99.99% │   0.01%  │   0.3MiB/s │  89.4ms │   4gg   │
│Canada       │http://mirror.csclub.uwaterloo.ca/archlinux│false│success  │   0.00% │  99.99% │   0.01%  │   5.6MiB/s │ 286.7ms │   3gg   │
└─────────────┴───────────────────────────────────────────┴─────┴─────────┴─────────┴─────────┴──────────┴────────────┴─────────┴─────────┘
# lastsync<dd.mm.yyyy> 01.03.2026
## United States
Server=http://arch.hu.fo/archlinux/$repo/os/$arch
Server=https://mirrors.rit.edu/archlinux/$repo/os/$arch
Server=http://mirrors.sonic.net/archlinux/$repo/os/$arch
Server=https://mirror.givebytes.net/archlinux/$repo/os/$arch
Server=http://ord.mirror.rackspace.com/archlinux/$repo/os/$arch
Server=https://arlm.tyzoid.com/$repo/os/$arch
Server=https://arch.hu.fo/archlinux/$repo/os/$arch
## Mexico
Server=https://arch.jsc.mx/$repo/os/$arch
## United States
Server=http://arch.mirror.constant.com/$repo/os/$arch
Server=https://arch.mirror.constant.com/$repo/os/$arch
Server=http://arlm.tyzoid.com/$repo/os/$arch
## Canada
Server=http://mirror.quantum5.ca/archlinux/$repo/os/$arch
## United States
Server=http://mirrors.rit.edu/archlinux/$repo/os/$arch
Server=https://ord.mirror.rackspace.com/archlinux/$repo/os/$arch
Server=https://mirror.theash.xyz/arch/$repo/os/$arch
## Canada
Server=https://mirror.franscorack.com/archlinux/$repo/os/$arch
## United States
Server=http://mirrors.lug.mtu.edu/archlinux/$repo/os/$arch
Server=https://us.arch.niranjan.co/$repo/os/$arch
Server=https://losangeles.mirror.pkgbuild.com/$repo/os/$arch
## Canada
Server=http://mirror.csclub.uwaterloo.ca/archlinux/$repo/os/$arch
```

with some tweaking from the ghostmirror developer, it looks like you could likely produce some great fast mirrors and be able to add user defined variables building a string for cachyos AND arch.

## caveats

you really need to run ghostmirror twice, once to build the mirrorlist, and once to update and rate it. so if you are doing a cachyos mirrorlist and a arch one, then that's four executable commands. however, using the first run and truncating the rate-able results seems worth it. this also provides a pause where if you needed to address some formating for pacman.conf you would be able to do that before executing a pacman -Syyu and getting it into production.

the developer also recommends in their readme that the first run of the command could be done just once, or very seldom so only the update command would need to be done, say, weekly and of course there's a timer option available for automating this. 

## wrapper 

this would obviously need done in a wrapper and with the storage of an intermediary file in a config directory for modding etc. but the general flow might look something like this:

- use user-defined country or locale, maybe IP (not ideal for VPN use), to determine country, develop string for adjactent countries. i.e. europe grouping DE,NE etc in addition to France. i used "United States",Canada,Mexico for my string.

- next a user-defined storage location or the choice to use the pacman.d directory directly

- amount of mirrors to add to repo...user-defined or default

- select based on state of operation, date synced, more recent and time to ping

these can be passed on where applicable to the final string executed in the update command.

```shell
sudo ghostmirror -PmuolsS  /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist light state,outofdate,morerecent,estimated,speed
```

the above string re-sorts and can be executed more often...speed is the important factor here.

i like the output as well, pretty.

so a tentative executable would take the user-inputs or defaults above, save the mirrorlist, then potentially allow operations to tweak the results, save everything and if its already been run and files are set, update and rank the mirrors for pacman -Syu operations:

```shell
#!/bin/bash

# Colors
bold_cyan="\e[1;36m"
bold_white="\e[1;37m"
reset="\e[0m"

echo -e "${bold_cyan}==> ${bold_white}Starting Update...${reset}"

echo -e "${bold_cyan}:: ${bold_white}Building CachyOS Servers List:${reset}"
sudo ghostmirror -PmuolsS  {temp mirrorlist location} /etc/pacman.d/cachyos-mirrorlist light state,outofdate,morerecent,estimated,speed

#display output
cat /etc/pacman.d/cachyos-mirrorlist

echo -e "${bold_cyan}:: ${bold_white}Building Arch Servers List:${reset}"
sudo ghostmirror -PmuolsS {temp mirrorlist location} /etc/pacman.d/mirrorlist light state,outofdate,morerecent,estimated,speed

#display output
cat /etc/pacman.d/mirrorlist

echo -e "${bold_cyan}==> ${bold_white}Starting Pacman:${reset}"
sudo pacman -Syyu
```