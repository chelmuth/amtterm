# config
#USE_OPENSSL=1
#USE_GNUTLS=1
srcdir	= .
VPATH	= $(srcdir)
-include Make.config
include $(srcdir)/mk/Variables.mk

ifdef USE_OPENSSL
SSL_DEFS=-DUSE_OPENSSL
pkglst+=openssl
endif

ifdef USE_GNUTLS
SSL_DEFS=-DUSE_GNUTLS
pkglst+=gnutls
endif

CFLAGS	+= -Wall -Wno-pointer-sign
CFLAGS	+= -DVERSION='"$(VERSION)"'
CFLAGS  += $(SSL_DEFS)

TARGETS	:= amtterm
DESKTOP := $(wildcard *.desktop)

all: build

#################################################################
# poor man's autoconf ;-)

include mk/Autoconf.mk

define make-config
LIB		:= $(LIB)
HAVE_GTK	:= $(call ac_pkg_config,gtk+-3.0)
HAVE_GDK	:= $(call ac_pkg_config,gdk-3.0)
HAVE_VTE	:= $(call ac_pkg_config,vte-2.91)
endef

#################################################################

# build gamt?
ifeq ($(HAVE_GTK)$(HAVE_VTE),yesyes)
  TARGETS += gamt
  gamt : CFLAGS += -Wno-strict-prototypes
  gamt : pkglst += gtk+-3.0 gdk-3.0 vte-2.91
endif

CFLAGS += $(shell test "$(pkglst)" != "" && pkg-config --cflags $(pkglst))
LDLIBS += $(shell test "$(pkglst)" != "" && pkg-config --libs   $(pkglst))

#################################################################

build: $(TARGETS)

install: build
	$(INSTALL_DIR) $(bindir) $(appdir) $(mandir)/man1 $(mandir)/man7
	$(INSTALL_BINARY) $(TARGETS) $(bindir)
	$(INSTALL_SCRIPT) amttool $(bindir)
	$(INSTALL_DATA) $(DESKTOP) $(appdir)
	$(INSTALL_DATA) gamt.man $(mandir)/man1/gamt.1
	$(INSTALL_DATA) amtterm.man $(mandir)/man1/amtterm.1
	$(INSTALL_DATA) amttool.man $(mandir)/man1/amttool.1
	$(INSTALL_DATA) amt-howto.man $(mandir)/man7/amt-howto.7

clean:
	rm -f *.o *~
	rm -f $(TARGETS)

distclean: clean
	rm -f Make.config
	rm -f mk/*.dep

#################################################################

amtterm: amtterm.o redir.o tcp.o auth.o ssl.o
gamt: gamt.o redir.o tcp.o parseconfig.o auth.o ssl.o

#################################################################

include mk/Compile.mk
include mk/Maintainer.mk
-include $(depfiles)
