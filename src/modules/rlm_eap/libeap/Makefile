#
# Makefile
#
# Version:	$Id$
#

include ../../../../Make.inc

SRCS		= eapcommon.c eapcrypto.c eapsimlib.c fips186prf.c
ifneq ($(OPENSSL_LIBS),)
SRCS		+= eap_tls.c mppe_keys.c
endif
LT_OBJS		= $(SRCS:.c=.lo)
INCLUDES	= eap_types.h eap_tls.h
CFLAGS		+= -DEAPLIB -I. -I.. -I$(top_builddir)/src
ifeq ($(USE_SHARED_LIBS),yes)
LINK_MODE	= -export-dynamic
else
LINK_MODE	= -static
endif
TARGET		= $(LIBPREFIX)freeradius-eap

# Define new rule for libtool objects
%.lo: %.c
	@echo CC $<
	@$(LIBTOOL) --quiet --mode=compile $(CC) $(CFLAGS) -c $<

all: $(TARGET).la

$(TARGET).la: $(LT_OBJS)
	@echo LINK $@ $<
	@$(LIBTOOL) --quiet --mode=link $(CC) -release $(RADIUSD_VERSION_STRING) \
	    $(LDFLAGS) $(LINK_MODE) -o $@ $(MOD_RPATH)$^ \
	    $(LIBRADIUS) $(LIBS)
	@for x in .libs/* $^; do \
		rm -f $(top_builddir)/src/modules/lib/$$x; \
		ln -s $(top_builddir)/src/modules/rlm_eap/libeap/$$x $(top_builddir)/src/modules/lib/$$x; \
	done

$(LT_OBJS): $(INCLUDES)

clean:
	@rm -f *.o *.lo $(TARGET).la
	@rm -rf .libs

install: all
	$(LIBTOOL) --mode=install $(INSTALL) -c $(MOD_RPATH) $(TARGET).la \
		$(R)$(libdir)/$(TARGET).la
	rm -f $(R)$(libdir)/$(TARGET)-$(RADIUSD_VERSION_STRING).la;
	ln -s $(TARGET).la $(R)$(libdir)/$(TARGET)-$(RADIUSD_VERSION_STRING).la
