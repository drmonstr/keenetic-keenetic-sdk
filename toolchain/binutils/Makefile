#
# Copyright (C) 2006-2012 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

PKG_NAME:=binutils
PKG_VERSION:=$(call qstrip,$(CONFIG_BINUTILS_VERSION))
BIN_VERSION:=$(PKG_VERSION)

PKG_SOURCE_URL:=@GNU/binutils/
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2

ifeq ($(PKG_VERSION),2.19.1)
  PKG_MD5SUM:=09a8c5821a2dfdbb20665bc0bd680791
endif
ifeq ($(PKG_VERSION),2.20.1)
  PKG_MD5SUM:=9cdfb9d6ec0578c166d3beae5e15c4e5
endif
ifeq ($(PKG_VERSION),2.21)
  PKG_MD5SUM:=c84c5acc9d266f1a7044b51c85a823f5
endif
ifeq ($(PKG_VERSION),2.21.1)
  PKG_MD5SUM:=bde820eac53fa3a8d8696667418557ad
endif
ifeq ($(PKG_VERSION),2.22)
  PKG_MD5SUM:=ee0f10756c84979622b992a4a61ea3f5
endif

PATCH_DIR:=./patches/$(PKG_VERSION)

REAL_STAGING_DIR_HOST:=$(STAGING_DIR_HOST)

include $(INCLUDE_DIR)/toolchain-build.mk

BINUTILS_CONFIGURE:= \
	./configure \
		--prefix=$(TOOLCHAIN_DIR) \
		--build=$(GNU_HOST_NAME) \
		--host=$(GNU_HOST_NAME) \
		--target=$(REAL_GNU_TARGET_NAME) \
		--with-sysroot=$(TOOLCHAIN_DIR) \
		--disable-multilib \
		--disable-werror \
		--disable-nls \
		$(GRAPHITE_CONFIGURE) \
		$(SOFT_FLOAT_CONFIG_OPTION) \
		$(call qstrip,$(CONFIG_EXTRA_BINUTILS_CONFIG_OPTIONS)) 

ifneq ($(CONFIG_SSP_SUPPORT),)
  BINUTILS_CONFIGURE+= \
		--enable-libssp
else
  BINUTILS_CONFIGURE+= \
		--disable-libssp
endif

ifneq ($(CONFIG_EXTRA_TARGET_ARCH),)
  BINUTILS_CONFIGURE+= \
		--enable-targets=$(call qstrip,$(CONFIG_EXTRA_TARGET_ARCH_NAME))-linux-$(TARGET_SUFFIX)
endif

define Host/Prepare
	$(call Host/Prepare/Default)
	ln -snf $(PKG_NAME)-$(PKG_VERSION) $(BUILD_DIR_TOOLCHAIN)/$(PKG_NAME)
	$(CP) $(SCRIPT_DIR)/config.{guess,sub} $(HOST_BUILD_DIR)/
endef

define Host/Configure
	(cd $(HOST_BUILD_DIR); \
		$(BINUTILS_CONFIGURE) \
	);
endef

define Host/Compile
	+$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR) all
endef

define Host/Install
	mkdir -p $(TOOLCHAIN_DIR)/initial
	$(MAKE) -C $(HOST_BUILD_DIR) \
		prefix=$(TOOLCHAIN_DIR)/initial \
		install
	$(call FixupLibdir,$(TOOLCHAIN_DIR)/initial)
	$(CP) $(TOOLCHAIN_DIR)/initial/. $(TOOLCHAIN_DIR)/
	$(CP) $(TOOLCHAIN_DIR)/bin/$(REAL_GNU_TARGET_NAME)-readelf $(REAL_STAGING_DIR_HOST)/bin/readelf
endef

define Host/Clean
	rm -rf \
		$(HOST_BUILD_DIR) \
		$(BUILD_DIR_TOOLCHAIN)/$(PKG_NAME)
endef

$(eval $(call HostBuild))
