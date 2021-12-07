## Mini-howto for branding the FreeBSD installer ##

### Assumptions ###

 - source code has been pulled down via git (```git clone --origin freebsd https://git.freebsd.org/src.git --branch main --depth 1 /usr/src```) # or releng/13, stable/13, etc.
 - custom kernel config already added

```
make -C /usr/src -j $(sysctl kern.smp.cpus |awk '{print $NF}') KERNCONF=CUSTOM buildworld buildkernel
```

If you're out of date, maybe update your build machine with what was just built

```
make -C /usr/src/ KERNCONF=CUSTOM installkernel

# reboot

make -C /usr/src/ installworld
```






To change the "FreeBSD Installer" to your own, do:

```
cd /usr/src/usr.sbin/bsdinstall || exit 1
# scripts first
for e in \
   scripts/zfsboot \
   scripts/netconfig \
   scripts/keymap \
   scripts/netconfig_ipv6 \
   scripts/checksum \
   scripts/adduser \
   scripts/docsinstall \
   scripts/mirrorselect \
   scripts/netconfig_ipv4 \
   scripts/time \
   scripts/jail \
   scripts/auto \
   scripts/hostname \
   scripts/services \
   scripts/mount \
   scripts/wlanconfig \
   scripts/hardening \
   scripts/rootpass ; do
    sed -i '' 's/FreeBSD Installer/Pro:Centric Installer/' "$e"
done

# then source files
for e in \
  distextract/distextract.c \
  distfetch/distfetch.c \
  partedit/partedit.c ; do
    sed -i '' 's/FreeBSD Installer/Pro:Centric Installer/' "$e"
done

# a rare error message we want branded
sed -i '' 's/version of FreeBSD/version of Pro:Centric/' scripts/checksum

# bootlabel, just in case (only for UFS?)
sed -i '' 's/bootlabel="FreeBSD"/bootlabel="procentric"/' scripts/bootconfig

cd - || exit 1
```



Build the distribution sets for the installer (and create any additional sets you need)

```
make -C /usr/src/release KERNCONF=CUSTOM release
```

Now /usr/obj/usr/src/amd64.amd64/release/ is populated, you can copy base.txz, kernel.txz, (others) into an overlay directory for poudriere-image(8).


#TODO: installerconfig, rc.local, rc.conf, loader.conf, etc.
