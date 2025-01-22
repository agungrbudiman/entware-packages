#
# This is free software, licensed under the GNU General Public License v2.
# See LICENSE.txt for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=mdns-repeater
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://github.com/kennylevinsen/mdns-repeater.git
PKG_SOURCE_PROTO=git
PKG_SOURCE_DATE:=2023-12-16
PKG_SOURCE_VERSION:=f714ec4d8a0eee248ead29c2ae1bb37f124a568f
PKG_MIRROR_HASH:=d9c5e60d08afd56c5b32b88a48de8b50be2e816196e55c5b734a0d616a3430f2

PKG_MAINTAINER:=Alexander Koenig <alex@lisas.de>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=LICENSE.txt

include $(INCLUDE_DIR)/package.mk

define Package/mdns-repeater
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Multicast DNS repeater for Linux
  SUBMENU:=IP Addresses and Names
  URL:=https://github.com/kennylevinsen/mdns-repeater
endef

TARGET_CFLAGS += -I$(STAGING_DIR)/usr/include

define Build/Compile
	CFLAGS="$(TARGET_CFLAGS)" CPPFLAGS="$(TARGET_CPPFLAGS)" $(MAKE) -C $(PKG_BUILD_DIR) $(TARGET_CONFIGURE_OPTS)
endef

define Pakcage/mdns-repeater/conffiles
/opt/etc/config/mdns-repeater
endef

define Package/mdns-repeater/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/mdns-repeater $(1)/opt/sbin/

	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/mdns-repeater.init $(1)/opt/etc/init.d/S08mdns-repeater

	$(INSTALL_DIR) $(1)/opt/etc/config
	$(INSTALL_CONF) ./files/mdns_repeater.conf $(1)/opt/etc/config/mdns-repeater
endef

$(eval $(call BuildPackage,mdns-repeater))
