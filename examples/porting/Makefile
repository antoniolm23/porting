#
#	Copyright (c) 1993-2004 Rinet Corp., Novosibirsk, Russia
#
# Redistribution and use in source forms, with and without modification,
# are permitted provided that this entire comment appears intact.
# Redistribution in binary form may occur without any restrictions.
#
# THIS SOFTWARE IS PROVIDED ``AS IS'' WITHOUT ANY WARRANTIES OF ANY KIND.
#

#
# Makefile.in -- template for Makefile
# Process this file with `./configure' to produce Makefile
# 	remember to edit Makefile.in, not Makefile
#

# Top level hierarchy
prefix = /usr
exec_prefix = ${prefix}
# Pathname of directory to install the binary
BINDEST = ${exec_prefix}/bin
# Pathname of directory to install the man page
MANDEST = ${prefix}/man

#
# You shouldn't need to edit anything below here.
#

PROG = trafshow
CC = gcc
CFLAGS = -march=x86-64 -mtune=generic -O2 -pipe -fstack-protector --param=ssp-buffer-size=4 -Wall -I. -I ../sys -DHAVE_CONFIG_H -pthread
YACC = bison -y
YFLAGS = -d -p `echo $@ | sed -e 's/\..*/_/'`
LEX = flex
LEXLIB = 
LFLAGS =
LDFLAGS = -Wl,-O1,--sort-common,--as-needed,-z,relro
LIBS =  -lslang -lpcap -lnsl -lresolv

# Explicitly define compilation rule since SunOS 4's make doesn't like gcc.
# Also, gcc does not remove the .o before forking 'as', which can be a
# problem if you don't own the file but can write to the directory.
.c.o:
	@rm -f $@
	$(CC) $(CFLAGS) -c $*.c

CSRC	= trafshow.c screen.c colormask.c getkey.c selector.c \
	events.c session.c show_if.c show_stat.c show_dump.c \
	parse_dl.c parse_ip.c netstat.c cisco_netflow.c addrtoname.c \
	hashtab.c lookupa.c recycle.c util.c help_page.c domain_resolver.c
YSRC	=
LSRC	=
GENSRC	= version.c
SRC	= $(CSRC) $(YSRC) $(LSRC) $(GENSRC)

# We would like to say "OBJ = $(SRC:.c=.o)" but Ultrix's make cannot
# hack the extra indirection
OBJ	= $(CSRC:.c=.o) $(YSRC:.y=.o) $(LSRC:.l=.o) $(GENSRC:.c=.o) 

CLEANFILES = $(PROG) $(OBJ) $(GENSRC) y.tab.h

$(PROG): $(OBJ)
	@rm -f $@
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $(OBJ) $(LIBS)

$(OBJ): config.h trafshow.h Makefile
version.o: version.c
version.c: VERSION Makefile
	@rm -f $@
	@sed -e 's/.*/char package[] = "&";/' PACKAGE > $@
	@sed -e 's/.*/char version[] = "&";/' VERSION >> $@
	@echo 'char target[] = "amd64-linux-gnu";' >> $@
	@echo 'char compiled[] = "$(CC) $(CFLAGS) $(LIBS)";' >> $@

install:
	./install-sh -c -s -m 555 -o bin -g bin $(PROG) $(DESTDIR)$(BINDEST)/$(PROG)
	./install-sh -c -m 444 -o bin -g bin $(PROG).1 $(DESTDIR)$(MANDEST)/man1/$(PROG).1
	[ -f /etc/$(PROG) ] || ./install-sh -c -m 444 .trafshow $(DESTDIR)/etc/$(PROG)

clean:
	rm -f $(CLEANFILES)

distclean:
	rm -f $(CLEANFILES) Makefile config.h \
		config.cache config.log	config.status autom4te.cache

tar:
	@cwd=`pwd`; dir=`basename $$cwd`; name=`cat PACKAGE`-`cat VERSION`; \
	list=""; tar="tar cvf"; \
	for i in `cat FILES`; do list="$$list $$name/$$i"; done; \
	rm -f ../$$name; ln -s $$dir ../$$name; \
	(cd ..; $$tar - $$list) | gzip -c > /tmp/$$name.tgz; \
	rm -f ../$$name

depend:	$(GENSRC)
	mkdep $(CFLAGS) $(SRC)
