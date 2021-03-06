#
# Copyright (C) 2006-2014 OpenWrt.org
# Copyright 2010 Vertical Communications
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=e2fsprogs
PKG_VERSION:=1.43.3
PKG_HASH:=ce8ef1bbb0d4730f170167284fda156ac9d6bf18db2750eb94af619a81b19927
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@SF/e2fsprogs

PKG_BUILD_DEPENDS:=util-linux
PKG_INSTALL:=1

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/e2fsprogs/Default
  URL:=http://e2fsprogs.sourceforge.net/
  SUBMENU:=Filesystem
endef

define Package/e2fsprogs
$(call Package/e2fsprogs/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Ext2/3/4 filesystem utilities
  DEPENDS:=+libuuid +libext2fs
endef

define Package/e2fsprogs/description
 This package contains essential ext2 filesystem utilities which consists of
 e2fsck, mke2fs and most of the other core ext2 filesystem utilities.
endef

define Package/libext2fs
$(call Package/e2fsprogs/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=ext2/3/4 filesystem library
  DEPENDS:=+librt
endef

define Package/libext2fs/description
 libext2fs is a library which can access ext2, ext3 and ext4 filesystems.
endef

define Package/tune2fs
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem tune utility
  DEPENDS:= +e2fsprogs
endef

define Package/resize2fs
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem resize utility
  DEPENDS:= +e2fsprogs
endef

define Package/badblocks
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem badblocks utility
  DEPENDS:= +e2fsprogs
endef

define Package/dumpe2fs
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem information dumping utility
  DEPENDS:= +e2fsprogs
endef

define Package/e2freefrag
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem free space fragmentation information utility
  DEPENDS:= +e2fsprogs
endef

define Package/filefrag
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem file fragmentation report utility
  DEPENDS:= +e2fsprogs
endef

define Package/debugfs
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem debugger
  DEPENDS:= +e2fsprogs
endef

define Package/chattr
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem chattr utility
  DEPENDS:= +e2fsprogs
endef

define Package/lsattr
$(call Package/e2fsprogs)
  TITLE:=Ext2 Filesystem lsattr utility
  DEPENDS:= +e2fsprogs
endef

TARGET_CFLAGS += $(FPIC) -ffunction-sections -fdata-sections

CONFIGURE_VARS += \
	ac_cv_lib_pthread_sem_init=no

CONFIGURE_ARGS += \
	--disable-testio-debug \
	--enable-elf-shlibs	\
	--disable-libuuid	\
	--enable-libblkid	\
	--disable-defrag	\
	--disable-uuidd		\
	--disable-tls		\
	--disable-nls		\
	--disable-rpath

define Build/Prepare
	$(call Build/Prepare/Default)
	$(CP) $(SCRIPT_DIR)/config.{guess,sub} $(PKG_BUILD_DIR)/config/
endef

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)/util \
		BUILD_CC="$(HOSTCC)" \
		CFLAGS="" \
		CPPFLAGS="" \
		LDFLAGS="" \
		subst
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		BUILD_CC="$(HOSTCC)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		LIBBLKID="$(PKG_BUILD_DIR)/lib/libblkid.a -luuid" \
		all
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_BUILD_DIR)/lib/ext2fs/ext2fs.pc $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_BUILD_DIR)/lib/et/com_err.pc $(1)/opt/lib/pkgconfig

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_BUILD_DIR)/lib/libext2fs.{so,a}* $(1)/opt/lib
	$(CP) $(PKG_BUILD_DIR)/lib/libcom_err.{so,a}* $(1)/opt/lib

	$(INSTALL_DIR) $(1)/opt/include/ext2fs
	$(CP) $(PKG_BUILD_DIR)/lib/ext2fs/*.h $(1)/opt/include/ext2fs
	$(INSTALL_DIR) $(1)/opt/include/et
	$(CP) $(PKG_BUILD_DIR)/lib/et/*.h $(1)/opt/include/et
endef

define Package/e2fsprogs/conffiles
/opt/etc/e2fsck.conf
endef

define Package/e2fsprogs/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/e2fsck $(1)/opt/sbin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/mke2fs $(1)/opt/sbin/
	$(LN) mke2fs $(1)/opt/sbin/mkfs.ext2
	$(LN) mke2fs $(1)/opt/sbin/mkfs.ext3
	$(LN) mke2fs $(1)/opt/sbin/mkfs.ext4
	$(LN) e2fsck $(1)/opt/sbin/fsck.ext2
	$(LN) e2fsck $(1)/opt/sbin/fsck.ext3
	$(LN) e2fsck $(1)/opt/sbin/fsck.ext4
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libe2p.so.* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_DATA) ./files/e2fsck.conf $(1)/opt/etc/e2fsck.conf
endef

define Package/libcom_err/install
endef

define Package/libext2fs/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/libext2fs.so.* \
		$(PKG_INSTALL_DIR)/opt/lib/libcom_err.so.* \
		$(1)/opt/lib/
endef

define Package/libext2fs/install_lib
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_BUILD_DIR)/lib/ext2fs/libext2fs.a $(1)/opt/lib/libext2fs_pic.a
endef

define Package/tune2fs/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/tune2fs $(1)/opt/sbin/
	$(LN) tune2fs $(1)/opt/sbin/findfs
endef

define Package/resize2fs/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/resize2fs $(1)/opt/sbin/
endef

define Package/badblocks/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/badblocks $(1)/opt/sbin/
endef

define Package/dumpe2fs/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/dumpe2fs $(1)/opt/sbin/
endef

define Package/e2freefrag/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/e2freefrag $(1)/opt/sbin/
endef

define Package/filefrag/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/filefrag $(1)/opt/sbin/
endef

define Package/debugfs/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/lib
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/debugfs $(1)/opt/sbin/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libss.so.* $(1)/opt/lib/
endef

define Package/chattr/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/chattr $(1)/opt/bin/
endef

define Package/lsattr/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/lsattr $(1)/opt/bin/
endef

$(eval $(call BuildPackage,e2fsprogs))
$(eval $(call BuildPackage,libext2fs))
$(eval $(call BuildPackage,tune2fs))
$(eval $(call BuildPackage,resize2fs))
$(eval $(call BuildPackage,badblocks))
$(eval $(call BuildPackage,dumpe2fs))
$(eval $(call BuildPackage,e2freefrag))
$(eval $(call BuildPackage,filefrag))
$(eval $(call BuildPackage,debugfs))
$(eval $(call BuildPackage,chattr))
$(eval $(call BuildPackage,lsattr))
