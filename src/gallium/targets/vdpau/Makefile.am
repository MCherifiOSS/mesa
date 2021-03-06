include $(top_srcdir)/src/gallium/Automake.inc

AM_CFLAGS = \
	$(GALLIUM_TARGET_CFLAGS)

vdpaudir = $(VDPAU_LIB_INSTALL_DIR)
vdpau_LTLIBRARIES = libvdpau_gallium.la

nodist_EXTRA_libvdpau_gallium_la_SOURCES = dummy.cpp
libvdpau_gallium_la_SOURCES =

libvdpau_gallium_la_LDFLAGS = \
	-shared \
	-module \
	-no-undefined \
	-version-number $(VDPAU_MAJOR):$(VDPAU_MINOR) \
	$(GC_SECTIONS) \
	$(LD_NO_UNDEFINED)

if HAVE_LD_VERSION_SCRIPT
libvdpau_gallium_la_LDFLAGS += \
	-Wl,--version-script=$(top_srcdir)/src/gallium/targets/vdpau/vdpau.sym
endif # HAVE_LD_VERSION_SCRIPT

if HAVE_LD_DYNAMIC_LIST
libvdpau_gallium_la_LDFLAGS += \
	-Wl,--dynamic-list=$(top_srcdir)/src/gallium/targets/dri-vdpau.dyn
endif # HAVE_LD_DYNAMIC_LIST

# NOTE: libvdpau_gallium does not use(link against) libvdpau
libvdpau_gallium_la_LIBADD = \
	$(top_builddir)/src/gallium/state_trackers/vdpau/libvdpautracker.la \
	$(top_builddir)/src/gallium/auxiliary/libgalliumvl.la \
	$(top_builddir)/src/gallium/auxiliary/libgallium.la \
	$(top_builddir)/src/util/libmesautil.la \
	$(VL_LIBS) \
	$(LIBDRM_LIBS) \
	$(GALLIUM_COMMON_LIB_DEPS)

EXTRA_libvdpau_gallium_la_DEPENDENCIES = \
	vdpau.sym \
	$(top_srcdir)/src/gallium/targets/dri-vdpau.dyn
EXTRA_DIST = \
	vdpau.sym \
	$(top_srcdir)/src/gallium/targets/dri-vdpau.dyn

TARGET_DRIVERS =
TARGET_CPPFLAGS =
TARGET_LIB_DEPS = $(top_builddir)/src/loader/libloader.la

include $(top_srcdir)/src/gallium/drivers/nouveau/Automake.inc

include $(top_srcdir)/src/gallium/drivers/r300/Automake.inc
include $(top_srcdir)/src/gallium/drivers/r600/Automake.inc
include $(top_srcdir)/src/gallium/drivers/radeonsi/Automake.inc

if HAVE_GALLIUM_STATIC_TARGETS

libvdpau_gallium_la_SOURCES += target.c
libvdpau_gallium_la_CPPFLAGS = $(TARGET_CPPFLAGS)
libvdpau_gallium_la_LIBADD += $(TARGET_LIB_DEPS) \
	$(TARGET_RADEON_WINSYS) $(TARGET_RADEON_COMMON)

else # HAVE_GALLIUM_STATIC_TARGETS

libvdpau_gallium_la_LIBADD += \
	$(top_builddir)/src/gallium/auxiliary/pipe-loader/libpipe_loader.la \
	$(GALLIUM_PIPE_LOADER_WINSYS_LIBS) \
	$(GALLIUM_PIPE_LOADER_LIBS)

endif # HAVE_GALLIUM_STATIC_TARGETS

if HAVE_MESA_LLVM
libvdpau_gallium_la_LIBADD += $(LLVM_LIBS)
libvdpau_gallium_la_LDFLAGS += $(LLVM_LDFLAGS)
endif

if HAVE_COMPAT_SYMLINKS
# Add a link to allow setting VDPAU_DRIVER_PATH to /lib/gallium of the build tree.
all-local: $(vdpau_LTLIBRARIES)
	$(AM_V_GEN)link_dir=$(top_builddir)/$(LIB_DIR)/gallium;		\
	$(MKDIR_P) $${link_dir};					\
	for i in $(TARGET_DRIVERS); do					\
		j=libvdpau_gallium.$(LIB_EXT);				\
		k=libvdpau_$${i}.$(LIB_EXT);				\
		l=$${k}.$(VDPAU_MAJOR).$(VDPAU_MINOR).0;		\
		ln -f .libs/$${j}.$(VDPAU_MAJOR).$(VDPAU_MINOR).0	\
		      $${link_dir}/$${l};				\
		ln -sf $${l}						\
		       $${link_dir}/$${k}.$(VDPAU_MAJOR).$(VDPAU_MINOR); \
		ln -sf $${l}						\
		       $${link_dir}/$${k}.$(VDPAU_MAJOR);		\
		ln -sf $${l}						\
		       $${link_dir}/$${k};				\
	done
endif

# hardlink each megadriver instance, but don't actually have
# libvdpau_gallium.so in the set of final installed files.
install-data-hook:
	$(AM_V_GEN)dest_dir=$(DESTDIR)/$(vdpaudir);			\
	for i in $(TARGET_DRIVERS); do					\
		j=libvdpau_gallium.$(LIB_EXT);				\
		k=libvdpau_$${i}.$(LIB_EXT);				\
		l=$${k}.$(VDPAU_MAJOR).$(VDPAU_MINOR).0;		\
		ln -f $${dest_dir}/$${j}.$(VDPAU_MAJOR).$(VDPAU_MINOR).0 \
		      $${dest_dir}/$${l};				\
		ln -sf $${l}						\
		       $${dest_dir}/$${k}.$(VDPAU_MAJOR).$(VDPAU_MINOR); \
		ln -sf $${l}						\
		       $${dest_dir}/$${k}.$(VDPAU_MAJOR);		\
		ln -sf $${l}						\
		       $${dest_dir}/$${k};				\
	done;								\
	$(RM) -f $${dest_dir}/libvdpau_gallium.*
