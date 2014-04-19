AC_PREREQ([2.69])
AC_INIT([scrypt],[1.1.6])
AC_CONFIG_SRCDIR([lib/scryptenc/scryptenc.c])
AC_CONFIG_AUX_DIR([config.aux])
AC_CONFIG_MACRO_DIR([m4])
AM_INIT_AUTOMAKE([foreign subdir-objects -Wall])

# Checks for programs.
AC_PROG_CC

# Checks for libraries.
# Check for clock_gettime.  On some systems, this is provided via librt.
AC_SEARCH_LIBS([clock_gettime], [rt])
AC_CHECK_FUNCS([clock_gettime])

AC_SEARCH_LIBS([AES_encrypt], [crypto],,
    AC_MSG_ERROR([function AES_ENCRYPT not found]))

# Check for a linuxy sysinfo syscall; and while we're doing that, check if
# struct sysinfo is the old version (total RAM == totalmem) or the new
# version (total RAM == totalmem * mem_unit).
AC_CHECK_HEADERS([sys/sysinfo.h])
AC_CHECK_FUNCS([sysinfo])
AC_CHECK_TYPES([struct sysinfo], [], [], [[#include <sys/sysinfo.h>]])
AC_CHECK_MEMBERS([struct sysinfo.totalram, struct sysinfo.mem_unit],
    [], [], [[#include <sys/sysinfo.h>]])

# Check if we have a hw.usermem sysctl.
CHECK_SYSCTL_HW_USERMEM

# Check if we have <sys/param.h>, since some systems require it for sysctl
# to work.
AC_CHECK_HEADERS([sys/param.h])

# Check for posix_memalign
AC_CHECK_FUNCS([posix_memalign])

# Figure out whether to use the SSE version of the scrypt code.
AC_ARG_ENABLE([sse2],
    [AS_HELP_STRING([--enable-sse2],
	[use optimized SSE2 code (yes/no/default)])],
    [enable_sse2=${enableval}],
    [enable_sse2=default])
# Unless explicitly disabled, check if the current CPU supports SEE2.
AS_IF([test x${enable_sse2} = xyes || test x${enable_sse2} = xdefault],
     [AX_EXT])
# If SSE2 requested but unavailable, fail with a cogent error.
AS_IF([test x${enable_sse2} = xyes && test x${ax_cv_support_sse2_ext} != xyes],
    [AC_MSG_ERROR([SSE2 requested but unavailable])])
# Engage default if appropriate.
AS_IF([test x${enable_sse2} = xdefault],
    [enable_sse2=${ax_cv_support_sse2_ext}])
# Tell automake about the setting.
AM_CONDITIONAL([SSE2], [test "x$enable_sse2" = xyes])
# Note AC_EXT sets SIMD_FLAGS, but Makefile.am does not use them.
# We presume that the compiler's defaults are appropriate.
# Could add an option to ask autotools to pass them.

AC_SYS_LARGEFILE

AX_CFLAGS_WARN_ALL

AC_CONFIG_HEADERS([config.h])
AC_CONFIG_FILES([Makefile])
AC_OUTPUT
