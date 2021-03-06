# vim:set noet ts=4:
#
# ibus - The Input Bus
#
# Copyright (c) 2007-2015 Peng Huang <shawn.p.huang@gmail.com>
# Copyright (c) 2015-2018 Takao Fujwiara <takao.fujiwara1@gmail.com>
# Copyright (c) 2007-2018 Red Hat, Inc.
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301
# USA

NULL =

libibus = $(top_builddir)/src/libibus-@IBUS_API_VERSION@.la

component_DATA = \
	gtkpanel.xml \
	$(NULL)
componentdir = $(pkgdatadir)/component

%.xml: %.xml.in
	$(AM_V_GEN) sed \
		-e 's|@VERSION[@]|$(VERSION)|g' \
		-e 's|@libexecdir[@]|$(libexecdir)|g' $< > $@.tmp && \
		mv $@.tmp $@

# force include config.h before gi18n.h.
AM_CPPFLAGS = \
	-I$(top_srcdir)/src \
	-I$(top_builddir)/src \
	-include $(CONFIG_HEADER) \
	$(NULL)

AM_CFLAGS = \
	@GLIB2_CFLAGS@ \
	@GIO2_CFLAGS@ \
	@GTHREAD2_CFLAGS@ \
	@GTK3_CFLAGS@ \
	@X11_CFLAGS@ \
	-DG_LOG_DOMAIN=\"IBUS\" \
	-DBINDIR=\"$(bindir)\" \
	-DPKGDATADIR=\"$(pkgdatadir)\" \
	-DIBUS_DISABLE_DEPRECATED \
	-Wno-unused-variable \
	-Wno-unused-but-set-variable \
	-Wno-unused-function \
	$(NULL)

AM_LDADD = \
	@GOBJECT2_LIBS@ \
	@GLIB2_LIBS@ \
	@GIO2_LIBS@ \
	@GTHREAD2_LIBS@ \
	@GTK3_LIBS@ \
	@X11_LIBS@ \
	-lXi \
	$(libibus) \
	$(NULL)

AM_VALAFLAGS = \
	--vapidir=$(top_builddir)/bindings/vala \
	--vapidir=$(top_srcdir)/bindings/vala \
	--pkg=posix \
	--pkg=gtk+-3.0 \
	--pkg=gdk-x11-3.0 \
	--pkg=ibus-1.0 \
	--pkg=config \
	--pkg=xi \
	--pkg=gdk-wayland \
	--target-glib="$(VALA_TARGET_GLIB_VERSION)" \
	$(NULL)

if ENABLE_LIBNOTIFY
AM_CFLAGS += \
	@LIBNOTIFY_CFLAGS@ \
	$(NULL)

AM_LDADD += \
	@LIBNOTIFY_LIBS@ \
	$(NULL)

AM_VALAFLAGS += \
	--pkg=libnotify \
	-D ENABLE_LIBNOTIFY \
	$(NULL)
endif

if ENABLE_APPINDICATOR
AM_VALAFLAGS += --define=INDICATOR
endif

if ENABLE_APPINDICATOR_ENGINE_ICON
AM_VALAFLAGS += --define=INDICATOR_ENGINE_ICON
endif

libexec_PROGRAMS = ibus-ui-gtk3

ibus_ui_gtk3_SOURCES = \
	application.vala \
	bindingcommon.vala \
	candidatearea.vala \
	candidatepanel.vala \
	emojier.vala \
	handle.vala \
	iconwidget.vala \
	indicator.vala \
	keybindingmanager.vala \
	panel.vala \
	pango.vala \
	property.vala \
	propertypanel.vala \
	separator.vala \
	switcher.vala \
	xkblayout.vala \
	gen-notification-item.xml.c \
	gen-notification-watcher.xml.c \
	$(NULL)

ibus_ui_gtk3_LDADD = \
	$(AM_LDADD) \
	$(NULL)

gen-%.xml.c: %.xml
	echo "Building $@ from $<"
	echo "const char * _$(subst -,_,$(subst .,_,$(basename $(notdir $<)))) = " > $@
	sed -e "s:\":\\\\\":g" -e s:^:\": -e s:\$$:\\\\n\": $< >> $@
	echo ";" >> $@

CLEANFILES =            \
    gtkpanel.xml        \
    $(NULL)

emoji_headers =         \
    ibusemojidialog.h   \
    $(NULL)

man_seven_in_files = ibus-emoji.7.in

# References:
# libappindicator/src/notification-item.xml
# libappindicator/src/notification-watcher.xml
# kdelibs/kdeui/knotifications/src/org.kde.StatusNotifierItem.xml
# kdelibs/kdeui/knotifications/src/org.kde.StatusNotifierWatcher.xml
EXTRA_DIST =                            \
    $(emoji_headers)                    \
    $(man_seven_in_files)               \
    emojierapp.vala                     \
    extension.vala                      \
    gtkextension.xml.in                 \
    gtkpanel.xml.in                     \
    notification-item.xml               \
    notification-watcher.xml            \
    ibus-ui-emojier.desktop.in.in       \
    ibus-extension-gtk3.desktop.in.in   \
    panelbinding.vala                   \
    $(NULL)

if ENABLE_EMOJI_DICT
AM_VALAFLAGS += --define=EMOJI_DICT

libexec_PROGRAMS += ibus-ui-emojier

ibus_ui_emojier_VALASOURCES =                   \
    emojierapp.vala                             \
    emojier.vala                                \
    iconwidget.vala                             \
    separator.vala                              \
    pango.vala                                  \
    $(NULL)
ibus_ui_emojier_SOURCES =                       \
    $(ibus_ui_emojier_VALASOURCES:.vala=.c)     \
    $(NULL)

ibus_ui_emojier_LDADD =                         \
    $(AM_LDADD)                                 \
    $(NULL)

ibus_ui_emojier_VALAFLAGS =                     \
    $(AM_VALAFLAGS)                             \
    $(NULL)

# This line and foo_VALASOURCES line can delete the duplicated entries
# of emojier.c: emojier.vala
emojierapp.c: $(ibus_ui_emojier_VALASOURCES)
	$(AM_V_VALAC)$(am__cd) $(srcdir) && $(VALAC) $(AM_VALAFLAGS) \
$(VALAFLAGS) -C $(ibus_ui_emojier_VALASOURCES)
	$(NULL)
# make dist creates .c files in a different srcdir
emojierapp.o: $(srcdir)/emojierapp.c
	$(AM_V_CC)source='$<' object='$@' libtool=no \
	DEPDIR=$(DEPDIR) $(CCDEPMODE) $(depcomp) \
	$(AM_V_CC_no)$(COMPILE) -c -o $@ $<
	$(NULL)

component_DATA += gtkextension.xml
CLEANFILES += gtkextension.xml
libexec_PROGRAMS += ibus-extension-gtk3

ibus_extension_gtk3_VALASOURCES =               \
    bindingcommon.vala                          \
    emojier.vala                                \
    extension.vala                              \
    iconwidget.vala                             \
    keybindingmanager.vala                      \
    panelbinding.vala                           \
    pango.vala                                  \
    $(NULL)
ibus_extension_gtk3_SOURCES =                   \
    $(ibus_extension_gtk3_VALASOURCES:.vala=.c) \
    $(NULL)

ibus_extension_gtk3_LDADD =                     \
    $(AM_LDADD)                                 \
    $(NULL)
ibus_extension_gtk3_VALAFLAGS =                 \
    $(AM_VALAFLAGS)                             \
    $(NULL)

# This line and foo_VALASOURCES line can delete the duplicated entries
# of emojier.c: emojier.vala
extension.c: $(ibus_extension_gtk3_VALASOURCES)
	$(AM_V_VALAC)$(am__cd) $(srcdir) && $(VALAC) $(AM_VALAFLAGS) \
$(VALAFLAGS) -C $(ibus_extension_gtk3_VALASOURCES)
	$(NULL)
# make dist creates .c files in a different srcdir
extension.o: $(srcdir)/extension.c
	$(AM_V_CC)source='$<' object='$@' libtool=no \
	DEPDIR=$(DEPDIR) $(CCDEPMODE) $(depcomp) \
	$(AM_V_CC_no)$(COMPILE) -c -o $@ $<
	$(NULL)
# of emojier.c: emojier.vala
panelbinding.c: $(ibus_extension_gtk3_VALASOURCES)
	$(AM_V_VALAC)$(am__cd) $(srcdir) && $(VALAC) $(AM_VALAFLAGS) \
$(VALAFLAGS) -C $(ibus_extension_gtk3_VALASOURCES)
	$(NULL)
# make dist creates .c files in a different srcdir
panelbinding.o: $(srcdir)/panelbinding.c
	$(AM_V_CC)source='$<' object='$@' libtool=no \
	DEPDIR=$(DEPDIR) $(CCDEPMODE) $(depcomp) \
	$(AM_V_CC_no)$(COMPILE) -c -o $@ $<
	$(NULL)

man_seven_files = $(man_seven_in_files:.7.in=.7)
man_seven_DATA =$(man_seven_files:.7=.7.gz)
man_sevendir = $(mandir)/man7
%.7: %.7.in
	$(AM_V_GEN) sed \
	    -e 's|@VERSION[@]|$(VERSION)|g' $< > $@.tmp && \
	    mv $@.tmp $@
%.7.gz: %.7
	$(AM_V_GEN) gzip -c $< > $@.tmp && mv $@.tmp $@

desktop_in_in_files = \
    ibus-ui-emojier.desktop.in.in \
    ibus-extension-gtk3.desktop.in.in \
    $(NULL)
desktop_in_files = $(desktop_in_in_files:.desktop.in.in=.desktop.in)
desktop_DATA = \
    org.freedesktop.IBus.Panel.Emojier.desktop \
    org.freedesktop.IBus.Panel.Extension.Gtk3.desktop \
    $(NULL)
desktopdir = $(datadir)/applications
%.desktop.in: %.desktop.in.in
	$(AM_V_GEN) sed \
		-e 's|^_Name=|Name=|g' \
		-e 's|@libexecdir[@]|$(libexecdir)|g' $< > $@.tmp && \
                mv $@.tmp $@
	$(NULL)
org.freedesktop.IBus.Panel.Emojier.desktop: ibus-ui-emojier.desktop.in
	$(AM_V_GEN) $(MSGFMT) --desktop --template $< -d $(top_srcdir)/po -o $@
	$(NULL)
org.freedesktop.IBus.Panel.Extension.Gtk3.desktop: ibus-extension-gtk3.desktop.in
	$(AM_V_GEN) $(MSGFMT) --desktop --template $< -d $(top_srcdir)/po -o $@
	$(NULL)

CLEANFILES += \
    $(desktop_DATA) \
    $(desktop_in_files) \
    $(man_seven_DATA) \
    $(man_seven_files) \
    $(NULL)

endif
# end of ENABLE_EMOJI_DICT

-include $(top_srcdir)/git.mk
