if HAVE_ZLIB
Z_LIBS= -lz
else
Z_LIBS=
endif

INCLUDES = \
	-fexceptions -DMONO_USE_EXC_TABLES	\
	-I$(top_srcdir) 	\
	$(GLIB_CFLAGS)

if !DISABLE_PROFILER
if JIT_SUPPORTED
bin_PROGRAMS = mprof-report
lib_LTLIBRARIES = libmono-profiler-cov.la libmono-profiler-aot.la libmono-profiler-iomap.la libmono-profiler-log.la
if PLATFORM_DARWIN
libmono_profiler_log_la_LDFLAGS = -Wl,-undefined -Wl,suppress -Wl,-flat_namespace
endif
endif
endif

if HAVE_OPROFILE
# Do something that uses OPROFILE_CFLAGS and OPROFILE_LIBS
endif

if SUPPORT_BOEHM
LIBMONO=$(top_builddir)/mono/mini/libmono-$(API_VER).la
else
LIBMONO=$(top_builddir)/mono/mini/libmonosgen-$(API_VER).la
endif

libmono_profiler_cov_la_SOURCES = mono-cov.c
libmono_profiler_cov_la_LIBADD = $(LIBMONO) $(GLIB_LIBS) $(LIBICONV)
libmono_profiler_aot_la_SOURCES = mono-profiler-aot.c
libmono_profiler_aot_la_LIBADD = $(LIBMONO) $(GLIB_LIBS) $(LIBICONV)
#libmono_profiler_logging_la_SOURCES = mono-profiler-logging.c
#libmono_profiler_logging_la_LIBADD = $(LIBMONO) $(GLIB_LIBS) $(LIBICONV)
libmono_profiler_iomap_la_SOURCES = mono-profiler-iomap.c
libmono_profiler_iomap_la_LIBADD = $(LIBMONO) $(GLIB_LIBS) $(LIBICONV)
libmono_profiler_log_la_SOURCES = proflog.c
libmono_profiler_log_la_LIBADD = $(LIBMONO) $(Z_LIBS)

mprof_report_SOURCES = decode.c
mprof_report_LDADD = $(Z_LIBS)

PLOG_TESTS_SRC=test-alloc.cs test-busy.cs test-monitor.cs test-excleave.cs \
	test-heapshot.cs test-traces.cs
PLOG_TESTS=$(PLOG_TESTS_SRC:.cs=.exe)

with_mono_path = MONO_PATH=$(mcs_topdir)/class/lib/net_4_0

RUNTIME = $(with_mono_path) $(top_builddir)/runtime/mono-wrapper
MCS = $(RUNTIME) $(mcs_topdir)/class/lib/build/mcs.exe -unsafe -nowarn:0162 -nowarn:0168 -nowarn:0219 -debug

%.exe: %.cs
	$(MCS) -out:$@ $<

testlog: $(PLOG_TESTS)
	$(with_mono_path) perl $(srcdir)/ptestrunner.pl $(top_builddir)

check-local: testlog

EXTRA_DIST=utils.c utils.h proflog.h log-profiler.txt perf_event.h \
	$(PLOG_TESTS_SRC) ptestrunner.pl
