include $(TOPDIR)/rules.mk

PKG_NAME:=mihomo
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/MetaCubeX/mihomo.git
PKG_SOURCE_DATE:=2024-08-24
PKG_SOURCE_VERSION:=27bcb26ecdb1828e042047e154f2083748a90d23
PKG_MIRROR_HASH:=51ec958a81cd354064d6c508d4316f244300a3a0f64e0769f9b546edccd6ed0d

PKG_LICENSE:=MIT
PKG_MAINTAINER:=Joseph Mory <morytyann@gmail.com>

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

PKG_BUILD_VERSION:=alpha-$(shell printf '%.8s' $(PKG_SOURCE_VERSION))
PKG_BUILD_TIME:=$(shell date -u -Iseconds)

GO_PKG:=github.com/metacubex/mihomo
GO_PKG_LDFLAGS_X:=$(GO_PKG)/constant.Version=$(PKG_BUILD_VERSION) $(GO_PKG)/constant.BuildTime=$(PKG_BUILD_TIME)
GO_PKG_TAGS:=with_gvisor

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/mihomo
  SECTION:=net
  CATEGORY:=Network
  TITLE:=A rule based proxy in Go.
  URL:=https://wiki.metacubex.one
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle +curl +yq firewall4 +kmod-nft-tproxy +ip-full +kmod-tun
  USERID:=mihomo=7890:mihomo=7890
endef

define Package/mihomo/description
 A rule based proxy in Go.
endef

define Package/mihomo/conffiles
/etc/config/mihomo
/etc/mihomo/mixin.yaml
endef

define Package/mihomo/install
	$(call GoPackage/Package/Install/Bin,$(1))

	$(INSTALL_DIR) $(1)/etc/mihomo
	$(INSTALL_DIR) $(1)/etc/mihomo/scripts
	$(INSTALL_DIR) $(1)/etc/mihomo/nftables
	$(INSTALL_DIR) $(1)/etc/mihomo/profiles
	$(INSTALL_DIR) $(1)/etc/mihomo/run
	$(INSTALL_DIR) $(1)/etc/mihomo/run/rules
	$(INSTALL_DIR) $(1)/etc/mihomo/run/ui

	$(INSTALL_DATA) $(CURDIR)/files/mixin.yaml $(1)/etc/mihomo/mixin.yaml

	$(INSTALL_BIN) $(CURDIR)/files/scripts/constants.sh $(1)/etc/mihomo/scripts/constants.sh
	$(INSTALL_BIN) $(CURDIR)/files/scripts/tun.sh $(1)/etc/mihomo/scripts/tun.sh

	$(INSTALL_BIN) $(CURDIR)/files/nftables/hijack.nft $(1)/etc/mihomo/nftables/hijack.nft
	$(INSTALL_BIN) $(CURDIR)/files/nftables/geoip_cn.nft $(1)/etc/mihomo/nftables/geoip_cn.nft
	$(INSTALL_BIN) $(CURDIR)/files/nftables/geoip6_cn.nft $(1)/etc/mihomo/nftables/geoip6_cn.nft

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) $(CURDIR)/files/mihomo.conf $(1)/etc/config/mihomo

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(CURDIR)/files/mihomo.init $(1)/etc/init.d/mihomo

	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) $(CURDIR)/files/uci-defaults/init.sh $(1)/etc/uci-defaults/99_init_mihomo
	$(INSTALL_BIN) $(CURDIR)/files/uci-defaults/migrate.sh $(1)/etc/uci-defaults/99_migrate_mihomo

	$(INSTALL_DIR) $(1)/etc/capabilities
	$(INSTALL_DATA) $(CURDIR)/files/capabilities.json $(1)/etc/capabilities/mihomo.json
endef

define Build/Prepare
	$(Build/Prepare/Default)
	$(RM) -r $(PKG_BUILD_DIR)/rules/logic_test
endef

$(eval $(call GoBinPackage,mihomo))
$(eval $(call BuildPackage,mihomo))
