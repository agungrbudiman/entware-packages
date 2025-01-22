# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2021 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=cloudflared
PKG_VERSION:=2025.1.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/cloudflare/cloudflared/tar.gz/$(PKG_VERSION)?
PKG_HASH:=f9223cdefaa4b75aa1c49638936af9d5007b6c7bd943ab70203bb75bf32467da

PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/cloudflare/cloudflared
GO_PKG_LDFLAGS_X:=main.Version=$(PKG_VERSION)
GO_PKG_INSTALL_BIN_PATH:=/opt/bin

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk
include ../../lang/golang/golang-compiler.mk

# Go with Cloudflare experimental patches

CF_GO_VERSION:=go1.22.5-devel-cf
CF_GO_SOURCE_URL:=https://github.com/cloudflare/go.git
CF_GO_SOURCE_VERSION:=f4334cdc0c3f22a3bfdd7e66f387e3ffc65a5c38
CF_GO_HASH:=8cc8faa271991c8a53cfa7826489478d596c51738e8707b4f90e29597e8f7b88
CF_GO_BOOTSTRAP:=$(STAGING_DIR_HOSTPKG)/lib/go-cross

CF_GO_BUILD_DIR:=$(PKG_BUILD_DIR)/.go_cloudflare
GO_PKG_EXT_GO_BIN:=$(CF_GO_BUILD_DIR)/bin/go

define Download/cloudflare-go
  FILE:=$(CF_GO_VERSION).tar.gz
  URL:=$(CF_GO_SOURCE_URL)
  SOURCE_VERSION:=$(CF_GO_SOURCE_VERSION)
  MIRROR_HASH:=$(CF_GO_HASH)
  SUBDIR:=$(CF_GO_VERSION)
  PROTO:=git
endef
$(eval $(call Download,cloudflare-go))

define Build/Prepare
	$(Build/Prepare/Default)
	mkdir -p "$(CF_GO_BUILD_DIR)"
	$(HOST_TAR) -C "$(CF_GO_BUILD_DIR)" --strip-components=1 -xzf "$(DL_DIR)/$(CF_GO_VERSION).tar.gz"
	
	$(eval $(call GoCompiler/AddProfile,cloudflare-go,$(CF_GO_BUILD_DIR),,$(CF_GO_VERSION),$(GO_HOST_OS_ARCH)))
	$(call GoCompiler/cloudflare-go/Make, \
		GOROOT_BOOTSTRAP="$(CF_GO_BOOTSTRAP)" \
		GOHOSTARCH="$(GO_HOST_ARCH)" \
        	GOCACHE="$(GO_BUILD_CACHE_DIR)" \
        	GOENV=off \
        	CC="$(HOSTCC_NOCACHE)" \
        	CXX="$(HOSTCXX_NOCACHE)" \
	)
endef

define Package/cloudflared
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=Cloudflare Tunnel client
  URL:=https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle
endef

define Package/cloudflared/description
  Contains the command-line client for Cloudflare Tunnel, a tunneling
  daemon that proxies traffic from the Cloudflare network to your origins.

  This daemon sits between Cloudflare network and your origin (e.g. a
  webserver). Cloudflare attracts client requests and sends them to you
  via this daemon, without requiring you to poke holes on your firewall
  --- your origin can remain as closed as possible.
endef

define Package/cloudflared/conffiles
/opt/etc/config/cloudflared
/opt/etc/cloudflared/
endef

define Package/cloudflared/install
	$(call GoPackage/Package/Install/Bin,$(1))

	$(INSTALL_DIR) $(1)/opt/etc/cloudflared/
	$(INSTALL_CONF) $(CURDIR)/files/sample_config.yml $(1)/opt/etc/cloudflared/config.yml
	$(INSTALL_DIR) $(1)/opt/etc/config/
	$(INSTALL_CONF) $(CURDIR)/files/cloudflared.config $(1)/opt/etc/config/cloudflared
	$(INSTALL_DIR) $(1)/opt/etc/init.d/
	$(INSTALL_BIN) $(CURDIR)/files/cloudflared.init $(1)/opt/etc/init.d/S99cloudflared
endef

$(eval $(call GoBinPackage,cloudflared))
$(eval $(call BuildPackage,cloudflared))
