#
# Copyright (C) 2008-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: -DHAVE_LINUXGPIO=OFF (uapi/linux/gpio.h >= 4.6)

include $(TOPDIR)/rules.mk

PKG_NAME:=avrdude
PKG_VERSION:=7.3
PKG_RELEASE:=4

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/avrdudes/avrdude
PKG_SOURCE_VERSION:=v$(PKG_VERSION)
PKG_MIRROR_HASH:=6533a9b6e10123d6885f4327896316219f3b9f15fbb322255f2adf4d923fcf7a

PKG_MAINTAINER:=Ted Hess <thess@kitschensync.net>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk
include $(INCLUDE_DIR)/nls.mk

define Package/avrdude
  SECTION:=utils
  CATEGORY:=Utilities
  SUBMENU:=Microcontroller programming
  TITLE:=AVR Downloader/UploaDEr
  URL:=https://github.com/avrdudes/avrdude
  DEPENDS:=+libncurses +libreadline +libusb-1.0 +libftdi1 +libelf
endef

define Package/avrdude/description
 AVRDUDE is a full featured program for programming Atmel's AVR CPU's.
endef

CMAKE_OPTIONS += \
	-DHAVE_LIBUSB=OFF \
	-DHAVE_LIBFTDI=OFF \
	-DHAVE_LIBHIDAPI=OFF \
	-DHAVE_LIBSERIALPORT=OFF \
	-DHAVE_LINUXGPIO=OFF

define Package/avrdude/conffiles
/opt/etc/avrdude.conf
endef

define Package/avrdude/install
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/avrdude.conf $(1)/opt/etc/
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/avrdude $(1)/opt/bin/
endef

$(eval $(call BuildPackage,avrdude))
