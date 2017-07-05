AM_CPPFLAGS = @VMOD_INCLUDES@ -I$(top_srcdir)/src/foreign -Wall -Werror -Wunused-parameter -Wmissing-prototypes

vmoddir = @VMOD_DIR@
vmod_LTLIBRARIES = libvmod_cookie.la \
	libvmod_header.la 


libvmod_cookie_la_LDFLAGS = $(AM_LDFLAGS) -module -export-dynamic -avoid-version -shared
libvmod_cookie_la_SOURCES = vcc_cookie_if.c vcc_cookie_if.h vmod_cookie.c
vcc_cookie_if.c vcc_cookie_if.h: @VMODTOOL@ $(top_srcdir)/src/vmod_cookie.vcc
	@VMODTOOL@ -w ../docs/ -o vcc_cookie_if $(top_srcdir)/src/vmod_cookie.vcc

libvmod_header_la_LDFLAGS = $(AM_LDFLAGS) -module -export-dynamic -avoid-version -shared
libvmod_header_la_SOURCES = vcc_header_if.c vcc_header_if.h vmod_header.c
vcc_header_if.c vcc_header_if.h: @VMODTOOL@ $(top_srcdir)/src/vmod_header.vcc
	@VMODTOOL@ -w ../docs/ -o vcc_header_if $(top_srcdir)/src/vmod_header.vcc


VMOD_TESTS = $(top_srcdir)/src/tests/*/*.vtc
.PHONY: $(VMOD_TESTS)

$(top_srcdir)/src/tests/*/*.vtc:
	@VARNISHTEST@ -Dvmoddir=$(vmoddir) -Dvarnishd=@VARNISHD@ -Dvmod_builddir=$(abs_top_builddir)/src -Dvmod_directors="directors from \"$(vmoddir)/libvmod_directors.so\"" $@

check: $(VMOD_TESTS)

EXTRA_DIST = foreign \
	$(top_srcdir)/src/*vcc \
	$(VMOD_TESTS) \
	vtree.h

CLEANFILES = $(builddir)/vcc*if.c $(builddir)/vcc*if.h \
	$(builddir)/../docs/vmod_*.man.rst \
	$(builddir)/../docs/vmod_*.rst
