#########################################################
#
# Tapper - Test infrastructure
# ----------------------------
#
# More information about Tapper:
#
#  - https://github.com/amd/Tapper-Doc/
#  - http://search.cpan.org/~amd/Tapper-Doc/
#
#########################################################
#
# Initial setup of a Tapper central control machine:
#
#  - chroot for installer to be booted over nfs
#
#  - chroot and image file for baseimage
#    to be installed on testmachines
#
#  - a client package with Tapper toolchain
#    to be installed on testmachines
#
#  - local setup for central Tapper host
#      . databases
#      . nfs exports
#      . tftpserver
#      . Tapper daemons
#      . initial Tapper config
#      . Web frontend on Apache
#      . initial example test report
# 
#########################################################

# ===== OSRC specific defaults =====

DEBIANMIRROR ?= http://ftp.de.debian.org/debian/

# install Tapper libs from Git repositories
TAPPERFROMGIT ?= 0

# build Python with some tweaks to static/shared modules
# (urrently only used for the baseimages/clientpkgs [tapper-starterkit/utils/prepare-baseimage-in-chroot])
PYTHONSTATICTWEAKS ?= 0

GITPREFIXTAPPERLIBS ?= git://github.com/amd
GITPREFIXTESTSUITES ?= git://wotan

# use local starterkit instead of download from amd64.org
LOCALSTARTERKIT ?= 1

SCRIPTENVIRONMENT = PERLVERSION=$(PERLVERSION) TAPPER_SERVER=$(TAPPER_SERVER) TAPPERFROMGIT=$(TAPPERFROMGIT) GITPREFIXTAPPERLIBS=$(GITPREFIXTAPPERLIBS) GITPREFIXTESTSUITES=$(GITPREFIXTESTSUITES)

# ===== General configuration =====

TMP=/tmp

DATATAPPER      = /data/tapper
# meta-reports
METAREPORTSDIR  = $(DATATAPPER)/live/metareports
# intermediate output
OUTPUTDIR       = $(DATATAPPER)/live/output
# OS images
IMAGESDIR       = $(DATATAPPER)/live/repository/images
# packages
PACKAGESDIR     = $(DATATAPPER)/live/repository/packages
# Tapper clientlib
TAPPERUTILSDIR  = $(DATATAPPER)/live/repository/packages/tapperutils
# test programs
TESTPROGRAMDIR  = $(DATATAPPER)/live/testprogram
# auto-generated configs
CONFIGSDIR      = $(DATATAPPER)/live/configs
# complete use-case specfiles
USECASEDIR      = $(CONFIGSDIR)/use_cases
# grub configs via TFTP
TFTPBOOTDIR     = $(DATATAPPER)/live/configs/tftpboot
# sync'ing interdependent tests
SYNCDIR         = $(DATATAPPER)/live/sync
# network bootable OS image
NFSROOTDIR      = $(DATATAPPER)/live/nfsroot

# installer NFS root
# 64bit always
INSTALLERCHROOT=/mnt/chroot/squeeze-tapper-nfsroot-for-installer
INSTALLERCHROOTPREPARE=prepare-installer-nfsroot-in-chroot
INSTALLERCHROOTPREPAREPERLMODULES=prepare-installer-nfsroot-perlmodules

# baseimage
# 64bit
BASEIMAGE64FILE=$(IMAGESDIR)/debian_base64.tgz
BASEIMAGE64CHROOT=/mnt/chroot/squeeze-tapper-baseimage64
BASEIMAGE64CHROOTPREPARE=prepare-baseimage-in-chroot
# 32bit
BASEIMAGE32FILE=$(IMAGESDIR)/debian_base32.tgz
BASEIMAGE32CHROOT=/mnt/chroot/squeeze-tapper-baseimage32
BASEIMAGE32CHROOTPREPARE=prepare-baseimage-in-chroot

# client package
# 64bit
CLIENTPKG64FILE=$(TAPPERUTILSDIR)/opt-tapper64.tar.gz
CLIENTPKG64CHROOT=/mnt/chroot/squeeze-tapper-clientpkg64
CLIENTPKG64CHROOTPREPARE=prepare-clientpkg-in-chroot
# 32bit
CLIENTPKG32FILE=$(TAPPERUTILSDIR)/opt-tapper32.tar.gz
CLIENTPKG32CHROOT=/mnt/chroot/squeeze-tapper-clientpkg32
CLIENTPKG32CHROOTPREPARE=prepare-clientpkg-in-chroot

### initial machine names
TAPPER_SERVER?=tapper
TESTMACHINE1=johnconnor
TESTMACHINE2=sarahconnor
TESTMACHINE3=bullock

### Perl setup
### if version changed, also change it in the *CHROOTPREPARE scripts
PERLVERSION ?= 5.12.3
DISTROPREFS=http://cpansearch.perl.org/src/ANDK/CPAN-1.9800/distroprefs/
DISTROPREFSDIR=/root/.cpan/prefs
PERLBIN=/opt/tapper/perl/perls/current/bin
CPAN=$(PERLBIN)/cpan
PERL=$(PERLBIN)/perl
PERLPREPARE = $(SCRIPTENVIRONMENT) $(STARTERKITDIR)/utils/prepare-optperl

### Python setup
PYTHONDIR=/opt/tapper/python
PYTHONBIN=$(PYTHONDIR)/bin
PYTHONPREPARE = $(SCRIPTENVIRONMENT) $(STARTERKITDIR)/utils/prepare-optpython

### opentftpd
#OPENTFTPDURL=http://downloads.sourceforge.net/project/tftp-server/tftp%20server%20single%20port/opentftpspV1.65.tar.gz
OPENTFTPDURL=http://renormalist.net/tftp/opentftpmtV1.62.tar.gz # the last working TFTP in the world :-/

# Debian automatic install
APTGETOPTIONS=-o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" --yes
APTGET=DEBIAN_FRONTEND=noninteractive; export DEBIAN_FRONTEND ; apt-get $(APTGETOPTIONS)
INSSERV ?= "insserv"
UPDATERC ?= update-rc.d -f

### starter kit
STARTERKITBASENAME=tapper-starterkit
STARTERKIT=$(STARTERKITBASENAME).tgz
STARTERKITURL=http://amd64.org/pub/tapper/$(STARTERKIT)
STARTERKITDIR=$(TMP)/$(STARTERKITBASENAME)

PREPAREPERLMODULE   = $(SCRIPTENVIRONMENT) $(STARTERKITDIR)/utils/prepare-perlmodule
PREPAREPYTHONMODULE = $(SCRIPTENVIRONMENT) $(STARTERKITDIR)/utils/prepare-pythonmodule

### Codespeed
CODESPEED_INSTANCENAME ?= speedcenter
CODESPEED_GITURL       ?= git://github.com/tobami/codespeed.git
CODESPEED_BASEURL      ?= http://$(TAPPER_SERVER)/speed
CODESPEED_DIR          ?= /opt/tapper/codespeed/$(CODESPEED_INSTANCENAME)
CODESPEED_URLSFILE      = $(CODESPEED_DIR)/urls.py

### intermediate build files, not the final results
CLEANFILES = $(TMP)/$(STARTERKIT) \
             $(STARTERKITDIR) \
             $(DISTROPREFSDIR)

CLEANFILESCHROOTNFSROOT    = $(INSTALLERCHROOT)
CLEANFILESCHROOTBASEIMAGES = $(BASEIMAGE64CHROOT) $(BASEIMAGE32CHROOT)
CLEANFILESCHROOTCLIENTPKGS = $(CLIENTPKG64CHROOT) $(CLIENTPKG32CHROOT)

LOCALCLEANFILES=$(TMP)/$(STARTERKIT) \
                $(STARTERKITDIR) \
                $(DISTROPREFSDIR) \
                /opt/tapper/perl

CPUCOUNT = `cat /proc/cpuinfo | grep vendor_id|wc -l`

CPAN_INSTALL = PERL_AUTOINSTALL="--defaultdeps" TEST_JOBS=$(CPUCOUNT) $(CPAN)

# default target
help:
	@echo ""
	@echo "Try most common targets:"
	@echo "------------------------"
	@echo "  make web              -- update WebGUI"
	@echo "  make mcp              -- update MCP"
	@echo "  make cli              -- update CLI"
	@echo "  make api              -- update Tapper::Reports::API"
	@echo "  make receiver         -- update Tapper::Reports::Receiver"
	@echo "  make action           -- update Tapper::Action"
	@echo "  make testplan         -- update Testplan"
	@echo "  make nfsroot++        -- update Tapper::Installer & friends in nfsroot"
	@echo "  make clientpkgs       -- create client packages (64bit + 32bit)"
	@echo "  make clientpkgs++     -- update client packages (64bit + 32bit)"
	@echo "  make baseimages       -- create base images     (64bit + 32bit)"
	@echo ""
	@echo "  make allautomation    -- everything for automation"
	@echo "                           *except* the MCP-MessageReceiver"
	@echo "                              . cleanups to force rebuild"
	@echo "                              . nfsrootdir"
	@echo "                              . baseimages"
	@echo "                              . clientpkgs"
	@echo "                              . mcp"
	@echo ""
	@echo "Special targets:"
	@echo "---------------"
	@echo "  make upgrade          -- upgrade most usual components and"
	@echo "                           show what more could be useful."
	@echo "  make mcpmsgreceiver   -- MCP-MessageReceiver"
	@echo "                           => only do if *REALLY* needed"
	@echo "                              because it risks MCP persistence!"
	@echo "  make schema-upgrade   -- run DB schema upgrade scripts"
	@echo "                           => after 'make schema' with structural changes"
	@echo ""
	@echo "Other targets:"
	@echo "--------------"
	@echo "  make nfsrootdir       -- a base OS subdir bootable from NFS for Tapper::Installer"
	@echo "  make localsetup       -- most stuff to run a central Tapper host"
	@echo "                           but WITHOUT automation. Contains:"
	@echo "                              . reports receiver"
	@echo "                              . reports api"
	@echo "                              . web frontend"
	@echo "  make tapperhost       -- everything to run a central Tapper control host"
	@echo "                           INCLUSIVE automation. Contains:"
	@echo "                              . make installerchroot"
	@echo "                              . make baseimage64"
	@echo "                              . make clientpkg64"
	@echo "                              . make localsetup"
	@echo "  make it so            -- same as 'make tapperhost'"
	@echo "  make clean            -- remove intermediate build files+chroots"
	@echo "                           (keeps the final /data/tapper/... intact)"

clean:
	-umount $(INSTALLERCHROOT)/sys
	-umount $(INSTALLERCHROOT)/proc
	-umount $(INSTALLERCHROOT)/dev/pts
	-umount $(BASEIMAGE64CHROOT)/sys
	-umount $(BASEIMAGE64CHROOT)/proc
	-umount $(BASEIMAGE64CHROOT)/dev/pts
	-umount $(BASEIMAGE32CHROOT)/sys
	-umount $(BASEIMAGE32CHROOT)/proc
	-umount $(BASEIMAGE32CHROOT)/dev/pts
	-umount $(CLIENTPKG64CHROOT)/sys
	-umount $(CLIENTPKG64CHROOT)/proc
	-umount $(CLIENTPKG64CHROOT)/dev/pts
	-umount $(CLIENTPKG32CHROOT)/sys
	-umount $(CLIENTPKG32CHROOT)/proc
	-umount $(CLIENTPKG32CHROOT)/dev/pts
	-rm -fr $(CLEANFILES)

cleanchrootnfs:
	-umount $(INSTALLERCHROOT)/sys
	-umount $(INSTALLERCHROOT)/proc
	-umount $(INSTALLERCHROOT)/dev/pts
	-rm -fr $(CLEANFILESCHROOTNFSROOT)

 cleanchrootbaseimages:
	-umount $(BASEIMAGE64CHROOT)/sys
	-umount $(BASEIMAGE64CHROOT)/proc
	-umount $(BASEIMAGE64CHROOT)/dev/pts
	-umount $(BASEIMAGE32CHROOT)/sys
	-umount $(BASEIMAGE32CHROOT)/proc
	-umount $(BASEIMAGE32CHROOT)/dev/pts
	-rm -fr $(CLEANFILESCHROOTBASEIMAGES)

 cleanchrootclientpkgs:
	-umount $(CLIENTPKG64CHROOT)/sys
	-umount $(CLIENTPKG64CHROOT)/proc
	-umount $(CLIENTPKG64CHROOT)/dev/pts
	-umount $(CLIENTPKG32CHROOT)/sys
	-umount $(CLIENTPKG32CHROOT)/proc
	-umount $(CLIENTPKG32CHROOT)/dev/pts
	-rm -fr $(CLEANFILESCHROOTCLIENTPKGS)

cleanchroots: cleanchrootnfs cleanchrootbaseimages cleanchrootclientpkgs


cleanlocal:
	@echo "########## Remove local machine setup"
	-$(APTGET) remove --purge expect-dev
	-$(APTGET) remove --purge expect
	-$(APTGET) remove --purge libssl-dev
	-$(APTGET) remove --purge mysql-server
	-$(APTGET) remove --purge libmysqlclient-dev
	-$(APTGET) remove --purge libsqlite3-0
	-$(APTGET) remove --purge sqlite3
	-$(APTGET) remove --purge libxml2-dev
	-$(APTGET) remove --purge libxml2
	-$(APTGET) remove --purge libexpat1-dev
	-$(APTGET) remove --purge libexpat1
	-$(APTGET) remove --purge libapache2-mod-fcgid
	-$(APTGET) remove --purge apache2
	-$(APTGET) remove --purge atftpd
	-$(APTGET) autoremove
	-rm -fr $(LOCALCLEANFILES)

cleanbaseimage64chroot:
	-umount $(BASEIMAGE64CHROOT)/sys
	-umount $(BASEIMAGE64CHROOT)/proc
	-umount $(BASEIMAGE64CHROOT)/dev/pts
	-rm -fr $(BASEIMAGE64CHROOT)

cleanbaseimage32chroot:
	-umount $(BASEIMAGE32CHROOT)/sys
	-umount $(BASEIMAGE32CHROOT)/proc
	-umount $(BASEIMAGE32CHROOT)/dev/pts
	-rm -fr $(BASEIMAGE32CHROOT)

cleanbaseimage64file:
	-rm -f $(BASEIMAGE64FILE)

cleanbaseimage32file:
	-rm -f $(BASEIMAGE32FILE)

cleanstarterkit:
	-rm -f $(STARTERKITDIR)/etc/log4perl.cfg

cleanoptpython:
	-rm -fr $(PYTHONDIR)

cleandb:
	-echo "drop database testrundb; drop database reportsdb; drop user tapper;" | mysql -uroot -p$${MYSQL_ROOT_PW}

mrproper: clean localclean localcleandb nfsrootclean
	-rm -fr $(CLIENTPKG64FILE)
	-rm -fr $(CLIENTPKG32FILE)
	-rm -fr $(BASEIMAGE64FILE)
	-rm -fr $(BASEIMAGE32FILE)

# ==============================================================
# *
# *
# *
# *                      General setup utils
# *
# *
# *
# ==============================================================

distroprefs: $(DISTROPREFSDIR)/00.README

$(DISTROPREFSDIR)/00.README:
	mkdir -p $(DISTROPREFSDIR)/
	wget -r --no-parent -nd -N -P $(DISTROPREFSDIR)/ $(DISTROPREFS)
# fix non-fitting distroprefs
	perl -pni -e 's/disabled: +1/disabled: 0/' $(DISTROPREFSDIR)/PARDUS.File-MimeInfo.yml

dependencies: distroprefs starterkit

starterkit:
ifeq ($(LOCALSTARTERKIT),1)
	cd .. ; tar -czf $(STARTERKIT) $(STARTERKITBASENAME)/ ; cp $(STARTERKIT) $(TMP)/$(STARTERKIT)
else
	curl -z$(TMP)/$(STARTERKIT) -L $(STARTERKITURL) -o $(TMP)/$(STARTERKIT)
endif
	cd $(TMP) ; tar xzf $(STARTERKIT)


# ==============================================================
# *
# *
# *
# *                      Installer NFS root
# *
# *
# *
# ==============================================================

installerchroot: dependencies libdeps $(INSTALLERCHROOT)/etc/log4perl.cfg

$(INSTALLERCHROOT)/etc/log4perl.cfg:
	@echo "##################################################################"
	@echo "# "
	@echo "# "
	@echo "#         ,************************."
	@echo "# Set up  *   INSTALLER NFS ROOT   *  for net booting testmachines"
	@echo "#         '************************'"
	@echo "# "
	@echo "# "
	@echo "##################################################################"
#
### prepare subdirs
#
	@echo "########## prepare subdirs"
	-umount $(INSTALLERCHROOT)/sys
	-umount $(INSTALLERCHROOT)/proc
	-umount $(INSTALLERCHROOT)/dev/pts
	-rm -fr $(INSTALLERCHROOT)
	mkdir -p $(INSTALLERCHROOT)
#
### bootstrap new debian in chroot
#
	@echo "########## bootstrap new debian in chroot"
	debootstrap --arch amd64 squeeze $(INSTALLERCHROOT) $(DEBIANMIRROR)
	mount -t proc proc $(INSTALLERCHROOT)/proc
	mount -t devpts devpts $(INSTALLERCHROOT)/dev/pts
#
### set shell environment
#
	@echo "########## set shell environment"
	echo 'PS1="CHROOT-Tapper-NFS-Installer:\w# "'  >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'CPUCOUNT=`cat /proc/cpuinfo | grep vendor_id | wc -l`' >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'export TEST_JOBS=$$CPUCOUNT'             >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'export PERL_AUTOINSTALL="--defaultdeps"' >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'export TWMC_TEST_PORT=9876'              >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'alias dir="ls -Flart --color"'           >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'rm="rm -i"'                              >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'cp="cp -i"'                              >> $(INSTALLERCHROOT)/root/.bashrc
	echo 'mv="mv -i"'                              >> $(INSTALLERCHROOT)/root/.bashrc
#
### Perl/CPAN setup
#
	@echo "########## Perl/CPAN setup"
	mkdir -p $(INSTALLERCHROOT)/$(DISTROPREFSDIR)/
	rsync -r $(DISTROPREFSDIR)/ $(INSTALLERCHROOT)/$(DISTROPREFSDIR)/
	perl -pni -e 's/disabled: +1/disabled: 0/' $(INSTALLERCHROOT)/$(DISTROPREFSDIR)/PARDUS.File-MimeInfo.yml
#
### enable console login
#
	@echo "########## enable console login"
	echo 's0:12345:respawn:/sbin/getty 115200 ttyS0 vt100' >> $(INSTALLERCHROOT)/etc/inittab
#
### set NFS read-only
#
	@echo "########## set NFS read-only"
	sed -i -e 's/rootmode=rw/rootmode=ro/' $(INSTALLERCHROOT)/etc/init.d/checkroot.sh
#
### prepare writeable directories
#
	@echo "########## prepare writeable directories"
	echo 'proc  /proc      proc  defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'sysfs /sys       sysfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'tmpfs /tmp       tmpfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'ramfs /var/log   ramfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'ramfs /var/lock  ramfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'ramfs /var/run   ramfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'ramfs /var/spool ramfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'ramfs /var/lib/nfs ramfs defaults 0 0' >> $(INSTALLERCHROOT)/etc/fstab
	echo 'tmpfs /mnt       tmpfs rw,size=4096k 2 0' >> $(INSTALLERCHROOT)/etc/fstab
#
### configure NFS working directory
#
	@echo "########## configure NFS working directory"
	echo "$(TAPPER_SERVER):$(DATATAPPER) $(DATATAPPER) nfs auto,defaults 0 0" >> $(INSTALLERCHROOT)/etc/fstab
#
### ssh authorized_keys for reboot via ssh
#
	mkdir -p $(INSTALLERCHROOT)/root/.ssh
	if [ -r /root/.ssh/id_dsa.pub ] ; then cat /root/.ssh/id_dsa.pub >> $(INSTALLERCHROOT)/root/.ssh/authorized_keys ; fi
	if [ -r /root/.ssh/id_rsa.pub ] ; then cat /root/.ssh/id_rsa.pub >> $(INSTALLERCHROOT)/root/.ssh/authorized_keys ; fi
	-chmod 600 $(INSTALLERCHROOT)/root/.ssh/authorized_keys
	-chmod 700 $(INSTALLERCHROOT)/root/.ssh
#
### run remaining configuration from inside the chroot
#
	@echo "########## run remaining configuration from inside the chroot"
	cd $(INSTALLERCHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)
	$(SCRIPTENVIRONMENT) chroot $(INSTALLERCHROOT) /tmp/tapper-starterkit/utils/$(INSTALLERCHROOTPREPARE)

installerchrootperl: installerchroot
	@echo "########## prepare subdirs"
	-umount $(INSTALLERCHROOT)/sys
	-umount $(INSTALLERCHROOT)/proc
	-umount $(INSTALLERCHROOT)/dev/pts
	mount -t proc proc $(INSTALLERCHROOT)/proc
	mount -t devpts devpts $(INSTALLERCHROOT)/dev/pts
	@echo "########## run remaining configuration from inside the chroot"
	cd $(INSTALLERCHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)
	$(SCRIPTENVIRONMENT) chroot $(INSTALLERCHROOT) /tmp/tapper-starterkit/utils/$(INSTALLERCHROOTPREPAREPERLMODULES)

nfsroot++: installerchrootperl nfsrootdir


# ==============================================================
# *
# *
# *
# *                      Base Image
# *
# *
# *
# ==============================================================

baseimage64chroot: dependencies $(BASEIMAGE64CHROOT)/etc/log4perl.cfg
# always update starterkit for subsequent targets
	cd $(BASEIMAGE64CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)

baseimage32chroot: dependencies $(BASEIMAGE32CHROOT)/etc/log4perl.cfg
# always update starterkit for subsequent targets
	cd $(BASEIMAGE32CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)

$(BASEIMAGE64CHROOT)/etc/log4perl.cfg:
	@echo "########################################################"
	@echo "# "
	@echo "#         ,**********************."
	@echo "# Set up  *   BASE IMAGE 64bit   *  to be used for Tests"
	@echo "#         '**********************'"
	@echo "# "
	@echo "########################################################"
#
### prepare subdirs
#
	@echo "########## prepare subdirs"
	-umount $(BASEIMAGE64CHROOT)/proc
	-umount $(BASEIMAGE64CHROOT)/dev/pts
	-rm -fr $(BASEIMAGE64CHROOT)
	mkdir -p $(BASEIMAGE64CHROOT)
#
### bootstrap new debian in it
#
	@echo "########## bootstrap new debian in it"
	debootstrap --arch amd64 squeeze $(BASEIMAGE64CHROOT) $(DEBIANMIRROR)
	mount -t proc proc $(BASEIMAGE64CHROOT)/proc
	mount -t devpts devpts $(BASEIMAGE64CHROOT)/dev/pts
#
### set shell environment
#
	@echo "########## set shell environment"
	echo 'PS1="CHROOT-Tapper-BaseImage64:\w# "'      >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'CPUCOUNT=`cat /proc/cpuinfo | grep vendor_id | wc -l`' >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'export TEST_JOBS=$$CPUCOUNT'             >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'export PERL_AUTOINSTALL="--defaultdeps"' >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'export PERLBREW_ROOT=/opt/tapper/perl'   >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'export PATH=/opt/tapper/python/bin:$${PATH}' >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'export TWMC_TEST_PORT=9876'              >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'alias dir="ls -Flart --color"'           >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'rm="rm -i"'                              >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'cp="cp -i"'                              >> $(BASEIMAGE64CHROOT)/root/.bashrc
	echo 'mv="mv -i"'                              >> $(BASEIMAGE64CHROOT)/root/.bashrc
	mkdir -p $(BASEIMAGE64CHROOT)/root/.ssh
	if [ -r /root/.ssh/id_dsa.pub ] ; then cat /root/.ssh/id_dsa.pub >> $(BASEIMAGE64CHROOT)/root/.ssh/authorized_keys ; fi
	if [ -r /root/.ssh/id_rsa.pub ] ; then cat /root/.ssh/id_rsa.pub >> $(BASEIMAGE64CHROOT)/root/.ssh/authorized_keys ; fi
	-chmod 600 $(BASEIMAGE64CHROOT)/root/.ssh/authorized_keys
	-chmod 700 $(BASEIMAGE64CHROOT)/root/.ssh
#
### Perl/CPAN setup
#
	@echo "########## Perl/CPAN setup"
	mkdir -p $(BASEIMAGE64CHROOT)/$(DISTROPREFSDIR)/
	rsync -r $(DISTROPREFSDIR)/ $(BASEIMAGE64CHROOT)/$(DISTROPREFSDIR)/
	-perl -pni -e 's/disabled: +1/disabled: 0/' $(BASEIMAGE64CHROOT)/$(DISTROPREFSDIR)/PARDUS.File-MimeInfo.yml
#
### enable console login
#
	@echo "########## enable console login"
	echo 's0:12345:respawn:/sbin/getty 115200 ttyS0 vt100' >> $(BASEIMAGE64CHROOT)/etc/inittab
#
### prepare writeable directories
#
	@echo "########## prepare writeable directories"
	echo 'proc  /proc      proc  defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
	echo 'sysfs /sys       sysfs defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
	echo 'tmpfs /tmp       tmpfs defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
	echo 'ramfs /var/log   ramfs defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
	echo 'ramfs /var/lock  ramfs defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
	echo 'ramfs /var/run   ramfs defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
	echo 'ramfs /var/spool ramfs defaults 0 0' >> $(BASEIMAGE64CHROOT)/etc/fstab
#
### configure NFS working directory
#
	@echo "########## configure NFS working directory"
	echo "$(TAPPER_SERVER):$(DATATAPPER) $(DATATAPPER) nfs auto,defaults 0 0" >> $(BASEIMAGE64CHROOT)/etc/fstab
#
### run remaining configuration from inside the chroot
#
	@echo "########## run remaining configuration from inside the chroot"
	cd $(BASEIMAGE64CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)
	$(SCRIPTENVIRONMENT) chroot $(BASEIMAGE64CHROOT) /tmp/tapper-starterkit/utils/$(BASEIMAGE64CHROOTPREPARE)

$(BASEIMAGE32CHROOT)/etc/log4perl.cfg:
	@echo "########################################################"
	@echo "# "
	@echo "#         ,**********************."
	@echo "# Set up  *   BASE IMAGE 32bit   *  to be used for Tests"
	@echo "#         '**********************'"
	@echo "# "
	@echo "########################################################"
#
### prepare subdirs
#
	@echo "########## prepare subdirs"
	-umount $(BASEIMAGE32CHROOT)/proc
	-umount $(BASEIMAGE32CHROOT)/dev/pts
	-rm -fr $(BASEIMAGE32CHROOT)
	mkdir -p $(BASEIMAGE32CHROOT)
#
### bootstrap new debian in it
#
	@echo "########## bootstrap new debian in it"
	debootstrap --arch i386 squeeze $(BASEIMAGE32CHROOT) $(DEBIANMIRROR)
	mount -t proc proc $(BASEIMAGE32CHROOT)/proc
	mount -t devpts devpts $(BASEIMAGE32CHROOT)/dev/pts
#
### set shell environment
#
	@echo "########## set shell environment"
	echo 'PS1="CHROOT-Tapper-BaseImage32:\w# "'      >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'CPUCOUNT=`cat /proc/cpuinfo | grep vendor_id | wc -l`' >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'export TEST_JOBS=$$CPUCOUNT'             >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'export PERL_AUTOINSTALL="--defaultdeps"' >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'export PERLBREW_ROOT=/opt/tapper/perl'   >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'export PATH=/opt/tapper/python/bin:$${PATH}' >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'export TWMC_TEST_PORT=9876'              >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'alias dir="ls -Flart --color"'           >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'rm="rm -i"'                              >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'cp="cp -i"'                              >> $(BASEIMAGE32CHROOT)/root/.bashrc
	echo 'mv="mv -i"'                              >> $(BASEIMAGE32CHROOT)/root/.bashrc
	mkdir -p $(BASEIMAGE32CHROOT)/root/.ssh
	if [ -r /root/.ssh/id_dsa.pub ] ; then cat /root/.ssh/id_dsa.pub >> $(BASEIMAGE32CHROOT)/root/.ssh/authorized_keys ; fi
	if [ -r /root/.ssh/id_rsa.pub ] ; then cat /root/.ssh/id_rsa.pub >> $(BASEIMAGE32CHROOT)/root/.ssh/authorized_keys ; fi
	-chmod 600 $(BASEIMAGE32CHROOT)/root/.ssh/authorized_keys
	-chmod 700 $(BASEIMAGE32CHROOT)/root/.ssh
#
### Perl/CPAN setup
#
	@echo "########## Perl/CPAN setup"
	mkdir -p $(BASEIMAGE32CHROOT)/$(DISTROPREFSDIR)/
	rsync -r $(DISTROPREFSDIR)/ $(BASEIMAGE32CHROOT)/$(DISTROPREFSDIR)/
	-perl -pni -e 's/disabled: +1/disabled: 0/' $(BASEIMAGE32CHROOT)/$(DISTROPREFSDIR)/PARDUS.File-MimeInfo.yml
#
### enable console login
#
	@echo "########## enable console login"
	echo 's0:12345:respawn:/sbin/getty 115200 ttyS0 vt100' >> $(BASEIMAGE32CHROOT)/etc/inittab
#
### prepare writeable directories
#
	@echo "########## prepare writeable directories"
	echo 'proc  /proc      proc  defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
	echo 'sysfs /sys       sysfs defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
	echo 'tmpfs /tmp       tmpfs defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
	echo 'ramfs /var/log   ramfs defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
	echo 'ramfs /var/lock  ramfs defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
	echo 'ramfs /var/run   ramfs defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
	echo 'ramfs /var/spool ramfs defaults 0 0' >> $(BASEIMAGE32CHROOT)/etc/fstab
#
### configure NFS working directory
#
	@echo "########## configure NFS working directory"
	echo "$(TAPPER_SERVER):$(DATATAPPER) $(DATATAPPER) nfs auto,defaults 0 0" >> $(BASEIMAGE32CHROOT)/etc/fstab
#
### run remaining configuration from inside the chroot
#
	@echo "########## run remaining configuration from inside the chroot"
	cd $(BASEIMAGE32CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)
	$(SCRIPTENVIRONMENT) linux32 chroot $(BASEIMAGE32CHROOT) /tmp/tapper-starterkit/utils/$(BASEIMAGE32CHROOTPREPARE)

clientpkgs: libdeps clientpkg64 clientpkg32
clientpkgs++: clientpkg64++ clientpkg32++

allautomation: cleanstarterkit \
               cleanchroots \
               cleanbaseimage64file cleanbaseimage64chroot \
               cleanbaseimage32file cleanbaseimage32chroot \
               starterkit \
               installerchroot \
               nfsrootdir \
               baseimage64file \
               baseimage32file \
               clientpkgs \
               mcp


# ==============================================================
# *
# *
# *
# *                      64 bit client pkg
# *
# *
# *
# ==============================================================

clientpkg64:   prepareclientchroot64                updateclientpkg64
clientpkg64++: prepareclientchroot64 addclientpkg64 updateclientpkg64

prepareclientchroot64: baseimage64file
#
### prepare subdirs
#
	@echo "########## Prepare client-side opt-tapper package"
	-umount $(CLIENTPKG64CHROOT)/proc
	-umount $(CLIENTPKG64CHROOT)/dev/pts
	-rm -fr $(CLIENTPKG64CHROOT)
	mkdir -p $(CLIENTPKG64CHROOT)
	tar -C $(CLIENTPKG64CHROOT) -xzf $(BASEIMAGE64FILE)
	mount -t proc proc $(CLIENTPKG64CHROOT)/proc
	mount -t devpts devpts $(CLIENTPKG64CHROOT)/dev/pts
	cd $(CLIENTPKG64CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)

addclientpkg64:
	tar -C $(CLIENTPKG64CHROOT) -xzf $(CLIENTPKG64FILE)

updateclientpkg64:
#
# Prepare client-side opt-tapper package
#
	@echo "##############################################################"
	@echo "# "
	@echo "#         ,**************************."
	@echo "# SET UP  *   CLIENT PACKAGE 64bit   *"
	@echo "#         '**************************'"
	@echo "# "
	@echo "##############################################################"
	cd $(CLIENTPKG64CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)
	$(SCRIPTENVIRONMENT) chroot $(CLIENTPKG64CHROOT) /tmp/tapper-starterkit/utils/$(CLIENTPKG64CHROOTPREPARE)
	-umount $(CLIENTPKG64CHROOT)/proc
	-umount $(CLIENTPKG64CHROOT)/dev/pts
	cd $(CLIENTPKG64CHROOT) ; \
	  if [ -r $(CLIENTPKG64FILE) ] ; then cp $(CLIENTPKG64FILE) $(CLIENTPKG64FILE)_bak_`date +%Y%m%d%H%M` ; fi ; \
	  tar -czf $(CLIENTPKG64FILE) opt/


# ==============================================================
# *
# *
# *
# *                      32 bit client pkg
# *
# *
# *
# ==============================================================


clientpkg32:   prepareclientchroot32                updateclientpkg32
clientpkg32++: prepareclientchroot32 addclientpkg32 updateclientpkg32

prepareclientchroot32: baseimage32file
#
### prepare subdirs
#
	@echo "########## Prepare client-side opt-tapper package"
	-umount $(CLIENTPKG32CHROOT)/proc
	-umount $(CLIENTPKG32CHROOT)/dev/pts
	-rm -fr $(CLIENTPKG32CHROOT)
	mkdir -p $(CLIENTPKG32CHROOT)
	tar -C $(CLIENTPKG32CHROOT) -xzf $(BASEIMAGE32FILE)
	mount -t proc proc $(CLIENTPKG32CHROOT)/proc
	mount -t devpts devpts $(CLIENTPKG32CHROOT)/dev/pts
	cd $(CLIENTPKG32CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)

addclientpkg32:
	tar -C $(CLIENTPKG32CHROOT) -xzf $(CLIENTPKG32FILE)

updateclientpkg32:
#
# Prepare client-side opt-tapper package
#
	@echo "##############################################################"
	@echo "# "
	@echo "#         ,**************************."
	@echo "# SET UP  *   CLIENT PACKAGE 32bit   *"
	@echo "#         '**************************'"
	@echo "# "
	@echo "##############################################################"
	cd $(CLIENTPKG32CHROOT)/tmp/ ; tar xzf $(TMP)/$(STARTERKIT)
	$(SCRIPTENVIRONMENT) linux32 chroot $(CLIENTPKG32CHROOT) /tmp/tapper-starterkit/utils/$(CLIENTPKG32CHROOTPREPARE)
	-umount $(CLIENTPKG32CHROOT)/proc
	-umount $(CLIENTPKG32CHROOT)/dev/pts
	cd $(CLIENTPKG32CHROOT) ; \
	  if [ -r $(CLIENTPKG32FILE) ] ; then cp $(CLIENTPKG32FILE) $(CLIENTPKG32FILE)_bak_`date +%Y%m%d%H%M` ; fi ; \
	  tar -czf $(CLIENTPKG32FILE) opt/


# ==============================================================
# *
# *
# *
# *                      Tapper Central Server
# *
# *
# *
# ==============================================================


intro:
	@echo "########################################################"
	@echo "# "
	@echo "# "
	@echo "# Set up central Tapper host"
	@echo "# "
	@echo "# "
	@echo "########################################################"


WORKDIRS := $(METAREPORTSDIR) \
             $(OUTPUTDIR) \
             $(IMAGESDIR) \
             $(PACKAGESDIR) \
             $(TAPPERUTILSDIR) \
             $(TESTPROGRAMDIR) \
             $(CONFIGSDIR) \
			 $(USECASEDIR) \
             $(TFTPBOOTDIR) \
             $(SYNCDIR) \
             $(NFSROOTDIR)

$(WORKDIRS):
	mkdir -p $@

workingdirs: $(WORKDIRS)

baseimage64file: baseimage64chroot workingdirs $(BASEIMAGE64FILE)

baseimage32file: baseimage32chroot workingdirs $(BASEIMAGE32FILE)

baseimages: baseimage64file baseimage32file

$(BASEIMAGE64FILE): $(BASEIMAGE64CHROOT)/etc/log4perl.cfg
#
# Pack baseimage64
#
	@echo "########## Pack baseimage64"
	-umount $(BASEIMAGE64CHROOT)/sys
	-umount $(BASEIMAGE64CHROOT)/proc
	-umount $(BASEIMAGE64CHROOT)/dev/pts
	cd $(BASEIMAGE64CHROOT) ; \
	  if [ -r $(BASEIMAGE64FILE) ] ; then cp $(BASEIMAGE64FILE) $(BASEIMAGE64FILE)_bak_`date +%Y%m%d%H%M` ; fi ; \
	  tar -czf $(BASEIMAGE64FILE) .

$(BASEIMAGE32FILE): $(BASEIMAGE32CHROOT)/etc/log4perl.cfg
#
# Pack baseimage32
#
	@echo "########## Pack baseimage32"
	-umount $(BASEIMAGE32CHROOT)/sys
	-umount $(BASEIMAGE32CHROOT)/proc
	-umount $(BASEIMAGE32CHROOT)/dev/pts
	cd $(BASEIMAGE32CHROOT) ; \
	  if [ -r $(BASEIMAGE32FILE) ] ; then cp $(BASEIMAGE32FILE) $(BASEIMAGE32FILE)_bak_`date +%Y%m%d%H%M` ; fi ; \
	  tar -czf $(BASEIMAGE32FILE) .

nfsrootdir: installerchroot workingdirs
#
# Prepare network boot OS
#
	@echo "########## Prepare network boot OS"
	rsync -axc --delete $(INSTALLERCHROOT)/ $(NFSROOTDIR)/

optperl: dependencies $(PERLBIN)/perl

$(PERLBIN)/perl:
#
# Install Perl
#
	@echo "########## Install Perl"
#	chmod +x $(PERLPREPARE)
	$(PERLPREPARE)

optpython: dependencies $(PYTHONBIN)/python

$(PYTHONBIN)/python:
#
# Install Python
#
	@echo "########## Install Python"
	$(PYTHONPREPARE)

temare: optpython
#
# Install temare - very OSRC-specific
#
	cd /tmp/ ; if [ ! -d OSRC-Tapper ] ; then git clone git://wotan/OSRC-Tapper ; fi
	cd /tmp/OSRC-Tapper/src/temare ; git pull ; \
	  echo "********************** temare (from git) **********************************" ; \
	  rm -f dist/*.egg ; \
	  set -x ; \
	  $(PYTHONBIN)/python setup.py bdist_egg ; \
	  $(PYTHONBIN)/easy_install -Z dist/*.egg ; \
	  set +x

codespeed: optpython $(CODESPEED_URLSFILE)
	@echo "" ; echo "*** Misc settings ***" ; echo ""
	@echo "" ; echo " * Added 'gunicorn' to INSTALLED_APPS in $(CODESPEED_DIR)/settings.py"
	@echo "" ; echo " * You may need to edit URLs in $(CODESPEED_URLSFILE)"
	@echo "" ; echo " * Start webserver via" ;
	@echo "     cd $(CODESPEED_DIR) ; \\"
	@echo "     $(PYTHONBIN)/python ./manage.py run_gunicorn"
	@echo ""

$(CODESPEED_URLSFILE):
#
# Install codespeed
#
	$(APTGET) install mercurial
	$(APTGET) install git
	$(APTGET) install apache2
	$(APTGET) install libapache2-mod-wsgi
	$(PYTHONBIN)/easy_install pip
	-$(PYTHONBIN)/pip install pysvn
	$(PYTHONBIN)/pip install gunicorn
	-#$(PYTHONBIN)/pip install isodate
	-#$(PYTHONBIN)/pip install django
	-#$(PYTHONBIN)/pip install South
	cd $(TMP) ; rm -fr codespeed ; git clone $(CODESPEED_GITURL)
	cd $(TMP)/codespeed ; $(PYTHONBIN)/pip install .
	mkdir -p $(CODESPEED_DIR)
	cp -r $(TMP)/codespeed/example/* $(CODESPEED_DIR)/
	perl -pni -e "s/ROOT_URLCONF = 'example.urls'/ROOT_URLCONF = '$(CODESPEED_INSTANCENAME).urls'/g" $(CODESPEED_DIR)/settings.py
	perl -pni -e "s/^INSTALLED_APPS = \(/INSTALLED_APPS = ('gunicorn',/g" $(CODESPEED_DIR)/settings.py
	-#cp -r $(CODESPEED_DIR)/example.urls $(CODESPEED_DIR)/$(CODESPEED_INSTANCENAME).urls
	@echo "" ; echo "*** codespeed - collectstatic ***"; echo ""
	cd $(CODESPEED_DIR) ; $(PYTHONBIN)/python manage.py collectstatic
	@echo "" ; echo "*** codespeed - syncdb ***"; echo ""
	cd $(CODESPEED_DIR) ; $(PYTHONBIN)/python manage.py syncdb
	@echo "" ; echo "*** codespeed - migrate ***"; echo ""
	cd $(CODESPEED_DIR) ; $(PYTHONBIN)/python manage.py migrate
	@echo "" ; echo "*** codespeed - loaddata ***"; echo ""
	cd $(CODESPEED_DIR) ; $(PYTHONBIN)/python manage.py loaddata $(TMP)/codespeed/codespeed/fixtures/testdata.json

nfsserver:
#
# Install an NFS server and export $(DATATAPPER)
#
	@echo "########## Install an NFS server and export $(DATATAPPER)"
	$(APTGET) install nfs-kernel-server
	-# find out IP in a clever way, hopefully not too clever...
	for IP in $$(ip addr | awk '/inet / {print $$2}'); do echo "$(DATATAPPER) $$IP(fsid=0,no_root_squash,no_subtree_check,rw,insecure) 192.168.1.0/255.255.255.0(fsid=0,no_root_squash,no_subtree_check,rw)" >> /etc/exports ; done
	-# later append: 192.168.1.0/255.255.255.0(fsid=0,no_root_squash,no_subtree_check,rw)
	/etc/init.d/nfs-kernel-server restart

mysql:
	$(APTGET) install mysql-server

apache2:
	$(APTGET) install apache2
	$(APTGET) install libapache2-mod-fcgid

libdeps:
#
# Prepare packages
#
	@echo "########## install some build dependencies"
	$(APTGET) update
	$(APTGET) install debootstrap
	$(APTGET) install patch
	$(APTGET) install makepatch
	$(APTGET) install curl
	$(APTGET) install wget
	$(APTGET) install rsync
	$(APTGET) install gcc
	$(APTGET) install perl-modules
	$(APTGET) install libsqlite3-dev
	$(APTGET) install expect
	$(APTGET) install expect-dev
	$(APTGET) install libssl-dev
	-$(APTGET) install libmysqlclient-dev
	-$(APTGET) install libmysqlclient15-dev
	$(APTGET) install libsqlite3-0
	$(APTGET) install sqlite3
	$(APTGET) install libxml2
	$(APTGET) install libxml2-dev
	$(APTGET) install libexpat1
	$(APTGET) install libexpat1-dev

opentftpd:
#
# Prepare CPAN libs
#
	cd $(TMP)
	curl -z$(TMP)/opentftpd.tgz -L $(OPENTFTPDURL) -o $(TMP)/opentftpd.tgz
	rm -fr $(TMP)/opentftp
	cd $(TMP) ; tar xzf opentftpd.tgz
	cd $(TMP)/opentftp/ ; g++ opentftpd.cpp -lpthread -o opentftpd
	-cp /opt/opentftp/opentftpd /opt/opentftp/opentftpd_`date +%Y%m%d%H%M`
	rsync -rc $(TMP)/opentftp/ /opt/opentftp/
	cp $(STARTERKITDIR)/etc/opentftp.ini /etc/
	cp $(STARTERKITDIR)/etc/init.d/opentftp /etc/init.d/
	$(INSSERV) opentftp || $(UPDATERC) opentftp defaults
	-# We do not use atftp anymore as it can not serve big files (like images)
	-# This fragile opentftpd fiddling is not nice, we know...
	-/etc/init.d/atftpd stop
	sleep 2
	-/etc/init.d/opentftp stop
	sleep 2
	/etc/init.d/opentftp start

# OSRC specific: hardware inventory DB synced into scheduler db
# TODO: hardwaredb changes trigger "ssh root@tapper -c make hwdbsync"
hwdbsync:
	$(PREPAREPERLMODULE) Tapper::HardwareDB
	$(PERLBIN)/hardwaredb_transfer

perlpkgs:
#
# Prepare CPAN libs
#
	@echo "########## Prepare CPAN libs"
	-# File::Slurp 9999.14 from 22 Mar 2011 introduced silly test bug, force install.
	$(CPAN_INSTALL) -f File::Slurp
	-# work around forgotten dependency in T::R::Web 3.0.4
	$(CPAN_INSTALL) File::Copy::Recursive
	-# fails sometimes in non-critical tests
	$(CPAN_INSTALL) -f Test::WWW::Mechanize
	-#TWMC_TEST_PORT=9876 $(CPAN_INSTALL) -f Test::WWW::Mechanize::Catalyst
	-# work around missing deps in HTML::FormFu
	$(CPAN_INSTALL) -f IO::Interactive
	-# non problematic rest
	$(CPAN_INSTALL) DBI
	$(CPAN_INSTALL) DBD::mysql
	$(CPAN_INSTALL) DBD::SQLite
	$(CPAN_INSTALL) Module::Install::Catalyst

databases:
#
# Prepare mysql-server
#
	@echo "########## Prepare mysql-server"
	-echo "create user tapper identified by 'tapper'; \
               create database reportsdb; \
               grant all on reportsdb.* to tapper@'%'; \
               create database testrundb;\
               grant all on testrundb.* to tapper@'%';" | \
	mysql -uroot -p$${MYSQL_ROOT_PW}
	-yes | $(PERL) $(PERLBIN)/tapper-db-deploy init --db ReportsDB
	-yes | $(PERL) $(PERLBIN)/tapper-db-deploy init --db TestrunDB
	@echo "(Newqueue fails if already exists. Ignore.)"
	-$(PERL) $(PERLBIN)/tapper-testrun newqueue --name AdHoc --prio 1000

examplemachines:
#
# Add known test machines
#
	@echo "########## Add known test machines"
	-$(PERL) $(PERLBIN)/tapper-testrun newhost --name $(TESTMACHINE1) --active
	-$(PERL) $(PERLBIN)/tapper-testrun newhost --name $(TESTMACHINE2) --active
	-$(PERL) $(PERLBIN)/tapper-testrun newhost --name $(TESTMACHINE3) --active
#
# Add machine features (no public frontend tool available)
#
	@echo "########## Add machine features"
# machine1
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE1)'),     'mem',  4096);" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE1)'),   'cores',     4);" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE1)'),  'vendor', 'AMD');" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE1)'), 'has_ecc',     1);" | mysql testrundb -utapper -ptapper
# machine2
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE2)'),     'mem',  2048);" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE2)'),   'cores',     2);" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE2)'),  'vendor', 'AMD');" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE2)'), 'has_ecc',     0);" | mysql testrundb -utapper -ptapper
# machine3
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE3)'),     'mem',  2048);" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE3)'),   'cores',     2);" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE3)'),  'vendor', 'AMD');" | mysql testrundb -utapper -ptapper
	-echo "insert into host_feature(host_id, entry, value)  values ((select id from host where name = '$(TESTMACHINE3)'), 'has_ecc',     0);" | mysql testrundb -utapper -ptapper

initialdata:
#
# Add some initial data to have a better looking webgui
#
	@echo "########## Add machine features"
	-echo "insert into user (name, login)  values ('Tapper Default User', 'tapper');" | mysql testrundb -utapper -ptapper
	-echo "insert into user (name, login)  values ('A Typical Tapper User', 'root');" | mysql testrundb -utapper -ptapper
	-echo "insert into topic (name, description)  values ('Misc', 'What does not fit into other topics');" | mysql testrundb -utapper -ptapper
	-echo "insert into topic (name, description)  values ('Software', 'Non-kernel software, like libraries, programs');" | mysql testrundb -utapper -ptapper
	-echo "insert into topic (name, description)  values ('Benchmark', 'Collection of values');" | mysql testrundb -utapper -ptapper
	-echo "insert into topic (name, description)  values ('Distribution', 'OS or Compiler distribution');" | mysql testrundb -utapper -ptapper

tapperconfig: log4perlconfig /etc/tapper.cfg

/etc/tapper.cfg:
#
# Install Tapper config
#
	@echo "########## Install Tapper config if not exists"
	if [ ! -e /etc/tapper.cfg ] ; then \
	  cp $(STARTERKITDIR)/etc/tapper.cfg /etc/ ; \
          export IP=$$(ip addr | awk '/inet / {print $$2}'|grep -v 127.0.0|cut -d/ -f1) ; \
            perl -i -pne 'my $$ip=$$ENV{IP}; s/__TAPPER_HOST_IP__/$$ip/g' /etc/tapper.cfg ; \
        fi

log4perlconfig: /etc/log4perl.cfg

/etc/log4perl.cfg:
#
# copy basic Log4Perl config
#
	@echo "########## Install Log4perl config if not exists"
	if [ ! -e /etc/log4perl.cfg ] ; then \
	  cp $(STARTERKITDIR)/etc/log4perl.cfg /etc/ ; \
	fi

receiver: workingdirs dependencies tapperconfig libdeps mysql optperl
#
# start scripts for reports-receiver
#
	@echo "########## start scripts for reports-receiver"
	$(PREPAREPERLMODULE) Tapper::Reports::Receiver
	cp $(STARTERKITDIR)/etc/init.d/tapper_reports_receiver_daemon /etc/init.d/
	$(INSSERV) tapper_reports_receiver_daemon || $(UPDATERC) tapper_reports_receiver_daemon defaults
	-/etc/init.d/tapper_reports_receiver_daemon stop
	sleep 5
	/etc/init.d/tapper_reports_receiver_daemon start
	sleep 2

cli: workingdirs dependencies tapperconfig libdeps optperl
	$(PREPAREPERLMODULE) Tapper::CLI

# OSRC specific - do not expect that on CPAN
hardwaredb: workingdirs dependencies tapperconfig libdeps optperl
	$(PREPAREPERLMODULE) Tapper::HardwareDB

harness: workingdirs dependencies tapperconfig libdeps optperl
	$(PREPAREPERLMODULE) Tapper::TAP::Harness

schema: workingdirs dependencies tapperconfig libdeps optperl
	$(PREPAREPERLMODULE) Tapper::Schema

schema-upgrade: schema cli
	-$(PERL) $(PERLBIN)/tapper-db-deploy upgrade --db ReportsDB
	-$(PERL) $(PERLBIN)/tapper-db-deploy upgrade --db TestrunDB
	for DAEMON in \
	  /etc/init.d/tapper_reports_receiver_daemon \
	  /etc/init.d/tapper_reports_api_daemon \
	  /etc/init.d/tapper_action_daemon \
	  /etc/init.d/tapper_mcp_daemon \
	  /etc/init.d/apache2 ; \
	do \
	  if [ -e $$DAEMON ] ; then $$DAEMON stop ; $$DAEMON start ; fi ; \
	done
	-### Explicitely do *not* restart tapper_mcp_msg_receiver_daemon!
	-### This has to be done manually to ensure a human thinks about it.

testplan: workingdirs dependencies tapperconfig libdeps optperl
	$(PREPAREPERLMODULE) Tapper::Testplan
	-# allowed to fail to relax this OSRC specific part for others
	-cp /data/tapper/live/testplan/utils/osrc/crontab  /etc/cron.d/tapper-testplan-osrc
	-cp /data/tapper/live/testplan/utils/topic/crontab /etc/cron.d/tapper-testplan-topic

api: workingdirs dependencies tapperconfig libdeps mysql optperl
#
# start scripts for reports-api
#
	@echo "########## start scripts for reports-api"
	$(PREPAREPERLMODULE) Tapper::Reports::API
	cp $(STARTERKITDIR)/etc/init.d/tapper_reports_api_daemon /etc/init.d/
	$(INSSERV) tapper_reports_api_daemon || $(UPDATERC) tapper_reports_api_daemon defaults
	/etc/init.d/tapper_reports_api_daemon restart

action: workingdirs dependencies tapperconfig libdeps mysql optperl
#
# start scripts for Tapper::Action
#
	@echo "########## start scripts for Tapper::Action"
	$(PREPAREPERLMODULE) Tapper::Config
	$(PREPAREPERLMODULE) Tapper::Action
	cp $(STARTERKITDIR)/etc/init.d/tapper_action_daemon /etc/init.d/
	chmod 755 /etc/init.d/tapper_action_daemon
	$(INSSERV) tapper_action_daemon || $(UPDATERC) tapper_action_daemon defaults
	/etc/init.d/tapper_action_daemon restart

mcp: workingdirs dependencies tapperconfig libdeps mysql optperl
#
# start scripts for MCP
#
	@echo "########## start scripts for MCP"
	-# Proc::ProcessTable 0.45 (needed by App::Daemon) has problems using threads (undefined symbol pthread_once)
	$(CPAN_INSTALL) D/DU/DURIST/Proc-ProcessTable-0.44.tar.gz
	$(CPAN_INSTALL) App::Daemon
	$(PREPAREPERLMODULE) Tapper::Schema
	$(PREPAREPERLMODULE) Tapper::Producer
	$(PREPAREPERLMODULE) Tapper::MCP
	cp $(STARTERKITDIR)/etc/init.d/tapper_mcp_daemon /etc/init.d/
	$(INSSERV) tapper_mcp_daemon || $(UPDATERC) tapper_mcp_daemon defaults
	-/etc/init.d/tapper_mcp_daemon stop
	-ps aux | grep -v awk | grep -v grep | awk '/mcp-daemon/ {print $$2}' | xargs kill $$1
	/etc/init.d/tapper_mcp_daemon start

mcpmsgreceiver: workingdirs dependencies tapperconfig libdeps mysql optperl
#
# start scripts for MCP Message Receiver
#
	@echo "########## start scripts for MCP Message Receiver"
	-# force latest schema
	$(PREPAREPERLMODULE) Tapper::Schema
	$(PREPAREPERLMODULE) Tapper::MCP::MessageReceiver
	cp $(STARTERKITDIR)/etc/init.d/tapper_mcp_msg_receiver_daemon /etc/init.d/
	$(INSSERV) tapper_mcp_msg_receiver_daemon || $(UPDATERC) tapper_mcp_msg_receiver_daemon defaults
	/etc/init.d/tapper_mcp_msg_receiver_daemon restart

web: workingdirs dependencies tapperconfig libdeps mysql apache2 optperl testplan
#
# start scripts for web frontend
#
	@echo "########## start scripts for web frontend"
	$(PREPAREPERLMODULE) Module::Install::Catalyst
	$(PREPAREPERLMODULE) Tapper::Reports::Web
	cp $(STARTERKITDIR)/usecases/kernel_build.mpc $(USECASEDIR)/
	cp $(STARTERKITDIR)/usecases/tests.yml   $(USECASEDIR)/
	cp $(STARTERKITDIR)/etc/apache2/conf.d/tapper_reports_web.conf /etc/apache2/conf.d/
	ln -sf $(METAREPORTSDIR) $$(dirname $$($(PERLBIN)/perldoc -l Tapper::Reports::Web))/Web/root/tapper/static/
	/etc/init.d/apache2 restart

# We get mental on this topic!
#
# Sometimes "atftp" stops working with big files.
#
# Then we use "opentftp", which gets broken exactly once we were happy
# with it.
#
# So just choose one of those two here that works for you:
#   atftp
#   opentftpd
tftpserver: opentftpd

atftp:
#
# Install an TFTP server and export $(DATATAPPER)
#
	@echo "########## Install an NFS server and export $(DATATAPPER)"
	$(APTGET) install atftpd
	-# symlink Debian specific dir to keep all files in our central data dir
	-rm -rf /srv/tftp
	-ln -sf $(TFTPBOOTDIR) /srv/tftp
	-ln -sf $(TFTPBOOTDIR) $(TFTPBOOTDIR)/tftpboot
	cp $(STARTERKITDIR)/tftpboot/bzImage $(TFTPBOOTDIR)/
	/etc/init.d/atftpd restart

testreport: receiver
# test report
	OURHOSTNAME=$$(perl -MSys::Hostname -e 'print Sys::Hostname::hostname') ;\
	  OURTAPPERPORT=$$(echo $$(cat /etc/tapper.cfg |grep '^report_port:'|cut -d: -f2)) ; \
	  cat $(STARTERKITDIR)/misc/starterkit.tap | sed -e "s/TAPPERHOST/$$OURHOSTNAME/" | \
	  netcat -q5 -w5 $(TAPPER_SERVER) $$OURTAPPERPORT
# look at frontend
	@echo "*** You should now be able to view reports here:"
	@echo "    http://$(TAPPER_SERVER)/tapper"

testquery: receiver
# test report
	@echo "*** The following output comes from the query interface"
	OURTAPPERAPIPORT=$$(echo $$(cat /etc/tapper.cfg |grep '^report_api_port:'|cut -d: -f2)) ; \
	  cat $(STARTERKITDIR)/misc/starterkit.query | netcat -q5 -w5 $(TAPPER_SERVER) $$OURTAPPERAPIPORT

localsetup: workingdirs dependencies \
            tapperconfig \
            libdeps mysql apache2 optperl perlpkgs optpython \
            cli databases \
            receiver web api \
            testreport \
            nfsserver tftpserver \
            mcp mcpmsgreceiver
	@echo "**************************************************"
	@echo "*"
	@echo "* Installation done."
	@echo "*"
	@echo "* Comment about passwords:"
	@echo "*"
	@echo "* Please note that the mysql user 'tapper' got a"
	@echo "* default password which matches its default"
	@echo "* configuration in /etc/tapper.cfg."
	@echo "*"
	@echo "* If you change it, change it in config, too."
	@echo "*"
	@echo "**************************************************"

tapperhost: intro dependencies \
            installerchroot baseimages nfsrootdir clientpkgs \
            localsetup
	@echo "**************************************************"
	@echo "*"
	@echo "* Installation done."
	@echo "*"
	@echo "* Comment about passwords:"
	@echo "*"
	@echo "* The created nfsroot and the baseimage that"
	@echo "* are both used during automatic installation and"
	@echo "* test running got default passwords, respectively."
	@echo "*"
	@echo "* Change them with 'chroot' and 'passwd' if you"
	@echo "* use them in non trusted environment."
	@echo "*"
	@echo "**************************************************"

engage: tapperhost

so: engage

it:
	@echo "Make it so!"

upgrade: web cli api receiver testplan schema-upgrade
	@echo "Upgraded the most usual components:"
	@echo ""
	@echo " - web"
	@echo " - cli"
	@echo " - api"
	@echo " - receiver"
	@echo " - testplan"
	@echo " - schema-upgrade"
	@echo ""
	@echo ""
	@echo "You might want to additionally install special targets:"
	@echo ""
	@echo " - mcp            (central automation layer, if used)"
	@echo " - action         (sub component of automation, if used)"
	@echo " - mcpmsgreceiver (message receiver -> rarely needs update)"
	@echo " - clientpkgs     (toolchain for test execution)"
	@echo " - baseimages     (OS images for virtualization)"
	@echo " - nfsroot++      (upgrade NFS root OS for network boot)"
	@echo ""
