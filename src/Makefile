MKPATH=../mk/
include $(MKPATH)buildsys.mk

# import source list
include Makefile.src
INCLUDE = $(HDRS) $(INCS)
OBJECTS = $(ANGFILES) $(ZFILES)
SRCS    = ${OBJECTS:.o=.c} ${MAINFILES:.o=.c}
PROG    = $(PROGNAME)$(PROG_SUFFIX)
VERSION := $(shell ../scripts/version.sh)
ifneq (${VERSION},)
	CFLAGS += -DBUILD_ID=${VERSION}
endif

CFLAGS += -I. -fPIC -std=c99 -O0 -g2 -W -Wall -Wextra
#CFLAGS += -I. -fPIC -std=c99 -O3 -flto -g2 -W -Wall -Wextra
# -Wold-style-definition -Wmissing-declarations -Wredundant-decls -Wpointer-arith -Wcast-align -Wwrite-strings -Winline -Wformat-security -Winit-self -Wmissing-include-dirs -Wundef -Wmissing-format-attribute -Wnested-externs -Wunreachable-code -Wno-unused-parameter -Wno-missing-field-initializers

# Replace above line with the two below and then look at gmon.out
# to do performance monitoring
# CFLAGS += -g -pg -I. -fPIC -std=c99 -O0
# LDFLAGS += -g -pg

# gcov intermediate data
GCOBJS = $(OBJECTS:.o=.gcno) $(OBJECTS:.o=.gcda)
GCOVS = $(OBJECTS:.o=.c.gcov)

CLEAN = angband.o $(OBJECTS) win/angband.res
DISTCLEAN = autoconf.h

export CFLAGS LDFLAGS LIBS

$(PROG): $(PROGNAME).o $(MAINFILES)
	$(CC) -o $@ $(PROGNAME).o $(MAINFILES) $(LDFLAGS) $(LDADD) $(LIBS)
	@printf "%10s %-20s\n" LINK $@

win/$(PROGNAME).res: win/$(PROGNAME).rc
	$(RC) $< -O coff -o $@

$(PROGNAME).o: $(OBJECTS)
	$(LD) $(LDFLAGS) -nostdlib -r -o $@ $(OBJECTS)
	@printf "%10s %-20s\n" LINK $@

tests: $(PROGNAME).o
	$(MAKE) -C tests all

test-clean:
	$(MAKE) -C tests clean

splint:
	splint -f .splintrc ${OBJECTS:.o=.c} main.c main-gcu.c

clean-coverage:
	rm -f tests/ran-already
	rm -f $(GCOVS) $(GCOBJS)

coverage: CFLAGS+=-fprofile-arcs -ftest-coverage
coverage: LDFLAGS+=-lgcov
coverage: clean-coverage clean gen-covs

tests/ran-already : tests
	touch $@

gen-covs: tests/ran-already $(GCOVS)
	./gen-coverage $(GCOVS)

%.gcov: %
	(gcov -o $(dir $^) -p $^ >/dev/null)

.PHONY : tests coverage clean-coverage tests/ran-already
