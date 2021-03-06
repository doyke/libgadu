dnl $Id$

AC_INIT([libgadu], [1.12.0-pre])
AC_CONFIG_SRCDIR([src/libgadu.c])
AM_INIT_AUTOMAKE

AC_PREREQ(2.50)
AC_CANONICAL_HOST
AC_CONFIG_HEADERS(config.h include/libgadu.h)
AC_CONFIG_MACRO_DIR([m4])

m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

AC_PROG_CC
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_LN_S
AC_C_CONST
AC_PROG_LIBTOOL
AC_SUBST(LIBTOOL_DEPS)

AC_SUBST(INCLUDES)
dnl Zależności dla pkgconfig
AC_SUBST(REQUIRES_PRIVATE)
AC_SUBST(LIBS_PRIVATE)

export PKG_CONFIG_PATH="$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig"
PKG_PROG_PKG_CONFIG

if test "$GCC"; then
	CFLAGS="$CFLAGS -Wall -Wmissing-prototypes"
fi

AC_C_BIGENDIAN

if test "x$ac_cv_c_bigendian" = "xyes"; then
	AC_DEFINE([GG_CONFIG_BIGENDIAN], [], [Defined if libgadu was compiled for bigendian machine.])
fi

AC_NEED_STDINT_H

if test "x$STDINT_H" = "xstdint.h"; then
	AC_DEFINE([GG_CONFIG_HAVE_STDINT_H], [], [Defined if uintX_t types are defined in <stdint.h>.])
fi

if test "x$STDINT_H" = "xinttypes.h"; then
	AC_DEFINE([GG_CONFIG_HAVE_INTTYPES_H], [], [Defined if uintX_t types are defined in <inttypes.h>.])
fi

if test "x$STDINT_H" = "xsys/inttypes.h"; then
	AC_DEFINE([GG_CONFIG_HAVE_SYS_INTTYPES_H], [], [Defined if uintX_t types are defined in <sys/inttypes.h>.])
fi

if test "x$STDINT_H" = "xsys/int_types.h"; then
	AC_DEFINE([GG_CONFIG_HAVE_SYS_INT_TYPES_H], [], [Defined if uintX_t types are defined in <sys/int_types.h>.])
fi

if test "x$STDINT_H" = "xsys/types.h"; then
	AC_DEFINE([GG_CONFIG_HAVE_SYS_TYPES_H], [], [Defined if uintX_t types are defined in <sys/types.h>.])
fi

AC_DEFINE_UNQUOTED([GG_LIBGADU_VERSION], ["${VERSION}"], [Library version])

dnl
dnl  Sprawdźmy, jaką wersję vsnprintf() dostajemy. Dodatkowa opcja jest
dnl  dla kompilacji skrośnej, bo nie można wtedy korzystać z AC_TRY_RUN().
dnl

AC_ARG_WITH(c99-vsnprintf,
	[  --with-c99-vsnprintf    target system has C99-compatible vsnprintf()])

if test "x$with_c99_vsnprintf" = "xyes"; then
	AC_DEFINE([GG_CONFIG_HAVE_C99_VSNPRINTF], [], [Defined if this machine has C99-compiliant vsnprintf().])
fi

if test "x$with_c99_vsnprintf" = "x"; then
	AC_MSG_CHECKING([for C99-compatible vsnprintf()])
	if test "x$cross_compiling" = "xyes"; then
		AC_MSG_ERROR([cannot determine vsnprintf() behaviour when cross-compiling. You need to provide --with-c99-vsnprintf or --without-c99-vsnprintf option instead. It is safe to assume that the former is valid on most modern operating systems.])
	fi
	AC_TRY_RUN(
	[
		#include <stdio.h>
		int main()
		{
			char tmp;
			return (snprintf(&tmp, sizeof(tmp), "test") != 4);
		}
	], [
		AC_MSG_RESULT([yes])
		AC_DEFINE([GG_CONFIG_HAVE_C99_VSNPRINTF], [], [Defined if this machine has C99-compiliant vsnprintf().])
	], [
		AC_MSG_RESULT([no])
	])
fi

dnl
dnl  Sprawdzamy dostępność specyficznego dla Win32 _vscprintf(), które
dnl  pozwala oszczędzić użycie alokatora na MSVC, które nie posiada
dnl  vsnprintf() zgodnego z C99.
dnl

AC_CHECK_FUNCS([_vscprintf])

dnl
dnl  Sprawdzamy, czy jest system obsługuje 64-bitowe zmienne potrzebne dla
dnl  połączeń bezpośrednich.
dnl

AC_MSG_CHECKING([for long long])

AC_TRY_COMPILE([],
[
	long long a = 1LL;
	unsigned long long b = 1LL;
], [
	AC_MSG_RESULT([yes])
	AC_DEFINE([GG_CONFIG_HAVE_LONG_LONG], [], [Defined if this machine supports long long.])
])

AC_CHECK_FUNCS([strtoull])
AC_CHECK_FUNCS([_strtoui64])

AC_MSG_CHECKING([if <$STDINT_H> has uint64_t])
AC_TRY_COMPILE([#include <$STDINT_H>], [uint64_t foo],
    AC_DEFINE([HAVE_UINT64_T], [], [Defined if this machine has uint64_t .])
    AC_MSG_RESULT([yes]),
    AC_MSG_RESULT([no]))

dnl
dnl  SunOS
dnl

AC_CHECK_LIB(nsl, t_accept, LIBS_PRIVATE="$LIBS_PRIVATE -lnsl")
AC_CHECK_LIB(socket, socket, LIBS_PRIVATE="$LIBS_PRIVATE -lsocket")

dnl
dnl  BeOS
dnl

AC_ARG_WITH(bind,
	[  --without-bind          do not use libbind even if found])

if test "x$with_bind" != "xno"; then
	AC_CHECK_LIB(bind, __inet_addr, LIBS_PRIVATE="$LIBS_PRIVATE -lbind")
fi

dnl
dnl  threadsafe
dnl

AC_CHECK_FUNCS([gethostbyname_r], [AC_DEFINE([GG_CONFIG_HAVE_GETHOSTBYNAME_R], [], [Defined if this machine has gethostbyname_r().])])

AC_MSG_CHECKING([for va_copy])
AC_TRY_LINK([#include <stdarg.h>], [va_list a, b; va_copy(a, b);], 
[
	AC_MSG_RESULT([yes])
	AC_DEFINE([GG_CONFIG_HAVE_VA_COPY], [], [Defined if this machine has va_copy().])
], [
	AC_MSG_RESULT([no])
])

AC_MSG_CHECKING([for __va_copy])
AC_TRY_LINK([#include <stdarg.h>], [va_list a, b; __va_copy(a, b);], 
[
	AC_MSG_RESULT([yes])
	AC_DEFINE([GG_CONFIG_HAVE___VA_COPY], [], [Defined if this machine has __va_copy().])
], [
	AC_MSG_RESULT([no])
])

AC_CHECK_FUNCS([_exit])

AC_CHECK_FUNCS([fork], [AC_DEFINE([GG_CONFIG_HAVE_FORK], [], [Defined if this machine has fork().])])

AC_ARG_ENABLE(debug, 
	[  --disable-debug         compile without debugging support])

if test "x$enable_debug" = "xno"; then 
	AC_MSG_WARN([--disable-debug is obsolete.])
fi

dnl
dnl  Resolver libgadu oparty na pthread
dnl

AC_ARG_WITH(pthread,
  [  --without-pthread       do not use pthread resolver even if found])

if test "x$with_pthread" != "xno"; then
	ACX_PTHREAD(
	[
		dnl workaround dla pkgconfiga
		if test "x$PTHREAD_CFLAGS" = "x-pthread"; then
			PTHREAD_LIBS="$PTHREAD_LIBS -pthread"
		else
			INCLUDES="$INCLUDES $PTHREAD_CFLAGS"
		fi

		LIBS_PRIVATE="$PTHREAD_LIBS $LIBS_PRIVATE"
		CFLAGS="$CFLAGS $PTHREAD_CFLAGS"
		CC="$PTHREAD_CC"
		have_pthread=yes

		AC_DEFINE([GG_CONFIG_HAVE_PTHREAD], [], [Defined if libgadu was compiled and linked with pthread support.])
	], [
		if test "x$with_pthread" = "xyes"; then
			AC_MSG_ERROR([Your system is not supporting pthreads])
		fi
	])
fi

if test "x$with_pthread" = "xyes"; then
	AC_DEFINE([GG_CONFIG_PTHREAD_DEFAULT], [], [Defined if pthread resolver is the default one.])
fi

dnl
dnl  Sprawdzamy zlib
dnl

AC_CHECK_LIB(z, inflateEnd, [have_zlib=yes], [have_zlib=no])
AC_CHECK_HEADER(zlib.h, [have_zlib_h=yes], [have_zlib_h=no])

if test "x$have_zlib" = "xyes" -a "x$have_zlib_h" = "xyes"; then
	LIBS_PRIVATE="$LIBS_PRIVATE -lz"
	AC_DEFINE(HAVE_ZLIB, 1, [define if you have zlib])
	AC_DEFINE([GG_CONFIG_HAVE_ZLIB], [], [Defined if libgadu was compiled and linked with zlib support.])
fi

dnl
dnl  Sprawdzamy GnuTLS
dnl

AC_ARG_WITH(gnutls,
  [  --without-gnutls        do not use GnuTLS even if found])

have_gnutls=""

if test "x$with_gnutls" != "xno"; then
	PKG_CHECK_MODULES([GNUTLS], [gnutls >= 2.10.0], [
		AC_DEFINE([HAVE_GNUTLS], [], [Defined if GnuTLS is available.])
		AC_DEFINE([GG_CONFIG_HAVE_GNUTLS], [], [Defined if libgadu was compiled and linked with GnuTLS support.])
		REQUIRES_PRIVATE="$REQUIRES_PRIVATE gnutls"
		CFLAGS="$CFLAGS $GNUTLS_CFLAGS"
		# Note: not adding to LIBS_PRIVATE, because
		# "REQUIRES_PRIVATE" takes care of that for pkg-config.
		LIBS="$LIBS $GNUTLS_LIBS"
		have_gnutls="yes"
	], [:])
fi

dnl
dnl  Sprawdzamy OpenSSL, jeśli nie ma GnuTLS
dnl

if test "x$have_gnutls" != "xyes"; then
	AC_CHECK_OPENSSL
fi

if test "x$have_openssl" = "xyes"; then
	# Note: not adding to LIBS_PRIVATE, because "REQUIRES_PRIVATE" takes care
	# of that for pkg-config.
	LIBS="$LIBS $OPENSSL_LIBS"
	CFLAGS="$CFLAGS $OPENSSL_CFLAGS"
	REQUIRES_PRIVATE="$REQUIRES_PRIVATE openssl"
	AC_DEFINE([GG_CONFIG_HAVE_OPENSSL], [], [Defined if libgadu was compiled and linked with OpenSSL support.])
fi

# We need separate lists of libs handled with and without pkgconfig help, for the pkgconfig file.
# We concatenate them here so that libtool can pick the pkgconfig-less libs too.
LIBS="$LIBS $LIBS_PRIVATE"

dnl
dnl  Testy
dnl

AC_SUBST([SUBDIRS_TEST])
AC_SUBST([OPTIONAL_TESTS_AUTOMATIC_GLIBC])
AC_SUBST([OPTIONAL_TESTS_AUTOMATIC_GLIBC_GNUTLS])
AC_SUBST([OPTIONAL_TESTS_MANUAL_GLIBC])
AC_SUBST([OPTIONAL_TESTS_PERL])
AC_SUBST([OPTIONAL_TESTS_VOICE7])
AC_SUBST([OPTIONAL_TESTS_SEARCH])
SUBDIRS_TEST=""
OPTIONAL_TESTS_AUTOMATIC_GLIBC=""
OPTIONAL_TESTS_AUTOMATIC_GLIBC_GNUTLS=""
OPTIONAL_TESTS_MANUAL_GLIBC=""
OPTIONAL_TESTS_PERL=""
OPTIONAL_TESTS_VOICE7=""
OPTIONAL_TESTS_SEARCH=""

AC_ARG_ENABLE(tests,
        [  --disable-tests         do not compile tests])

if test "x$enable_tests" != "xno"; then
	SUBDIRS_TEST="test"

	dnl
	dnl  Sprawdź, czy można korzystać z __funkcji w libc.
	dnl 

	have_glibc=""

	AC_CHECK_LIB(c, __connect, [have_glibc="yes"])
	
	if test "x$have_glibc" = "xyes"; then
		OPTIONAL_TESTS_AUTOMATIC_GLIBC="resolver packet"
		OPTIONAL_TESTS_MANUAL_GLIBC="dcc7"
		
		dnl For tests always try to use GnuTLS if available
		if test "x$have_gnutls" != "xyes"; then
			PKG_CHECK_MODULES([GNUTLS], [gnutls >= 2.10.0], [
				AC_DEFINE([HAVE_GNUTLS], [], [Defined if GnuTLS is available.])
				have_gnutls="yes"
			], [:])
		fi

		if test "x$have_gnutls" = "xyes"; then
			OPTIONAL_TESTS_AUTOMATIC_GLIBC_GNUTLS="connect"
		fi
	fi

	dnl
	dnl  libgsm i libspeex dla voice7 (obecnie wyłączone)
	dnl

dnl	tmp=x
dnl	AC_CHECK_LIB(speex, speex_encoder_init, [tmp="x$tmp"])
dnl	AC_CHECK_HEADERS(speex/speex.h, [tmp="x$tmp"])
dnl	AC_CHECK_LIB(gsm, gsm_create, [tmp="x$tmp"])
dnl	AC_CHECK_HEADERS(gsm/gsm.h, [tmp="x$tmp"])
dnl
dnl	if test "x$tmp" = "xxxxxx"; then
dnl		OPTIONAL_TESTS_VOICE7="voice7"
dnl	fi

	dnl
	dnl  curl, expat i OpenSSL dla search
	dnl

	tmp=x
	AC_CHECK_LIB(curl, curl_easy_init, [tmp="x$tmp"])
	AC_CHECK_HEADERS(curl/curl.h, [tmp="x$tmp"])
	AC_CHECK_LIB(expat, XML_ParserCreate, [tmp="x$tmp"])
	AC_CHECK_HEADERS(expat.h, [tmp="x$tmp"])

	if test "x$tmp" = "xxxxxx"; then
		dnl For tests always try to use OpenSSL if available
		if test "x$have_openssl" != "xyes"; then
			force_openssl="yes"
			AC_CHECK_OPENSSL
		fi

		if test "x$have_openssl" = "xyes"; then
			OPTIONAL_TESTS_SEARCH="search"
		fi
	fi

	dnl
	dnl  Perl dla protocol
	dnl

	AC_CHECK_PROG([PERL], [perl], [perl], [:])
	if test "x$PERL" != "x:"; then
		OPTIONAL_TESTS_PERL="protocol"
	fi

	dnl
	dnl  Sprawdź, czy mamy libxml2 do testów automatycznych
	dnl
	AC_SUBST([LIBXML2_CFLAGS])
	AC_SUBST([LIBXML2_LIBS])
	PKG_CHECK_MODULES([LIBXML2], [libxml-2.0], [
		AC_DEFINE([HAVE_LIBXML2], [], [Defined if libxml2 is available.])
	], [:])
fi

dnl
dnl  Sprawdź, czy mamy czym generować dokumentację
dnl

AC_CHECK_PROG([DOXYGEN], [doxygen], [doxygen], [:])

AC_SUBST([SUBDIRS_DOXYGEN])
if test "x$DOXYGEN" = "xdoxygen"; then
	SUBDIRS_DOXYGEN="docs"
	AC_SUBST([DOXYFILE_HTML_DIR])
	DOXYFILE_HTML_DIR="html"
else
	SUBDIRS_DOXYGEN=""
fi

dnl
dnl  Egzotyczne systemy operacyjne
dnl
case "$host" in
	*-*-mingw* | *-*-cygwin*)
		MINGW_LDFLAGS="-no-undefined -lws2_32"
		;;
	*)
		MINGW_LDFLAGS=""
		;;
esac
AC_SUBST([MINGW_LDFLAGS])

AC_CONFIG_FILES([Makefile src/Makefile include/Makefile pkgconfig/Makefile pkgconfig/libgadu.pc docs/Makefile docs/Doxyfile examples/Makefile test/Makefile test/automatic/Makefile test/automatic/script/Makefile test/manual/Makefile test/manual/lib/Makefile])

AC_OUTPUT
