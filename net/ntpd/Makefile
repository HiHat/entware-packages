#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: ALTERNATIVES

include $(TOPDIR)/rules.mk

PKG_NAME:=ntp
PKG_REALVERSION:=4.2.8p17
PKG_VERSION:=$(subst p,_p,$(PKG_REALVERSION))
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-$(PKG_REALVERSION).tar.gz
PKG_SOURCE_URL:=http://www.eecis.udel.edu/~ntp/ntp_spool/ntp4/ntp-4.2/
PKG_HASH:=103dd272e6a66c5b8df07dce5e9a02555fcd6f1397bdfb782237328e89d3a866
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_REALVERSION)

PKG_LICENSE:=NTP
PKG_LICENSE_FILES:=COPYRIGHT html/copyright.html
PKG_CPE_ID:=cpe:/a:ntp:ntp

PKG_LIBTOOL_PATHS:=. sntp
PKG_CHECK_FORMAT_SECURITY:=0
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/ntpd/Default
  SUBMENU:=Time Synchronization
  SECTION:=net
  CATEGORY:=Network
  TITLE:=ISC ntp
  MAINTAINER:=Peter Wagner <tripolar@gmx.at>
  URL:=http://www.ntp.org/
  DEPENDS:=+libopenssl +libpthread +libcap
endef

define Package/ntpd/Default/description
 The ISC ntp suite is a collection of tools used to synchronize the
 system clock with remote NTP time servers and run/monitor local NTP
 servers.
endef

define Package/ntpd
$(call Package/ntpd/Default)
  DEPENDS+= +ntp-utils
  TITLE+= server
  USERID:=ntp=123:ntp=123
  ALTERNATIVES:=300:/opt/sbin/ntpd:/opt/libexec/ntpd
endef

define Package/ntpd/description
$(call Package/ntpd/Default/description)
 .
 This package contains the ntpd server.
endef

define Package/ntpdate
$(call Package/ntpd/Default)
  TITLE+=date
endef

define Package/ntpdate/description
$(call Package/ntpd/Default/description)
 .
 This package contains ntpdate.
endef

define Package/ntp-utils
$(call Package/ntpd/Default)
  TITLE+= utilities
  DEPENDS+= +libevent2-pthreads
endef

define Package/ntp-utils/description
$(call Package/ntpd/Default/description)
 .
 This package contains ntpdc, ntpq, ntptime and sntp.
endef

define Package/ntp-keygen
$(call Package/ntpd/Default)
  TITLE+=keygen
  DEPENDS+= +libevent2-core
endef

define Package/ntp-keygen/description
$(call Package/ntpd/Default/description)
 .
 This package contains the ntp-keygen.
endef

define Package/ntpd/conffiles
/opt/etc/ntp.conf
/opt/etc/config/ntpd
endef

CONFIGURE_VARS += \
	ac_cv_header_md5_h=no \
	ac_cv_lib_rt_sched_setscheduler=no \
	ac_cv_header_dns_sd_h=no \
	hw_cv_func_snprintf_c99=yes \
	hw_cv_func_vsnprintf_c99=yes \

CONFIGURE_ARGS += \
	--disable-all-clocks \
	--disable-parse-clocks \
	--without-ntpsnmpd \
	--without-lineeditlibs \
	--enable-NMEA \
	--enable-LOCAL-CLOCK \
	--enable-SHM \
	--enable-ATOM \
	--enable-linuxcaps \
	--with-yielding-select=yes \
	--with-crypto \
	$(if $(CONFIG_PACKAGE_ntp-utils),--with-sntp,--without-sntp) \
	--with-openssl-incdir="$(STAGING_DIR)/opt/include" \
	--with-openssl-libdir="$(STAGING_DIR)/opt/lib"

define Package/ntpd/install
	$(INSTALL_DIR) $(1)/opt/libexec
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ntpd/ntpd $(1)/opt/libexec/
	$(INSTALL_DIR) $(1)/opt/etc $(1)/opt/etc/config
	$(INSTALL_CONF) ./files/ntp.conf $(1)/opt/etc/
	$(INSTALL_CONF) ./files/ntpd.config $(1)/opt/etc/config/ntpd
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S77ntpd $(1)/opt/etc/init.d/
	$(INSTALL_DIR) $(1)/opt/etc/hotplug.d/iface
	$(INSTALL_BIN) ./files/ntpd.hotplug $(1)/opt/etc/hotplug.d/iface/20-ntpd
	$(INSTALL_DIR) $(1)/opt/var/spool/ntp
endef


define Package/ntpdate/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ntpdate/ntpdate $(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S77ntpdate $(1)/opt/etc/init.d/
endef

define Package/ntp-utils/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ntpdc/ntpdc $(1)/opt/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ntpq/ntpq $(1)/opt/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/util/ntptime $(1)/opt/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/sntp/sntp $(1)/opt/sbin/
endef

define Package/ntp-keygen/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/util/ntp-keygen $(1)/opt/sbin/
endef

$(eval $(call BuildPackage,ntpd))
$(eval $(call BuildPackage,ntpdate))
$(eval $(call BuildPackage,ntp-utils))
$(eval $(call BuildPackage,ntp-keygen))
