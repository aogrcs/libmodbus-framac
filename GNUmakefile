export FRAMA_C_MEMORY_FOOTPRINT = 8

-include frama-c-path.mk

FRAMAC_CONFIG ?= frama-c-config
FRAMAC_SHARE=$(shell $(FRAMAC_CONFIG) -print-share-path)
include $(shell $(FRAMAC_CONFIG) -print-share-path)/analysis-scripts/frama-c.mk

TEST_COMMON_SRCS = 

CPPFLAGS += -include__fc_libc_fixes.h -I src
FCFLAGS  += -machdep gcc_x86_64
EVAFLAGS += \
  -val-use-spec atoi,malloc,free,calloc,Transform,parse_include \
  -eva-equality-domain -eva-gauges-domain -eva-symbolic-locations-domain \
  -slevel 500 \
  -val-warn-on-alarms \
  -val-malloc-functions malloc,realloc,Malloc,Malloc2,Realloc,Realloc2 \
  -no-val-alloc-returns-null \
  -main analysis_main

TARGETS = libmodbus-unit-test-server
all: $(TARGETS:%=%.eva)
help:
	@echo "targets: $(TARGETS)"
loop: $(TARGETS:%=%.parse.loop) $(TARGETS:%=%.eva.loop)
parse: $(TARGETS:%=%.parse)
stats: $(TARGETS:%=%.parse) $(TARGETS:%=%.eva)
	../show_stats.sh "$(notdir $(CURDIR))" $^

libmodbus-unit-test-server.parse: $(sort $(wildcard src/*.c)) tests/unit-test-server.c $(FRAMAC_SHARE)/libc/netdb.c $(TEST_COMMON_SRCS)
