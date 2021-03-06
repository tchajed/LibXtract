AC_PREREQ(2.13)
# Increment for major API changes, release status changes
m4_define(libxtract_major_version, 0)
# Increment for feature additions and enhancements
m4_define(libxtract_minor_version, 6)
# Increment for fixes 
m4_define(libxtract_fix_version, 6)
# Development status
m4_define(libIntegra_dev_status, [beta])

m4_define(libxtract_version, libxtract_major_version.libxtract_minor_version.libxtract_fix_version)

PACKAGE=libxtract

AC_INIT([libxtract], [libxtract_version], [libxtract-devel@lists.sourceforge.net])
AC_DEFINE(LIBXTRACT_VERSION, libxtract_version, [LibXtract Version])
dnl AM_INIT_AUTOMAKE($PACKAGE, $LIBXTRACT_VERSION)
AM_INIT_AUTOMAKE(1.6)
AM_CONFIG_HEADER(config.h)
AC_CONFIG_MACRO_DIR([m4])
AC_PROG_CC
AC_PROG_LIBTOOL
AC_PROG_INSTALL
AC_C_BIGENDIAN
AC_PATH_PROG(PKG_CONFIG, pkg-config, no)
AC_ENABLE_STATIC(no)
AC_ENABLE_SHARED(yes)
AC_PROG_LIBTOOL
AC_CHECK_HEADERS([math.h, stdlib.h, stdio.h])
AC_CHECK_PROG([DOXYGEN], [doxygen], [doc], [])
AC_SUBST(DOXYGEN)

AC_ARG_ENABLE(fft, 
              [  --enable-fft    Turn fft-based fft processing on],
              [case "${enableval}" in
               yes) fft=true ;;
               no)  fft=false ;;
               *) AC_MSG_ERROR(bad value ${enableval} for --enable-fft) ;;
           esac],[fft=false])

AC_ARG_ENABLE(pd_example, 
              [  --enable-pd_example    Compile the Pure Data external example],
              [case "${enableval}" in
               yes) pd_example=true ;;
               no)  pd_example=false ;;
               *) AC_MSG_ERROR(bad value ${enableval} for --enable-pd_external) ;;
           esac],[pd_example=false])
	   
AC_ARG_ENABLE(simpletest, 
              [  --enable-simpletest    Compile the 'simpletest' example],
              [case "${enableval}" in
               yes) simpletest=true ;;
               no)  simpletest=false ;;
               *) AC_MSG_ERROR(bad value ${enableval} for --enable-simpletest) ;;
           esac],[simpletest=false])

# libtool version: current:revision:age
#
# If the library source code has changed at all since the last update, then
# increment revision (`c:r:a' becomes `c:r+1:a').
#
# If any interfaces have been added, removed, or changed since the last update,
# increment current, and set revision to 0.
#
# If any interfaces have been added since the last public release, then
# increment age.
#
# If any interfaces have been removed since the last public release, then set
# age to 0.
XTRACT_SO_VERSION=0:0:0

CFLAGS="$CFLAGS -pedantic -ansi -fno-strict-aliasing -Wall -std=c99 -I/usr/local/include"
LDFLAGS="$LDFLAGS -lm"

AC_ARG_WITH(pd_dir,
    [  --with-pd-dir=path   pd header path (default=/usr/local/include) ],
    [
    CFLAGS="$CFLAGS -I$withval"
    echo
    echo "pd dir is $withval"
    echo
    ])

AC_ARG_WITH(fftw3_dir,
    [  --with-fftw3-dir=path   fftw3 header path (default=/usr/local/include) ],
    [
    CFLAGS="$CFLAGS -I$withval"
    echo
    echo "fftw3 dir is $withval"
    echo
    ])

dnl Set FFT optimisation level
AC_ARG_WITH(fft_optimisation,
    [  --with-fft_optimisation=level    set fft optimistaion level (default=1)],
    [
    FFT_OPTIMISATION="$withval"
    echo
    echo "fft optimisation level is $withval"
    echo
    ])

dnl set a specific java compiler
AC_ARG_WITH(javac,
	[  --with-javac=compiler  set a specific java compiler (determined automatically if not set) ],
	[JAVAC="$withval"
	echo
	echo "JAVAC is set to $withval"
	echo
	])

dnl If --enable-swig, make with java bindings
AC_ARG_WITH(java,
    [ --with-java If --enable-swig - make with java bindings (default=no) ],
    [with_java=true])

AM_CONDITIONAL(BUILD_JAVA, test "x${with_java}" = 'xtrue')

dnl If --enable-swig, make with java bindings
AC_ARG_WITH(python,
    [ --with-python If --enable-swig - make with python bindings (default=no) ],    [with_python=true])

AM_CONDITIONAL(BUILD_PYTHON, test "x${with_python}" = 'xtrue')


dnl are we building the simpletest example
if [[ "$simpletest" = "true" ]] ; then
    AC_DEFINE([BUILD_SIMPLETEST], [1], [Build the simpletest example])
fi

AM_CONDITIONAL(BUILD_SIMPLETEST, test "x${simpletest}" = 'xtrue')

dnl Are we building the PD examples?
if [[ "$pd_example" = "true" ]] ; then
    PD_SOURCES="xtract~.c"
    AC_DEFINE([BUILD_PD_EXAMPLE], [1], [Build the pd example])
    AC_CHECK_HEADER(m_pd.h, [have_pd_hdr=yes ], [
    have_pd_hdr=no
    echo
    echo "no m_pd.h header found.  try with option --with-pd-dir=/path/to/pd/src"
    echo
    exit
    ])
fi

AM_CONDITIONAL(BUILD_PD_EXAMPLE, test "x${pd_example}" = 'xtrue')


dnl Enable debugging (no)
AC_ARG_ENABLE(debug,
  [  --enable-debug[[=value]]   compile with debug [[default=no]]],
  with_debug="yes",
  with_debug="no")
if test "$with_debug" = "yes"
then
  AC_DEFINE(DEBUG,1,[Define to enable debug])
  CFLAGS="$CFLAGS -O0 -ggdb -g -Werror"
fi

AC_ARG_ENABLE(swig,
       [  --enable-swig    Generate swig bindings],
       [case "${enableval}" in
        yes) swig=true ;;
        no)  swig=false ;;
        *) AC_MSG_ERROR(bad value ${enableval} for --enable-swig) ;;
    esac],[swig=false])



if [[ "$with_java" = "true" ]] ; then
    if test "$JAVAC" = ""
    then
	AC_PROG_JAVAC
    fi
    if test "$JAVAC" = "javac"
    then
	AC_JNI_INCLUDE_DIR
    
	for JNI_INCLUDE_DIR in $JNI_INCLUDE_DIRS
	do
	    CFLAGS="$CFLAGS -I$JNI_INCLUDE_DIR"
	done
    fi
   dnl AC_PROG_JAVAH
   dnl AC_PATH_PROG(JAVAH,javah)
fi

if [[ "$with_python" = "true" ]] ; then
    AM_PATH_PYTHON
    SWIG_PYTHON
fi


AM_CONDITIONAL(BUILD_SWIG, test "x${swig}" = 'xtrue')

dnl SWIG stuff
if [[ "$swig" = "true" ]] ; then
    AX_PKG_SWIG(1.3.21, [], [ AC_MSG_ERROR([SWIG is not installed]) ])
    AC_DEFINE([BUILD_SWIG], [1], [Build the swig bindings])
fi

dnl Are we building with fftw?
if [[ "$fft" = "true" ]] ; then
    LDFLAGS="$LDFLAGS -lfftw3f"
    AC_DEFINE([BUILD_FFT], [1], [Build the fft functions])
    if test "$FFT_OPTIMISATION" = ""
    then
        AC_DEFINE([XTRACT_FFT_OPTIMISATION_LEVEL], [1], [fft optimisation 1])
    else
       # AC_SUBST(OPTIMISATION_LEVEL, "$FFT_OPTIMISATION")
        AC_DEFINE_UNQUOTED(XTRACT_FFT_OPTIMISATION_LEVEL, ${FFT_OPTIMISATION})
    fi
    AC_CHECK_HEADER(fftw3.h, [have_fftw3_hdr=yes ], [
    have_pd_hdr=no
    echo
    echo "no fftw3.h header found.  try with option --with-fftw3-dir=/path/to/fftw3/header"
    echo
    exit
    ])
fi

AM_CONDITIONAL(BUILD_FFT, test "x${fft}" = 'xtrue')

dnl Check for architecture endian-ness
#AC_C_BIGENDIAN(bigendian=true, bigendian=false, bigendian=undefined)
#if [[ "$is_bigendian" = "false" ]] ; then
#    AC_DEFINE([WORDS_BIGENDIAN], [0], [Architecture is big endian])
#else
#    AC_DEFINE([WORDS_BIGENDIAN], [1], [Architecture is not big endian])
#fi


dnl ------------------------------------------
dnl ----  do some magic to gues the host opsys
dnl ----  taken from libvorbis configure.in
dnl ------------------------------------------
dnl AC_CANONICAL_HOST

dnl AC_SUBST(PD_CFLAGS,"$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused -Wmissing-prototypes -O1")
dnl AC_SUBST(PD_LDFLAGS,"$PD_LDFLAGS -shared")
dnl pd_ldflags="$PD_LDFLAGS -L/usr/local/lib -ldl"
if test -z "$GCC"; then
        case $host in
        *-*-irix*)
            dnl If we're on IRIX, we wanna use cc even if gcc
            dnl is there (unless the user has overriden us)...
            if test -z "$CC"; then
                    CC=cc
            fi
        ;;
        sparc-sun-solaris*)
            PD_CFLAGS="-xO4 -fast -w -fsimple -native -xcg92"
        ;;
        *)
            PD_CFLAGS="-O"
        ;;
        esac
else

        case $host in
        *86-*-linux*)
            PD_CFLAGS="$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused"
            PD_LDFLAGS="$PD_LDFLAGS -shared"
            dnl we could test for bad glibc here, but don't
            PD_SUFFIX=pd_linux
        ;;
        powerpc-*-linux*)
            PD_CFLAGS="$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused -Wmissing-prototypes"
            PD_LDFLAGS="$PD_LDFLAGS -shared"
            PD_SUFFIX=pd_linux
        ;;
        *-*-linux*)
            PD_CFLAGS="$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused -Wmissing-prototypes -fPIC"
            PD_LDFLAGS="$PD_LDFLAGS -shared -export_dynamic"
            PD_SUFFIX=pd_linux
        ;;
        sparc-sun-*)
            echo "YOU HAVE A SPARC STATION, not setting any flags, not supported yet"
        ;;
        dnl *86-*-darwin*)
        dnl    PD_CFLAGS="$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused -Wmissing-prototypes"
        dnl    PD_LDFLAGS="$PD_LDFLAGS -dynamiclib -mmacosx-version-min=10.5 -undefined dynamic_lookup -single_module"
        dnl    PD_SUFFIX=pd_darwin
	dnl ;;
	*-*-darwin*)
            PD_CFLAGS="$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused -Wmissing-prototypes"
            PD_LDFLAGS="$PD_LDFLAGS -bundle -undefined dynamic_lookup -flat_namespace"
            PD_SUFFIX=pd_darwin
	;;

        *)
        dnl assume unix
            PD_CFLAGS="$PD_CFLAGS -DUNIX -Wall -Wimplicit -Wunused -Wmissing-prototypes -O1"
            PD_LDFLAGS="$PD_LDFLAGS -shared"
            PD_SUFFIX=pd_linux
        ;;
        esac
fi

PD_CFLAGS="$PD_CFLAGS -DPD"

AC_SUBST(PD_CFLAGS)
AC_SUBST(PD_LDFLAGS)
AC_SUBST(PD_SUFFIX)
AC_SUBST(PD_SOURCES)
		
AC_CONFIG_FILES([doc/documentation.doxygen
				libxtract.pc])

dnl There must be a better way to do this...
AC_OUTPUT(Makefile src/Makefile xtract/Makefile doc/Makefile examples/Makefile examples/puredata/Makefile examples/simpletest/Makefile swig/Makefile swig/python/Makefile swig/java/Makefile)

echo
echo "**************************************************************"
echo
echo "Summary:"
echo
dnl echo you are using the ${host} architecture 

if test "$fft" = "true"; then
  echo "fft:                   yes (using fftw3f)"
else
  echo "fft:                   no (not using fftw3, no fft functions)"
fi
if test "$simpletest" = "true"; then
  echo "simpletest example:	yes"
else
  echo "simpletest example:     no"
fi
if test "$pd_example" = "true"; then
  echo "PD external:             yes"
  echo 
  echo "The PD help files will be installed in:" 
  echo ${prefix}"/lib/doc/5.reference/xtract/" 
  echo "You must make sure that this is in your PD help path"
else
  echo "PD external:             no"
fi
if test "$swig" == "true"; then
  echo "SWIG bindings:                      yes"
else
  echo "SWIG bindings:                      no"
fi
if test "$with_java" == "true"; then
    echo "with JAVA module:		    yes"
else
    echo "with JAVA module:		    no"
fi
echo
echo "**************************************************************"
echo
echo Configuration completed successfully. Type \'make\' to build ${PACKAGE}
echo


