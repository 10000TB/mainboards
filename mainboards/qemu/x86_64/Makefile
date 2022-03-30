flashinitramfs.cpio: flashkernel core.cpio flashkernel54
	GO111MODULE=off u-root -o $@ \
		    -files flashkernel \
			-files flashkernel54 \
		    -files core.cpio \
		    -files /usr/bin/gzip \
	github.com/u-root/u-root/cmds/boot/pxeboot \
	github.com/u-root/u-root/cmds/core/cat \
	github.com/u-root/u-root/cmds/core/elvish \
	github.com/u-root/u-root/cmds/core/init \
	github.com/u-root/u-root/cmds/core/ip \
	github.com/u-root/u-root/cmds/core/ls \
	github.com/u-root/u-root/cmds/core/kexec \
	github.com/u-root/u-root/cmds/core/pci \
	github.com/u-root/u-root/cmds/core/wget

core.cpio: flash.config Makefile
	GO111MODULE=off u-root -o $@

flashkernel: flash.config core.cpio
	cp $< linux/.config
	echo CONFIG_CMDLINE_BOOL=y >> linux/.config
	echo CONFIG_CMDLINE_OVERRIDE=y >> linux/.config
	echo 'CONFIG_CMDLINE="noefi ip=dhcp earlyprintk=ttyS0,115200,keep console=ttyS0,115200"' >> linux/.config
	(cd linux && make olddefconfig && make -j32)
	cp linux/arch/x86/boot/bzImage $@

flashkernel54: flash54.config core.cpio
	cp $< linux54/linux/.config
	echo CONFIG_CMDLINE_BOOL=y >> linux54/linux/.config
	echo CONFIG_CMDLINE_OVERRIDE=y >> linux54/linux/.config
	echo 'CONFIG_CMDLINE="noefi ip=dhcp earlyprintk=ttyS0,115200,keep console=ttyS0,115200"' >> linux54/linux/.config
	(cd linux54/linux && make olddefconfig && make CC=x86_64-linux-gnu-gcc-9  -j32)
	cp linux54/linux/arch/x86/boot/bzImage $@

testflashkernel: flashkernel flashinitramfs.cpio
	qemu-system-x86_64 -m 8192 -kernel flashkernel -nographic -serial stdio -initrd flashinitramfs.cpio -monitor $(MONITOR) $(EXTRA)

fetch: getkernel geturoot

getkernel:
	rm -rf linux
	git clone --depth=1 -b v5.10 https://github.com/torvalds/linux

getkernel54:
	rm -rf linux54
	mkdir linux54
	(cd linux54; git clone --depth=1 -b v5.4 https://github.com/torvalds/linux)

geturoot:
	GO111MODULE=off go get -u github.com/u-root/u-root
