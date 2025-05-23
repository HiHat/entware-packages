#
# Copyright (C) 2006-2018 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=tcl
TCL_MAJOR_VERSION:=8.6
PKG_VERSION:=${TCL_MAJOR_VERSION}.14
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)$(PKG_VERSION)-src.tar.gz
PKG_SOURCE_URL:=@SF/$(PKG_NAME)
PKG_HASH:=5880225babf7954c58d4fb0f5cf6279104ce1cd6aa9b71e9a6322540e1c4de66
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)$(PKG_VERSION)
HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/$(PKG_NAME)$(PKG_VERSION)

PKG_MAINTAINER:=Joe Mistachkin <joe@mistachkin.com>
PKG_LICENSE:=TCL
PKG_LICENSE_FILES:=license.terms
PKG_CPE_ID:=cpe:/a:tcl_tk:tcl_tk

PKG_BUILD_DEPENDS:=HOST_OS_MACOS:fakeuname/host
PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
HOST_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

ifeq ($(CONFIG_HOST_OS_MACOS),y)
    include ../../utils/fakeuname/fakeuname.mk
endif

define Package/tcl
  SUBMENU:=Tcl
  SECTION:=lang
  CATEGORY:=Languages
  DEPENDS:=+libpthread +zlib
  TITLE:=The Tcl language
  URL:=https://www.tcl.tk/
endef

define Package/tcl/description
  Tcl, or Tool Command Language, is a an elegant, versatile, feature-rich,
  simple-to-learn yet very powerful industrial-strength open-source
  programming language and development platform. It is renowned for its
  stability and utility, and its emphasis on providing a cross-platform
  programming API makes it an ideal choice for an enormous variety of
  programming jobs.
endef

CONFIGURE_PATH := unix

CONFIGURE_VARS += \
	$(if $(CONFIG_HOST_OS_MACOS),PATH=$(FAKEUNAME_PATH):$(PATH)) \
	tcl_cv_strtod_unbroken=ok

CONFIGURE_ARGS += \
	--enable-threads

MAKE_PATH := unix

define Build/Prepare
	$(call Build/Prepare/Default)
	rm -rf $(PKG_BUILD_DIR)/pkgs/*
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/*.h $(1)/opt/include/

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libtcl*.{a,so*} $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/tclConfig.sh $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/tclooConfig.sh $(1)/opt/lib/
	$(SED) 's,-I/opt/include,-I$(STAGING_DIR)/opt/include,g; \
		s,-L/opt/lib,-L$(STAGING_DIR)/opt/lib,g' \
			$(1)/opt/lib/tclConfig.sh

	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/tcl.pc \
		$(1)/opt/lib/pkgconfig
	$(SED) 's,/opt/include,$$$${prefix}/include,g' $(1)/opt/lib/pkgconfig/tcl.pc
	$(SED) 's,/opt/lib,$$$${prefix}/lib,g' $(1)/opt/lib/pkgconfig/tcl.pc
endef

define Package/tcl/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so $(1)/opt/lib

	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/* $(1)/opt/bin

	$(CP) -a $(PKG_INSTALL_DIR)/opt/lib/tcl8 $(1)/opt/lib/
	$(CP) -a $(PKG_INSTALL_DIR)/opt/lib/tcl$(TCL_MAJOR_VERSION) $(1)/opt/lib/
	$(LN) tclsh$(TCL_MAJOR_VERSION) $(1)/opt/bin/tclsh
endef

define Host/Configure
	$(call Host/Configure/Default,$(1),$(2),$(CONFIGURE_PATH)/$(3))
endef

define Host/Compile
	+$(HOST_MAKE_VARS) \
	$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR)/$(MAKE_PATH) \
		$(HOST_MAKE_FLAGS) \
		$(1)
endef

define Host/Install
	$(call Host/Compile,install)
	(cd $(HOST_BUILD_PREFIX)/bin; test -f tclsh || ln -s tclsh$(TCL_MAJOR_VERSION) tclsh)
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,tcl))
