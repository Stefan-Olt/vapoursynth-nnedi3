warning_flags = -Wall -Wextra -Wno-unused-parameter -Wshadow
common_cflags = -O2 $(MFLAGS) $(STACKREALIGN) $(warning_flags)
AM_CFLAGS = -std=c99 $(common_cflags)
AM_CXXFLAGS = -std=c++11 $(common_cflags)

AM_CPPFLAGS = $(VapourSynth_CFLAGS) -DNNEDI3_DATADIR='"$(pkgdatadir)"'

dist_pkgdata_DATA = src/nnedi3_weights.bin

lib_LTLIBRARIES = libnnedi3.la
noinst_LTLIBRARIES =

yasm_verbose = $(yasm_verbose_$(V))
yasm_verbose_ = $(yasm_verbose_$(AM_DEFAULT_VERBOSITY))
yasm_verbose_0 = @echo "  YASM    " $@;

.asm.lo:
	$(yasm_verbose)$(LIBTOOL) $(AM_V_lt) --mode=compile --tag=CC $(AS) $(ASFLAGS) -o $@ $< -prefer-non-pic

libnnedi3_la_SOURCES = src/nnedi3.cpp \
					   src/cpufeatures.cpp \
					   src/cpufeatures.h

if NNEDI3_X86
libnnedi3_la_SOURCES += src/asm/cpu-a.asm \
						src/simd_x86.h

noinst_LTLIBRARIES += libsse2.la libfma3.la libfma4.la

libsse2_la_SOURCES = src/simd_sse2.c
libsse2_la_CFLAGS = $(AM_CFLAGS) -msse2 -funroll-loops

libfma3_la_SOURCES = src/simd_fma3.c
libfma3_la_CFLAGS = $(AM_CFLAGS) -mfma -funroll-loops -ffp-contract=fast

libfma4_la_SOURCES = src/simd_fma4.c
libfma4_la_CFLAGS = $(AM_CFLAGS) -mfma4 -funroll-loops -ffp-contract=fast

libnnedi3_la_LIBADD = libsse2.la libfma3.la libfma4.la
endif

if NNEDI3_ARM
noinst_LTLIBRARIES += libneon.la

libneon_la_SOURCES = src/simd_neon.c

# With just -mfpu=neon, clang says
#    /usr/include/clang/3.5.0/include/arm_neon.h:28:2: error: "NEON support not enabled"
# "-march=armv7-a" makes it shut up. Hopefully this parameter doesn't screw up anything.
libneon_la_CFLAGS = $(AM_CFLAGS) -mfpu=neon

libnnedi3_la_LIBADD = libneon.la
endif

libnnedi3_la_LDFLAGS = -no-undefined -avoid-version $(PLUGINLDFLAGS)
