#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# XXX keep version kernel?||ppp?

include $(TOPDIR)/rules.mk

PKG_NAME:=pptpd
PKG_VERSION:=1.4.0
PKG_RELEASE:=6

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@SF/poptop
PKG_HASH:=8fcd8b8a42de2af59e9fe8cbaa9f894045c977f4d038bbd6346a8522bb7f06c0

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

PKG_CPE_ID:=cpe:/a:poptop:pptp_server

include $(INCLUDE_DIR)/package.mk

define Package/pptpd
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+ppp
  TITLE:=PopTop pptp server
  URL:=http://poptop.sourceforge.net/
  SUBMENU:=VPN
endef

CONFIGURE_ARGS += \
	--enable-bcrelay \

CONFIGURE_VARS += \
	ac_cv_header_libintl_h=no \

MAKE_FLAGS += \
	COPTS="$(TARGET_CFLAGS) -I$(STAGING_DIR)/opt/include" \
	INSTALL="install" \

define Package/pptpd/conffiles
/opt/etc/pptpd.conf
/opt/etc/ppp/options.pptpd
/opt/etc/config/pptpd
endef

define Package/pptpd/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/sbin/bcrelay \
		$(PKG_INSTALL_DIR)/opt/sbin/pptpctrl \
		$(PKG_INSTALL_DIR)/opt/sbin/pptpd \
		$(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/lib/pptpd
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pptpd/* $(1)/opt/lib/pptpd/
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_DATA) ./files/pptpd.conf $(1)/opt/etc/
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S20poptop $(1)/opt/etc/init.d/
	$(INSTALL_DIR) $(1)/opt/etc/ppp
	$(INSTALL_DATA) ./files/options.pptpd $(1)/opt/etc/ppp/
endef

$(eval $(call BuildPackage,pptpd))
