# 
# Copyright (C) 2007-2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Main makefile for the toolchain
#
# Steps:
# 1) toolchain/binutils/install
#    build & install binutils
# 2) toolchain/gcc/minimal/install
#    build & install a minimal gcc, needed for steps 3 & 4
# 3) toolchain/kernel-headers/install
#    install kernel headers, needed for step 4
# 4) toolchain/libc/headers/install
#    build & install libc headers & support files, needed for step 5
# 5) toolchain/gcc/initial/install
#    build & install an initial gcc, needed for step 6
# 6) toolchain/libc/install
#    build & install the final libc
# 7) toolchain/gcc/final/install
#    build & install the final gcc
# 8) toolchain/libc/utils/install
#    build & install libc utilities
#

curdir:=toolchain

# subdirectories to descend into
$(curdir)/builddirs := $(if $(CONFIG_GDB),gdb) $(if $(CONFIG_INSIGHT),insight) $(if $(CONFIG_EXTERNAL_TOOLCHAIN),wrapper,kernel-headers binutils gcc/minimal gcc/initial gcc/final $(LIBC)/headers $(LIBC) fortify-headers) $(if $(CONFIG_YASM),yasm)
ifdef CONFIG_USE_UCLIBC
  $(curdir)/builddirs += $(LIBC)/utils
endif
$(curdir)/builddirs-compile:=$($(curdir)/builddirs-prepare)
$(curdir)/builddirs-install:=$($(curdir)/builddirs-compile)

# builddir dependencies
ifeq ($(CONFIG_EXTERNAL_TOOLCHAIN),)
  $(curdir)/gcc/minimal/compile:=$(curdir)/binutils/install
  $(curdir)/kernel-headers/install:=$(curdir)/gcc/minimal/install
  $(curdir)/gcc/initial/prepare:=$(curdir)/gcc/minimal/prepare
  $(curdir)/gcc/final/prepare:=$(curdir)/gcc/initial/prepare

  $(curdir)/$(LIBC)/headers/compile:=$(curdir)/kernel-headers/install
  $(curdir)/gcc/initial/compile:=$(curdir)/$(LIBC)/headers/install
  $(curdir)/$(LIBC)/compile:=$(curdir)/gcc/initial/install
  $(curdir)/gcc/final/compile:=$(curdir)/$(LIBC)/install
  $(curdir)/$(LIBC)/utils/compile:=$(curdir)/gcc/final/install
  $(curdir)/$(LIBC)/prepare:=$(curdir)/$(LIBC)/headers/prepare
  $(curdir)/$(LIBC)/utils/prepare:=$(curdir)/$(LIBC)/headers/prepare
endif

ifndef DUMP_TARGET_DB
ifneq ($(ARCH),)
  $(TOOLCHAIN_DIR)/info.mk: .config
	@for dir in $(TOOLCHAIN_DIR); do ( \
		$(if $(QUIET),,set -x;) \
		mkdir -p "$$dir"; \
		cd "$$dir"; \
		ln -nsf lib lib64; \
		ln -nsf lib lib32; \
		mkdir -p stamp lib bin usr/include usr/lib ; \
	); done
	@grep GCC_VERSION $@ >/dev/null 2>&1 || $(INSTALL_DATA) $(TOPDIR)/toolchain/info.mk $@
	@touch $@
endif
endif

# prerequisites for the individual targets
$(curdir)/ := .config prereq
$(curdir)//prepare = $(STAGING_DIR)/.prepared $(TOOLCHAIN_DIR)/info.mk $(tools/stamp-install)
$(curdir)//compile = $(1)/prepare
$(curdir)//install = $(1)/compile

ifndef DUMP_TARGET_DB
$(TOOLCHAIN_DIR)/stamp/.gcc-initial_installed:
endif

$(eval $(call stampfile,$(curdir),toolchain,install,$(TOOLCHAIN_DIR)/stamp/.gcc-initial_installed,,$(TOOLCHAIN_DIR)))
$(eval $(call subdir,$(curdir)))

