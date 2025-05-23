#
# This is free software, licensed under the GNU General Public License v2.
#
# Based on https://github.com/kyak/openwrt-packages/tree/master/lynx
#

include $(TOPDIR)/rules.mk

PKG_NAME:=lynx
PKG_VERSION:=2.9.2
PKG_RELEASE:=1

PKG_LICENSE:=GPL-2.0
PKG_MAINTAINER:=Leonid Esman <leonid.esman@gmail.com>
PKG_CPE_ID:=cpe:/a:lynx:lynx
PKG_SOURCE:=$(PKG_NAME)$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://invisible-mirror.net/archives/lynx/tarballs/ \
                https://fossies.org/linux/www/
PKG_HASH:=7374b89936d991669e101f4e97f2c9592036e1e8cdaa7bafc259a77ab6fb07ce
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

define Package/lynx
	TITLE:=Lynx is the text web browser
	SECTION:=net
	CATEGORY:=Network
	DEPENDS:=+libncursesw +zlib +libopenssl +ca-bundle
	URL:=https://lynx.invisible-island.net/
endef

define Package/lynx/description
	Classic non-graphical (text-mode) web browser
endef

CONFIGURE_ARGS += \
	--with-ssl \
	--with-build-cc="$(HOSTCC)" \
	--with-build-cflags="$(HOST_CFLAGS)" \
	--with-build-cppflags="$(HOST_CPPFLAGS)" \
	--with-build-ldflags="$(HOST_LDFLAGS)" \
	--enable-ipv6 \
	--with-screen=ncursesw \
	--enable-widec \
	--enable-charset-choice \
	--enable-nsl-fork \
	--disable-config-info \
	--disable-dired \
	--disable-trace \
	--disable-finger \
	--disable-gopher \
	--disable-news \
	--disable-wais \
	--disable-prettysrc \
	--disable-scrollbar \
	--disable-idna \
	--without-bzlib

CONFIGURE_VARS += \
	ac_cv_lib_bsd_arc4random=no \
	cf_cv_ncurses_header="ncursesw/curses.h" \
	ac_cv_path_NCURSES_CONFIG=""

define Package/lynx/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/lynx $(1)/opt/bin/
	$(INSTALL_DATA) ./files/lynx.cfg $(1)/opt/etc/
	$(INSTALL_DATA) ./files/lynx.lss $(1)/opt/etc/
endef

$(eval $(call BuildPackage,lynx))
