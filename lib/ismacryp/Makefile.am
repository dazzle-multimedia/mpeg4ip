lib_LTLIBRARIES = libismacryp.la

AM_CPPFLAGS = -I$(top_srcdir)/include

libismacryp_la_SOURCES = ismacryplib.c \
	ismacryplib.h \
	ismacryplib_priv.h

AM_CFLAGS = -D_REENTRANT @BILLS_CWARNINGS@

EXTRA_DIST = \
	libismacryp.dsp \
	README \
	README.html
