
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=nut
PKG_VERSION:=2.8.1
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=http://www.networkupstools.org/source/2.8/
PKG_HASH:=7da48ee23b1f0d8d72560bb0af84f5c5ae4dbe35452b84cb49840132e47f099c
PKG_LICENSE:=GPL-2.0-or-later GPL-3.0-or-later GPL-1.0-or-later Artistic-1.0-Perl
PKG_LICENSE_FILES:=LICENSE-GPL2 LICENSE-GPL3 COPYING
PKG_FIXUP:=autoreconf

PKG_INSTALL:=1

PKG_CONFIG_DEPENDS:= \
	CONFIG_NUT_DRIVER_SNMP \
	CONFIG_NUT_DRIVER_USB \
	CONFIG_NUT_DRIVER_SERIAL \
	CONFIG_NUT_DRIVER_NEON \
	CONFIG_NUT_SSL \
	CONFIG_PACKAGE_nut-web-cgi

include $(INCLUDE_DIR)/package.mk

define Package/nut/Default
	SECTION:=net
	CATEGORY:=Network
	URL:=http://www.networkupstools.org/
	TITLE:=Network UPS Tools (NUT)
endef

define Package/nut/description/Default
Network UPS Tools (NUT) is a client/server monitoring system that
allows computers to share uninterruptible power supply (UPS) and
power distribution unit (PDU) hardware. Clients access the hardware
through the server, and are notified whenever the power status
changes.
endef

define Package/nut
	$(call Package/nut/Default)
	MENU:=1
endef

define Package/nut/description
$(call Package/nut/description/Default)
endef

define Package/nut/config
	source "$(SOURCE)/Config.in"
endef

define Package/nut/install
	true
endef

define Package/nut-server/install
	# Server portion
	$(INSTALL_DIR) $(1)/opt/etc/nut
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/share/nut
	$(INSTALL_BIN) ./files/S15upsd $(1)/opt/etc/init.d/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/upsd $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/share/nut/cmdvartab $(1)/opt/share/nut/
#	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/ups.conf.sample $(1)/opt/etc/nut/ups.conf
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upsd.conf.sample $(1)/opt/etc/nut/upsd.conf
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upsd.users.sample $(1)/opt/etc/nut/upsd.users
	# Driver common portion
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/upsdrvctl $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/share/nut/driver.list $(1)/opt/share/nut/
endef

define Package/nut-common
	$(call Package/nut/Default)
	TITLE+= (common)
	DEPENDS:= nut \
		+NUT_DRIVER_SNMP:libnetsnmp \
		+NUT_DRIVER_USB:libusb-compat \
		+NUT_DRIVER_NEON:libneon \
		+NUT_SSL:libopenssl
endef

define Package/nut-common/description
$(call Package/nut/description/Default)
This package contains the common files.
endef

define Package/nut-common/conffiles
/opt/etc/nut/nut.conf
endef

define Package/nut-common/install
	$(INSTALL_DIR) $(1)/opt/etc/nut
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libupsclient.so* $(1)/opt/lib/
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/nut.conf.sample $(1)/opt/etc/nut/nut.conf
endef

define Package/nut-server
	$(call Package/nut/Default)
	TITLE+= (server)
	DEPENDS:=nut +nut-common
	USERID:=nut=113:nut=113
endef

define Package/nut-server/description
$(call Package/nut/description/Default)
upsd is responsible for serving the data from the drivers to the
clients. It connects to each driver and maintains a local cache of the
current state. Queries from the clients are served from this cache, so
delays are minimal. This program is essential, and must be running at
all times to actually make any use out of the drivers and clients.
endef

define Package/nut-server/conffiles
/opt/etc/nut/upsd.conf
/opt/etc/nut/upsd.users
/opt/etc/nut/ups.conf
endef

define Package/nut-upsmon
  $(call Package/nut/Default)
	TITLE+= (monitor)
	DEPENDS:=nut +nut-common
	USERID:=nutmon=114:nutmon=114
endef

define Package/nut-upsmon/description
$(call Package/nut/description/Default)
upsmon is the client process that is responsible for the most important
part of UPS monitoring--shutting down the system when the power goes
out. It can call out to other helper programs for notification purposes
during power events. upsmon can monitor multiple systems using a single
process. Every UPS that is defined in the upsmon.conf configuration file
is assigned a power value and a type (slave or master).
endef

define Package/nut-upsmon/conffiles
/opt/etc/nut/upsmon.conf
endef

define Package/nut-upsmon/install
	$(INSTALL_DIR) $(1)/opt/etc/nut
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S15upsmon $(1)/opt/etc/init.d/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/upsmon $(1)/opt/sbin/
#	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upsmon.conf.sample $(1)/opt/etc/nut/upsmon.conf
endef

define Package/nut-upsmon-sendmail-notify
	$(call Package/nut/Default)
	TITLE+= (upsmon with notifications via sendmail)
	DEPENDS:=nut +nut-upsmon
	CONFLICTS:=nut-upssched
endef

define Package/nut-upsmon-sendmail-notify/description
$(call Package/nut/description/Default)
upsmon with default notification via sendmail
endef

#define Package/nut-upsmon-sendmail-notify/install
#	$(INSTALL_DIR) $(1)/usr/bin $(1)/etc/uci-defaults
#	$(INSTALL_BIN) ./files/nut-sendmail-notify $(1)/usr/bin/
#	$(INSTALL_DATA) ./files/nut-sendmail-notify.default $(1)/etc/uci-defaults/nut-sendmail-notify
#endef

define Package/nut-upsc
	$(call Package/nut/Default)
	TITLE+= (upsc command)
	DEPENDS:=nut +nut-common
endef

define Package/nut-upsc/description
$(call Package/nut/description/Default)
upsc is provided as a quick way to poll the status of a UPS server. It
can be used inside shell scripts and other programs that need UPS data
but do not want to include the full interface.
endef

define Package/nut-upsc/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/upsc $(1)/opt/bin
endef

define Package/nut-upslog
	$(call Package/nut/Default)
	TITLE+= (logging client)
	DEPENDS:=nut +nut-common
endef

define Package/nut-upslog/description
$(call Package/nut/description/Default)
upslog is a daemon that will poll a UPS at periodic intervals, fetch the
variables that interest you, format them, and write them to a file.
endef

define Package/nut-upslog/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/upslog $(1)/opt/bin/
endef

define Package/nut-upscmd
	$(call Package/nut/Default)
	TITLE+= (controller)
	DEPENDS:=nut +nut-common
endef

define Package/nut-upscmd/description
$(call Package/nut/description/Default)
upscmd allows you to invoke "instant commands" in your UPS hardware. Not
all hardware supports this, so check the list with -l to see if anything
will work on your equipment. On hardware that supports it, you can use
this program to start and stop battery tests, invoke a front panel test
(beep!), turn the load on or off, and more.
endef

define Package/nut-upscmd/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/upscmd $(1)/opt/bin/
endef

define Package/nut-upsrw
	$(call Package/nut/Default)
	TITLE+= (variable editor)
	DEPENDS:=nut +nut-common
endef

define Package/nut-upsrw/description
$(call Package/nut/description/Default)
upsrw allows you to view and change the read/write variables inside your
UPS. It sends commands via the upsd to your driver, which configures the
hardware for you. The list of variables that allow you to change their
values is based on the capabilities of your UPS equipment. Not all
models support this feature. Typically, cheaper hardware does not
support any of them.
endef

define Package/nut-upsrw/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/upsrw $(1)/opt/bin/
endef

define Package/nut-upssched
	$(call Package/nut/Default)
	TITLE+= (helper for triggering events from upsmon)
	DEPENDS:=nut +nut-common +nut-upsmon
endef

define Package/nut-upssched/description
$(call Package/nut/description/Default)
upssched was created to allow users to execute programs at times relative
to events being monitored by upsmon. The original purpose was to allow
for a shutdown to occur after some fixed period on battery, but there are
other uses that are possible.
You can alternatively write your own script and save some space.
endef

define Package/nut-upssched/conffiles
/opt/etc/nut/upssched.conf
endef

define Package/nut-upssched/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/etc/nut
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/upssched-cmd $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/upssched $(1)/opt/sbin/
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upssched.conf.sample $(1)/opt/etc/nut/upssched.conf
endef

define Package/nut-web-cgi
	$(call Package/nut/Default)
	TITLE+= Web CGI interface
	DEPENDS:=nut +nut-common +PACKAGE_nut-web-cgi:libgd +PACKAGE_nut-web-cgi:libtiff
endef

define Package/nut-web-cgi/description
The CGI programs are clients that run through your web server.
They allow you to see UPS status and perform certain administrative
commands from any web browser. Javascript and cookies are not required.
endef

define Package/nut-web-cgi/conffiles
/opt/etc/nut/hosts.conf
/opt/etc/nut/upsset.conf
/opt/etc/nut/upsstats.html
/opt/etc/nut/upsstats-single.html
/opt/etc/httpd.conf
endef

define Package/nut-web-cgi/install
	$(INSTALL_DIR) $(1)/opt/share/www/cgi-bin $(1)/opt/share/www/nut
	$(INSTALL_DIR) $(1)/opt/etc/nut
	$(CP) $(PKG_INSTALL_DIR)/opt/html/* $(1)/opt/share/www/nut/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/cgi-bin/* $(1)/opt/share/www/cgi-bin/
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/hosts.conf.sample $(1)/opt/etc/nut/hosts.conf
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upsstats.html.sample $(1)/opt/etc/nut/upsstats.html
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upsstats-single.html.sample $(1)/opt/etc/nut/upsstats-single.html
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/nut/upsset.conf.sample $(1)/opt/etc/nut/upsset.conf
endef

define Package/nut-avahi-service
	$(call Package/nut/Default)
	TITLE+= (Avahi service)
	DEPENDS:=nut +avahi-daemon
endef

define Package/nut-avahi-service/description
This package contains the service definition for announcing the
attached UPS via mDNS/DNS-SD.
endef

define Package/nut-avahi-service/conffiles
/opt/etc/avahi/services/nut.service
endef

define Package/nut-avahi-service/install
	$(INSTALL_DIR) $(1)/opt/etc/avahi/services
	$(INSTALL_CONF) ./files/nut.service $(1)/opt/etc/avahi/services/
endef

# Dealing with all of the drivers is very repetitive, but the previous
# maintainer had a neat solution which just needed some tweaking.
define DriverPackage
        define Package/nut-driver-$(2)
		$(call Package/nut/Default)
		TITLE:=$(2) (NUT $(1) driver)
		DEPENDS:=nut +nut-server
		$(if $(filter $(1),snmp),DEPENDS+= @NUT_DRIVER_SNMP)
		$(if $(filter $(1),usb),DEPENDS+= @NUT_DRIVER_USB)
		$(if $(filter $(1),serial),DEPENDS+= @NUT_DRIVER_SERIAL)
                $(if $(filter $(1),neon),DEPENDS+= @NUT_DRIVER_NEON)
        endef
	# Deliberately empty description in order to trigger a build failure.
	# It should be overridden by the list below, and when updating to a
	# new version of nut we will need to provide descriptions for any new
	# drivers.
        define Package/nut-driver-$(2)/description

        endef
        define Package/nut-driver-$(2)/install
		$(INSTALL_DIR) $$(1)/opt/lib/nut
		$(CP) $$(PKG_INSTALL_DIR)/opt/lib/nut/$(2) $$(1)/opt/lib/nut/
		$(if $(filter $(2),clone),$(CP) $$(PKG_INSTALL_DIR)/opt/lib/nut/$(2)-outlet $$(1)/opt/lib/nut/)
        endef
endef
define DriverDescription
        define Package/nut-driver-$(2)/description
		$(3)
        endef
endef
# These lists are lifted *directly* from drivers/Makefile.am in the nut
# source tree. This it to make it simpler to keep in sync when updating
# to a newer version of nut. Do not edit this manually.
#
# DO NOT EDIT (except to update with a fresh cut/paste)!
SERIAL_DRIVERLIST = al175 bcmxcp belkin belkinunv bestfcom	\
 bestfortress bestuferrups bestups dummy-ups etapro everups	 \
 gamatronic genericups isbmex liebert liebert-esp2 masterguard metasys	\
 mge-utalk microdowell mge-shut oneac optiups powercom rhino	 \
 safenet skel solis tripplite tripplitesu upscode2 victronups powerpanel \
 blazer_ser clone clone-outlet ivtscd apcsmart apcsmart-old apcupsd-ups riello_ser	\
 nutdrv_qx
SNMP_DRIVERLIST = snmp-ups
USB_LIBUSB_DRIVERLIST = usbhid-ups bcmxcp_usb tripplite_usb \
 blazer_usb richcomm_usb riello_usb \
 nutdrv_atcl_usb \
 nutdrv_qx
NEONXML_DRIVERLIST = netxml-ups
# END: DO NOT EDIT!
SERIAL_DRIVERLIST_IGNORE:=skel clone-outlet nutdrv_qx
# nutdrv_qx can be either USB or serial. Given most routers have USB
# instead of serial ports, and not wanting two identical packages with
# different names that conflict with each other, only the option for the
# driver with USB bindings is provided. If you really want to save that
# tiny bit of space and build it without USB support, remove nutdrv_qx
# from the previous line.

$(foreach d,$(filter-out $(SERIAL_DRIVERLIST_IGNORE),$(SERIAL_DRIVERLIST)),$(eval $(call DriverPackage,serial,$(d))))
$(foreach d,$(SNMP_DRIVERLIST),$(eval $(call DriverPackage,snmp,$(d))))
$(foreach d,$(USB_LIBUSB_DRIVERLIST),$(eval $(call DriverPackage,usb,$(d))))
$(foreach d,$(NEONXML_DRIVERLIST),$(eval $(call DriverPackage,neon,$(d))))

$(eval $(call DriverDescription,serial,al175,\
	Driver for Eltek UPS models with AL175 alarm module))
$(eval $(call DriverDescription,serial,bcmxcp,\
	Driver for UPSes supporting the serial BCM/XCP protocol))
$(eval $(call DriverDescription,serial,belkin,\
	Driver for Belkin serial UPS equipment))
$(eval $(call DriverDescription,serial,belkinunv,\
	Driver for Belkin "Universal UPS" and compatible))
$(eval $(call DriverDescription,serial,bestfcom,\
	Driver for Best Power Fortress/Ferrups))
$(eval $(call DriverDescription,serial,bestfortress,\
	Driver for old Best Fortress UPS equipment))
$(eval $(call DriverDescription,serial,bestuferrups,\
	Driver for Best Power Micro-Ferrups))
$(eval $(call DriverDescription,serial,bestups,\
	Driver for Best Power / SOLA (Phoenixtec protocol) UPS equipment))
$(eval $(call DriverDescription,serial,dummy-ups,\
	Driver for multi-purpose UPS emulation))
$(eval $(call DriverDescription,serial,etapro,\
	Driver for ETA UPS equipment))
$(eval $(call DriverDescription,serial,everups,\
	Driver for Ever UPS models))
$(eval $(call DriverDescription,serial,gamatronic,\
	Driver for Gamatronic UPS equipment))
$(eval $(call DriverDescription,serial,genericups,\
	Driver for contact-closure UPS equipment))
$(eval $(call DriverDescription,serial,isbmex,\
	Driver for ISBMEX UPS equipment))
$(eval $(call DriverDescription,serial,liebert,\
	Driver for Liebert contact-closure UPS equipment))
$(eval $(call DriverDescription,serial,liebert-esp2,\
	Driver for Liebert UPS, using the ESP-II serial protocol))
$(eval $(call DriverDescription,serial,masterguard,\
	Driver for Masterguard UPS equipment))
$(eval $(call DriverDescription,serial,metasys,\
	Driver for Meta System UPS equipment))
$(eval $(call DriverDescription,serial,mge-utalk,\
	Driver for MGE UPS SYSTEMS UTalk protocol equipment))
$(eval $(call DriverDescription,serial,microdowell,\
	Driver for Microdowell Enterprise UPS series))
$(eval $(call DriverDescription,serial,mge-shut,\
	Driver for SHUT Protocol UPS equipment))
$(eval $(call DriverDescription,serial,oneac,\
	Driver for Oneac UPS equipment))
$(eval $(call DriverDescription,serial,optiups,\
	Driver for Opti-UPS (Viewsonic) UPS and Zinto D (ONLINE-USV) equipment))
$(eval $(call DriverDescription,serial,powercom,\
	Driver for serial Powercom/Trust/Advice UPS equipment))
$(eval $(call DriverDescription,serial,rhino,\
	Driver for Brazilian Microsol RHINO UPS equipment))
$(eval $(call DriverDescription,serial,safenet,\
	Driver for SafeNet compatible UPS equipment))
$(eval $(call DriverDescription,serial,solis,\
	Driver for Brazilian Microsol SOLIS UPS equipment))
$(eval $(call DriverDescription,serial,tripplite,\
	Driver for Tripp-Lite SmartPro UPS equipment))
$(eval $(call DriverDescription,serial,tripplitesu,\
	Driver for Tripp-Lite SmartOnline (SU) UPS equipment))
$(eval $(call DriverDescription,serial,upscode2,\
	Driver for UPScode II compatible UPS equipment))
$(eval $(call DriverDescription,serial,victronups,\
	Driver for IMV/Victron UPS unit Match, Match Lite, NetUps))
$(eval $(call DriverDescription,serial,powerpanel,\
	Driver for PowerPanel Plus compatible UPS equipment))
$(eval $(call DriverDescription,serial,blazer_ser,\
	Driver for Megatec/Q1 protocol serial based UPS equipment))
$(eval $(call DriverDescription,serial,clone,\
	UPS driver clone))
$(eval $(call DriverDescription,serial,ivtscd,\
	Driver for the IVT Solar Controller Device))
$(eval $(call DriverDescription,serial,apcsmart,\
	Driver for American Power Conversion Smart Protocol UPS equipment))
$(eval $(call DriverDescription,serial,apcsmart-old,\
	Driver for American Power Conversion Smart Protocol UPS equipment))
$(eval $(call DriverDescription,serial,apcupsd-ups,\
	Driver for apcupsd client access))
$(eval $(call DriverDescription,serial,riello_ser,\
	Driver for Riello UPS Protocol UPS equipment))
$(eval $(call DriverDescription,snmp,snmp-ups,\
	Multi-MIB Driver for SNMP UPS equipment))
$(eval $(call DriverDescription,usb,usbhid-ups,\
	Driver for USB/HID UPS equipment))
$(eval $(call DriverDescription,usb,bcmxcp_usb,\
	Experimental driver for UPSes supporting the BCM/XCP protocol over USB))
$(eval $(call DriverDescription,usb,tripplite_usb,\
	Driver for older Tripp Lite USB UPSes (not PDC HID)))
$(eval $(call DriverDescription,usb,blazer_usb,\
	Driver for Megatec/Q1 protocol USB based UPS equipment))
$(eval $(call DriverDescription,usb,richcomm_usb,\
	Driver for UPS equipment using Richcomm dry-contact to USB solution))
$(eval $(call DriverDescription,usb,riello_usb,\
	Driver for Riello UPS Protocol UPS equipment via USB))
$(eval $(call DriverDescription,usb,nutdrv_atcl_usb,\
	Driver for ATCL FOR UPS equipment))
$(eval $(call DriverDescription,usb,nutdrv_qx,\
	Driver for Q* protocol serial and USB based UPS equipment))
$(eval $(call DriverDescription,neon,netxml-ups,\
        Driver for NetXML based UPS equipment))

CONFIGURE_VARS += \
	ac_cv_path_AR=$(TARGET_AR)

CONFIGURE_ARGS += \
	--sysconfdir=/opt/etc/nut \
	--datadir=/opt/share/nut \
	--with-dev \
	--$(if $(CONFIG_NUT_DRIVER_USB),with,without)-usb \
	--without-avahi \
	--$(if $(CONFIG_NUT_DRIVER_SNMP),with,without)-snmp $(if $(CONFIG_NUT_DRIVER_SNMP),--with-snmp-libs="$(TARGET_LDFLAGS) -lnetsnmp") \
	--$(if $(CONFIG_NUT_DRIVER_SERIAL),with,without)-serial \
	--without-doc \
        --$(if $(CONFIG_NUT_DRIVER_NEON),with,without)-neon \
	--without-powerman \
	--without-wrap \
	--with-hotplug-dir=/opt/etc/hotplug \
	--with$(if $(CONFIG_PACKAGE_nut-web-cgi),,out)-cgi \
	--without-ipmi \
	--without-freeipmi \
	--without-linux-i2c \
	--$(if $(CONFIG_NUT_SSL),with,without)-ssl $(if $(CONFIG_NUT_SSL),--with-openssl) \
	--without-libltdl \
	--without-macosx_ups \
	--with-statepath=/opt/var/run \
	--with-pidpath=/opt/var/run \
	--with-drvpath=/opt/lib/nut \
	--with-user=root \
	--with-group=root \
	$(if $(CONFIG_PACKAGE_nut-web-cgi),--with-gd-includes="-I$(STAGING_DIR)/opt/include") \
	$(if $(CONFIG_PACKAGE_nut-web-cgi),--with-gd-libs="-L$(STAGING_DIR)/opt/lib -lgd -lfreetype -ljpeg -lpng16 -ltiff -lwebp -lz")

TARGET_CXXFLAGS += -std=c++98

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/lib*.so* $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/include/*.h $(1)/opt/include/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/*.pc $(1)/opt/lib/pkgconfig/
endef

$(eval $(call BuildPackage,nut))
$(eval $(call BuildPackage,nut-common))
$(eval $(call BuildPackage,nut-server))
$(eval $(call BuildPackage,nut-upsmon))
#$(eval $(call BuildPackage,nut-upsmon-sendmail-notify))
$(eval $(call BuildPackage,nut-upsc))
$(eval $(call BuildPackage,nut-upscmd))
$(eval $(call BuildPackage,nut-upslog))
$(eval $(call BuildPackage,nut-upsrw))
$(eval $(call BuildPackage,nut-upssched))
$(eval $(call BuildPackage,nut-web-cgi))
$(eval $(call BuildPackage,nut-avahi-service))
$(foreach d,$(filter-out $(SERIAL_DRIVERLIST_IGNORE),$(SERIAL_DRIVERLIST)),$(eval $(call BuildPackage,nut-driver-$(d))))
$(foreach d,$(SNMP_DRIVERLIST),$(eval $(call BuildPackage,nut-driver-$(d))))
$(foreach d,$(USB_LIBUSB_DRIVERLIST),$(eval $(call BuildPackage,nut-driver-$(d))))
$(foreach d,$(NEONXML_DRIVERLIST),$(eval $(call BuildPackage,nut-driver-$(d))))
