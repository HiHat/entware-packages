#
# Copyright (C) 2016 Velocloud Inc.
# Copyright (C) 2016 Aleksander Morgado <aleksander@aleksander.es>
#
# This is free software, licensed under the GNU General Public License v2.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=libqmi
PKG_VERSION:=1.34.0
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://gitlab.freedesktop.org/mobile-broadband/libqmi.git
PKG_SOURCE_VERSION:=$(PKG_VERSION)
PKG_MIRROR_HASH:=65ee91b81c6f68d908cc94a0b5d670eefa29d550ecf8ef94cb836981fbfa4c2a

PKG_BUILD_FLAGS:=gc-sections

PKG_MAINTAINER:=Nicholas Smith <nicholas@nbembedded.com>
PKG_CPE_ID:=cpe:/a:libqmi_project:libqmi

PKG_CONFIG_DEPENDS:= \
	CONFIG_LIBQMI_COLLECTION_FULL \
	CONFIG_LIBQMI_COLLECTION_BASIC \
	CONFIG_LIBQMI_COLLECTION_MINIMAL

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk
include $(INCLUDE_DIR)/meson.mk

TARGET_CFLAGS += -fno-merge-all-constants -fmerge-constants

define Package/libqmi/config
  source "$(SOURCE)/Config.in"
endef

define Package/libqmi
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:= \
    +glib2 \
    +LIBQMI_WITH_MBIM_QMUX:libmbim \
    +LIBQMI_WITH_QRTR_GLIB:libqrtr-glib
  TITLE:=Helper library to talk to QMI enabled modems
  URL:=https://www.freedesktop.org/wiki/Software/libqmi
  LICENSE:=LGPL-2.0-or-later
  LICENSE_FILES:=COPYING.LIB
endef

define Package/libqmi/description
  Helper library talk to QMI enabled modems.
  Add qmi-utils for extra utilities.
endef

define Package/qmi-utils
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libqmi
  TITLE:=Utilities to talk to QMI enabled modems
  URL:=https://www.freedesktop.org/wiki/Software/libqmi
  LICENSE:=GPL-2.0-or-later
  LICENSE_FILES:=COPYING
endef

define Package/libqmi-utils/description
  Utils to talk to QMI enabled modems
endef

MESON_ARGS += \
	-Dudev=false \
	-Dintrospection=false \
	-Dman=false \
	-Dbash_completion=false \
	-Db_lto=true \
	-Dmbim_qmux=$(if $(CONFIG_LIBQMI_WITH_MBIM_QMUX),true,false) \
	-Dqrtr=$(if $(CONFIG_LIBQMI_WITH_QRTR_GLIB),true,false) \
	-Dcollection=$(if $(CONFIG_LIBQMI_COLLECTION_MINIMAL),minimal\
		    ,$(if $(CONFIG_LIBQMI_COLLECTION_BASIC),basic,full))

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/include/libqmi-glib \
		$(1)/opt/include/

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/libqmi*.so* \
		$(1)/opt/lib/

	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/pkgconfig/qmi-glib.pc \
		$(1)/opt/lib/pkgconfig
endef

define Package/libqmi/install
	$(INSTALL_DIR) \
		$(1)/opt/lib \
		$(1)/opt/libexec

	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/libqmi*.so.* \
		$(1)/opt/lib/

	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/libexec/qmi-proxy $(1)/opt/libexec/
endef

define Package/qmi-utils/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/qmicli $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/qmi-network $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/qmi-firmware-update $(1)/opt/bin/
endef

$(eval $(call BuildPackage,libqmi))
$(eval $(call BuildPackage,qmi-utils))
