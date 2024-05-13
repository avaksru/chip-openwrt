include $(TOPDIR)/rules.mk

PKG_NAME:=chip-tool
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://github.com/project-chip/connectedhomeip.git
PKG_SOURCE_PROTO:=git
PKG_SOURCE_MIRROR:=0 # don't try OpenWrt mirror
PKG_SOURCE_SUBMODULES:= \
  third_party/pigweed \
  third_party/jsoncpp \
  third_party/nlassert \
  third_party/editline \
  third_party/nlunit-test \
  third_party/nlio \
  third_party/perfetto \
  third_party/libwebsockets

PKG_SOURCE_DATE:=2023-10-28
PKG_SOURCE_VERSION:=181b0cb14ff007ec912f2ba6627e05dfb066c008

PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=gn/host

include $(INCLUDE_DIR)/package.mk

# https://github.com/project-chip/matter-openwrt/issues/2
ifeq ($(DUMP)$(filter SUBMODULES:=,$(Download/Defaults)),)
$(error PKG_SOURCE_SUBMODULES is not supported, ensure https://github.com/openwrt/openwrt/pull/13000 is included in your OpenWrt buildroot)
endif

define Package/chip-tool
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Matter chip-tool
	URL:=https://github.com/project-chip/connectedhomeip
	DEPENDS:=+libstdcpp +libopenssl +glib2
endef

define Package/chip-tool/description
	Matter controller
	Integrates a router / access point with the Matter IoT ecosystem.
endef
TARGET_CFLAGS += -DFATCONFDIR=\"/root\" -DSYSCONFDIR=\"/root\" -DLOCALSTATEDIR=\"/root\"
TARGET_CXXFLAGS += -DFATCONFDIR=\"/root\" -DSYSCONFDIR=\"/root\" -DLOCALSTATEDIR=\"/root\"
TARGET_CXXFLAGS += -Wno-format-nonliteral 
TARGET_CFLAGS += -Wno-format-nonliteral

# The build environment contains host tools that can be shared between targets
CHIP_BUILD_ENV_DIR:=$(STAGING_DIR_HOST)/share/chip-build-env
CHIP_TOOL_OUT_DIR:=$(PKG_BUILD_DIR)/out/chip-tool

define Build/Configure
	mkdir -p $(CHIP_TOOL_OUT_DIR) && cd $(CHIP_TOOL_OUT_DIR) && \
		$(CONFIGURE_VARS) $(PKG_BUILD_DIR)/scripts/configure \
			--build-env-dir="$(CHIP_BUILD_ENV_DIR)" \
			--project=examples/chip-tool\
			--target=$(GNU_TARGET_NAME) \
			--enable-tracing-support=no

endef

define Build/Compile
	$(NINJA) -C $(CHIP_TOOL_OUT_DIR) chip-tool
endef

define Package/chip-tool/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(CHIP_TOOL_OUT_DIR)/chip-tool $(1)/usr/sbin/chip-tool
endef

$(eval $(call BuildPackage,chip-tool))
