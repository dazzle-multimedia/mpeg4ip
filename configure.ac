#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.61])
AC_INIT([mpeg4ip], [1.5.0.1])
AC_CONFIG_HEADER([config.h])
AC_CONFIG_AUX_DIR([build-aux])
AM_INIT_AUTOMAKE([-Wall -Werror no-dist-gzip dist-bzip2 1.10])
AM_MAINTAINER_MODE

# Checks for programs.
AC_PROG_LIBTOOL
AC_PROG_AWK
AC_PROG_CXX
AC_PROG_CC

# Checks for libraries.
AC_CHECK_LIB([SDL], [SDL_Init])
AC_CHECK_LIB([a52], [a52_init])
AC_CHECK_LIB([id3], [ID3Frame_GetID])
AC_CHECK_LIB([m], [sin])
AC_CHECK_LIB([mp4av], [MP4GetSampleTime])
AC_CHECK_LIB([mp4v2], [MP4Read])
AC_CHECK_LIB([mpeg2], [mpeg2_reset])
AC_CHECK_LIB([pthread], [pthread_create])
AC_CHECK_LIB([xvidcore], [xvid_global])
AC_CHECK_LIB([z], [deflate])

# Checks for header files.
AC_HEADER_DIRENT
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS([arpa/inet.h fcntl.h float.h inttypes.h libintl.h limits.h memory.h netdb.h netinet/in.h stdint.h stdlib.h string.h sys/file.h sys/ioctl.h sys/param.h sys/socket.h sys/time.h sys/timeb.h syslog.h unistd.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_HEADER_STDBOOL
AC_C_CONST
AC_C_INLINE
AC_TYPE_INT16_T
AC_TYPE_INT32_T
AC_TYPE_INT64_T
AC_TYPE_INT8_T
AC_TYPE_OFF_T
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_TYPE_SSIZE_T
AC_HEADER_TIME
AC_STRUCT_TM
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT64_T
AC_TYPE_UINT8_T
AC_C_VOLATILE

# Checks for library functions.
AC_FUNC_CLOSEDIR_VOID
AC_FUNC_FORK
AC_FUNC_FSEEKO
AC_PROG_GCC_TRADITIONAL
AC_FUNC_LSTAT
AC_FUNC_LSTAT_FOLLOWS_SLASHED_SYMLINK
AC_FUNC_MALLOC
AC_FUNC_MEMCMP
AC_FUNC_MMAP
AC_FUNC_REALLOC
AC_FUNC_SELECT_ARGTYPES
AC_FUNC_SETPGRP
AC_FUNC_STAT
AC_FUNC_STRFTIME
AC_FUNC_STRTOD
AC_FUNC_VPRINTF
AC_CHECK_FUNCS([floor ftruncate gethostbyname gethostname gettimeofday inet_ntoa localtime_r memmove memset mkdir mkfifo munmap pow select socket sqrt strcasecmp strchr strdup strerror strncasecmp strpbrk strrchr strstr strtol strtoul strtoull uname])


# FIXME: Need to check libraries and capabilities, not OS
dnl check for mac
SUN_LIBS=
have_macosx=false
case "$target" in
   *-apple-darwin*)
   have_macosx=true
   AC_DEFINE(HAVE_MACOSX, [1], [have mac])
   
   ;;
   *-*-solaris*)
     SUN_LIBS=-lstdc++
     ;;
   *)
   ;;
esac

if test $have_macosx = false; then
   AC_CONFIG_SUBDIRS(lib/SDLAudio)
fi
AM_CONDITIONAL(HAVE_MAC_OSX, $have_macosx)


AC_ARG_ENABLE(a52dec,
[AC_HELP_STRING([--disable-a52dec],[disable use of installed a52dec library])],
[ case "${enableval}" in
  no)  disable_a52=yes ;;
  yes) disable_a52=no ;;
  *) AC_MSG_ERROR([bad valud ${enableval} for --disable-a52]) ;;
esac],
  [disable_a52=no])

if test x$disable_a52 = xyes; then
  have_a52=false
  AC_MSG_NOTICE([disabling a52dec library])
else
   AC_CHECK_LIB(a52, 
                a52_syncinfo, 
	        [have_a52=true],
                [have_a52=false])
fi
AM_CONDITIONAL(HAVE_A52DEC_LIB, $have_a52)

AC_ARG_ENABLE(mpeg2dec,
[AC_HELP_STRING([--disable-mpeg2dec],[disable use of installed mpeg2dec library])],
[ case "${enableval}" in
  no)  disable_it=yes ;;
  yes) disable_it=no ;;
  *) AC_MSG_ERROR([bad valud ${enableval} for --disable-mpeg2dec]) ;;
esac],
  [disable_it=no])

if test x$disable_it = xyes; then
  have_mpeg2=false
  AC_MSG_NOTICE([disabling mpeg2dec library])
else
  AC_CHECK_LIB(mpeg2,
	       mpeg2_init,
	       [have_mpeg2=true],
	       [have_mpeg2=false])
  if test x$have_mpeg2 = xtrue; then
     AC_MSG_CHECKING([libmpeg2 version])
     AC_COMPILE_IFELSE([#include <stdint.h>
#include <mpeg2dec/mpeg2.h>

#ifndef MPEG2_VERSION
#error no mpeg2 version
#else
#if MPEG2_VERSION(0, 4, 0) <= MPEG2_RELEASE
int main (int argc, char **argv)
{
  return 0;
}
#else
#error prior release
#endif
#endif
],
	  [have_mpeg2=true],
	  [have_mpeg2=false])
     if test x$have_mpeg2 = xtrue; then
        AC_MSG_RESULT([yes])
     else
        AC_MSG_RESULT([no])
     fi
  fi
fi
AM_CONDITIONAL(HAVE_LIBMPEG2, $have_mpeg2)
if test $have_mpeg2 = true; then
   AC_DEFINE(HAVE_LIBMPEG2, [1], [have libmpeg2 decoder library])
fi


dnl Check for pre 1.0 API version of xvid
AC_ARG_ENABLE(xvid,
[AC_HELP_STRING([--disable-xvid],[disable xvid link])],
[ case "${enableval}" in
  no)  disable_xvid=yes ;;
  yes) disable_xvid=no ;;
  *) AC_MSG_ERROR([bad valud ${enableval} for --disable-xvid]) ;;
esac],
  [disable_xvid=no])

if test x$disable_xvid = xyes; then
  use_xvid10=false
  no_xvid=true
  AC_MSG_NOTICE([disabling xvid library])
else
AC_MSG_CHECKING([for xvid.h, API version 3])
AC_TRY_COMPILE([],
	[#include <xvid.h>
	 int ix = API_VERSION;],
	 [no_xvid=false],
	 [no_xvid=true])
use_xvid10=false
if test $no_xvid = false; then
    AC_MSG_RESULT(yes)
    AC_DEFINE(HAVE_XVID_H, [1], [have xvid.h])
else
   AC_MSG_RESULT(no)
   AC_CHECK_HEADER(xvid.h, [no_xvid=false], [noxvid=true])
   if test $no_xvid = false; then
      use_xvid10=true
      AC_DEFINE(HAVE_XVID10, [1], [have xvid.h with 1.0 API])
   fi
fi
fi
AM_CONDITIONAL(HAVE_XVID_1_0, $use_xvid10)
AM_CONDITIONAL(NO_XVID, $no_xvid)

dnl Allow server build to be disabled
AC_ARG_ENABLE(server, 
[AC_HELP_STRING([--enable-server],[enable server build])],
[ case "${enableval}" in
  yes) server=true ;;
  no)  server=false ;;
  *)   AC_MSG_ERROR(bad value ${enableval} for --enable-server) ;;
esac],
[server=true])
AM_CONDITIONAL(SERVER, test $server = true)

dnl Allow player build to be disabled
AC_ARG_ENABLE(player, 
[AC_HELP_STRING([--enable-player],[enable player build])],
[ case "${enableval}" in
  yes) player=true ;;
  no)  player=false ;;
  *)   AC_MSG_ERROR(bad value ${enableval} for --enable-player) ;;
esac],
[player=true])
AM_CONDITIONAL(PLAYER, [test x$player = xtrue])

dnl test for ffmpeg
have_ffmpeg=false
disable_ffmpeg=false
FFMPEG_INC=
FFMPEG_LIB=
AC_ARG_ENABLE(ffmpeg,
	       [AC_HELP_STRING([--enable-ffmpeg=<directory>],[enable ffmpeg for mp4live and specify ffmpeg home directory])],
	       [if test x$enableval = xno; then
	           disable_ffmpeg=true
		else
		   if test x$enableval != xyes; then
		     have_ffmpeg_arg=true
		   fi
		fi
	       ],
	       [have_ffmpeg_arg=false]
	       )

if test x$disable_ffmpeg = xfalse; then
  if test x$have_ffmpeg_arg = xtrue; then
     AC_MSG_NOTICE([have enable ffmpeg $enable_ffmpeg])
     FFMPEG_INC=-I${enable_ffmpeg}/libavcodec
     FFMPEG_LIB="${enable_ffmpeg}/libavcodec/libavcodec.a -lz"
     saveCFLAGS="$CFLAGS"
     CFLAGS="$CFLAGS $FFMPEG_INC"
     AC_CHECK_TYPES([AVRational], , , [#include <avcodec.h>])
     AC_CHECK_MEMBERS(AVCodecContext.time_base, , , [#include <avcodec.h>])
     CFLAGS=$saveCFLAGS
     have_ffmpeg=true
     AC_DEFINE(HAVE_FFMPEG, 1, [enable ffmpeg for mp4live])
  else
     saved_LIBS="$LIBS"
     LIBS="$LIBS -lm -lz"
     AC_CHECK_LIB(avutil, av_crc_init, [have_avutil=true], [have_avutil=false])
     if test x$have_avutil = xtrue; then
        LIBS="$LIBS -lavutil"
	ffmpeg_add_libs="-lavutil"
     else
        ffmpeg_add_libs=
     fi
     AC_CHECK_LIB(avcodec, avcodec_init, 
	          [have_ffmpeg=true], [have_ffmpeg=false])
     LIBS=$saved_LIBS
     if test x$have_ffmpeg = xtrue; then
        AC_DEFINE(HAVE_FFMPEG_INSTALLED, 1, [defined if ffmpeg is installed])
        FFMPEG_INC=
        FFMPEG_LIB="-lavcodec -lz $ffmpeg_add_libs"
        AC_CHECK_TYPES([AVRational], , , [#include <ffmpeg/avcodec.h>])
	AC_CHECK_MEMBERS(AVCodecContext.time_base, , , [#include <ffmpeg/avcodec.h>])
        AC_DEFINE(HAVE_FFMPEG, 1, [enable ffmpeg for mp4live])
     fi
  fi
else
  AC_MSG_NOTICE([disabling ffmpeg library])
fi
AC_SUBST(FFMPEG_INC)
AC_SUBST(FFMPEG_LIB)
AM_CONDITIONAL(HAVE_FFMPEG, [test x$have_ffmpeg = xtrue])

AC_ARG_ENABLE(mp4live,
              [AC_HELP_STRING([--enable-mp4live],[enable mp4live build on Linux [default=yes]])],
              , enable_mp4live=yes)

have_mp4live=false
if test x$enable_mp4live = xyes; then
   case "$target" in
   *-*-linux*)
	have_mp4live=true
	AC_ARG_ENABLE(mp4live-alsa,
		      [AC_HELP_STRING([--enable-mp4live-alsa],
				      [enable ALSA interface in mp4live])],
		      [ case "${enableval}" in
		        no)  enable_alsa=no ;;
			yes) enable_alsa=yes ;;
			*) AC_MSG_ERROR(bad value ${enableval} for --enable-mp4live-alsa) ;;
			esac],
		      [ enable_alsa=no])
	if test x$enable_alsa = xyes; then 
	dnl - CheckAlsa - from SDL
	    AM_PATH_ALSA(0.9.0, [have_alsa=yes], [have_alsa=no])
	    if test x$have_alsa = xyes; then
	       AC_CHECK_FUNCS(snd_pcm_hw_params_set_rate_resample)
	       dnl - not needed AC_SUBST(ALSA_CFLAGS) - gotten by SDL_CFLAGS
	       dnl - not needed AC_SUBST(ALSA_LIBS) - gotten by SDL_LIBS
	       AC_DEFINE(HAVE_ALSA, [1], [have alsa enabled for mp4live])
	    fi
	    CFLAGS="$alsa_save_CFLAGS"
	    LDFLAGS="$alsa_save_LDFLAGS"
	    LIBS="$alsa_save_LIBS"
	    dnl - restore flags
	fi

	AC_CHECK_TYPE([struct v4l2_buffer], 
                      [AC_DEFINE(HAVE_MP4LIVE, 1, [mp4live can be used])],
	              [have_mp4live=false],
		        [#include <sys/time.h>
#include <sys/types.h>
#include <linux/videodev.h>])
	if test x$have_mp4live = xfalse; then
	    KERNEL_VERSION=`uname -r`
	    AC_CHECK_FILE(/lib/modules/$KERNEL_VERSION/build/include/linux/videodev2.h,
	                  [AC_MSG_WARN([])
			   AC_MSG_WARN([])
			  AC_MSG_WARN([We cannot locate videodev2.h in /usr/include/linux])
			  AC_MSG_WARN([])
			  AC_MSG_WARN([This file is responsible for V4L2 in mp4live])
			   AC_MSG_WARN([This file is correct in /lib/modules/$KERNEL_VERSION/build/include])
			   AC_MSG_WARN([It is recommended that you copy this file and videodev.h to /usr/include/linux and re-run bootstrap])
			   AC_MSG_WARN([You may also have to have to copy compiler.h, as well])
			   AC_MSG_WARN([])
			   AC_MSG_WARN([This is known to happen on Fedora Core systems])
			   AC_MSG_WARN([Mp4live has been disabled])
			   AC_MSG_WARN([])
			   ])
	fi
	;;
    *)
	;;
   esac
fi
AM_CONDITIONAL(MP4LIVE, [test x$have_mp4live = xtrue])

dnl read out glib and gtk paths and flags.  Will need to dummy this
dnl out for a glibless system
AC_ARG_ENABLE(gtk-glib,
[AC_HELP_STRING([--disable-gtk-glib],[disable gtk check])],
[ case "${enableval}" in
  no) disable_gtk_glib=yes ;;
  yes)  disable_gtk_glib=no ;;
  *) AC_MSG_ERROR([bad valud ${enableval} for --disable-gtk-glib]) ;;
esac],
  [disable_gtk_glib=no])

glib_gtk=no
if test x$disable_gtk_glib = xno; then
  AM_PATH_GLIB_2_0(, [haveglib20=yes], [haveglib20=no])
  if test x$haveglib20 = xyes; then 
   AM_PATH_GTK_2_0(, [havegtk20=yes], [havegtk20=no])
   if test x$havegtk20 = xyes; then 
      AC_DEFINE(HAVE_GTK_2_0, 1, [Have GTK 2.0 on system])
      AC_DEFINE(HAVE_GTK, 1, [Have GTK on system])
      glib_gtk=yes
   fi
  fi
  dnl If we're going to check for GLIB, we most likely need the real path
  dnl we need to save off the CFLAGS and CPPFLAGS for the AC_CHECK_HEADER
  dnl command 
  save_CFLAG="$CFLAGS"
  save_CPPFLAG="$CPPFLAGS"
  CFLAGS="$CFLAGS $GLIB_CFLAGS"
  CPPFLAGS="$CPPFLAGS $GLIB_CFLAGS"
  AC_CHECK_HEADERS(glib.h)
  CFLAGS="$save_CFLAG"
  CPPFLAGS="$save_CPPFLAG"
fi
if test x$glib_gtk = xno; then
  GLIB_CFLAGS=""
  GLIB_LIBS=""
  AC_SUBST(GLIB_CFLAGS)
  AC_SUBST(GLIB_LIBS)
      GTK_CFLAGS=""
      GTK_LIBS=""
      AC_SUBST(GTK_CFLAGS)
      AC_SUBST(GTK_LIBS)
fi
AM_CONDITIONAL(NO_GLIB_GTK, test x$glib_gtk = xno)

AC_ARG_ENABLE(id3tags,
[AC_HELP_STRING([--disable-id3tags],[disable use of installed id3tags library])],
[ case "${enableval}" in
  no)  disable_it=yes ;;
  yes) disable_it=no ;;
  *) AC_MSG_ERROR([bad valud ${enableval} for --disable-id3tags]) ;;
esac],
  [disable_it=no])

if test x$disable_it = xyes; then
  have_id3_tag=false
  AC_MSG_NOTICE([disabling id3 tag library])
else
  AC_LANG_PUSH(C++)
  AC_CHECK_HEADER(id3/tag.h, [have_id3_tag=true], [have_id3_tag=false])
  if test x$have_id3_tag = xtrue; then
    saved_LIBS="$LIBS"
    LIBS="$LIBS -lid3 -lz"
    AC_LINK_IFELSE(
   [#include <id3/tag.h>
    #include <id3/misc_support.h>
    int main () {
       ID3_GetAlbum((const ID3_Tag *)NULL);
       return 0;
    }
    ],
    
    [have_id3_tag=true], [have_id3_tag=false])
    LIBS=$saved_LIBS
  fi
  AC_LANG_POP(C++)
fi
if test $have_id3_tag = true; then
   dnl should check for libraries here
   AC_DEFINE(HAVE_ID3_TAG_H, [1], [have id3/tag.h])
fi
AM_CONDITIONAL(HAVE_ID3_TAG, $have_id3_tag)

AC_CONFIG_FILES([Makefile
                 common/Makefile
                 common/video/Makefile
                 doc/Makefile
                 doc/ietf/Makefile
                 doc/mcast/Makefile
                 doc/mp4v2/Makefile
                 doc/programs/Makefile
                 include/Makefile
                 lib/Makefile
                 lib/audio/Makefile
                 lib/avi/Makefile
                 lib/fposrec/Makefile
                 lib/gnu/Makefile
                 lib/ismacryp/Makefile
                 lib/mp4/Makefile
                 lib/mp4/doc/Makefile
                 lib/mp4av/Makefile
                 lib/mp4v2/Makefile
                 lib/mp4v2/test/Makefile
                 lib/mp4v2/util/Makefile
                 lib/mpeg2ps/Makefile
                 lib/mpeg2t/Makefile
                 lib/msg_queue/Makefile
                 lib/sdp/Makefile
                 lib/utils/Makefile
                 player/Makefile
                 player/lib/Makefile
                 player/lib/audio/Makefile
                 player/lib/audio/celp/BS/Makefile
                 player/lib/audio/celp/Makefile
                 player/lib/audio/celp/dec/Makefile
                 player/lib/audio/celp/include/Makefile
                 player/lib/audio/faad/Makefile
                 player/lib/audio/mp3util/Makefile
                 player/lib/audio/portaudio/Makefile
                 player/lib/libhttp/Makefile
                 player/lib/mp4util/Makefile
                 player/lib/rtsp/Makefile
                 player/plugin/Makefile
                 player/plugin/audio/Makefile
                 player/plugin/audio/a52dec/Makefile
                 player/plugin/audio/celp/Makefile
                 player/plugin/audio/faad2/Makefile
                 player/plugin/audio/ffmpeg/Makefile
                 player/plugin/audio/g711/Makefile
                 player/plugin/audio/raw/Makefile
                 player/plugin/rtp/Makefile
                 player/plugin/rtp/h261/Makefile
                 player/plugin/rtp/h264/Makefile
                 player/plugin/rtp/isma_audio/Makefile
                 player/plugin/rtp/isma_href/Makefile
                 player/plugin/rtp/isma_video/Makefile
                 player/plugin/rtp/rfc2429/Makefile
                 player/plugin/rtp/rfc3016/Makefile
                 player/plugin/rtp/rfc3267/Makefile
                 player/plugin/text/Makefile
                 player/plugin/text/href/Makefile
                 player/plugin/text/plain/Makefile
                 player/plugin/video/Makefile
                 player/plugin/video/ffmpeg/Makefile
                 player/plugin/video/h261/Makefile
                 player/plugin/video/mpeg2dec/Makefile
                 player/plugin/video/raw/Makefile
                 player/plugin/video/xvid/Makefile
                 player/src/Makefile
                 player/src/codec/Makefile
                 player/src/codec/aa/Makefile
                 player/src/codec/mp3/Makefile
                 player/src/codec/mpeg4/Makefile
                 player/src/codec/wav/Makefile
                 player/src/win_client/Makefile
                 player/src/win_common/Makefile
                 player/src/win_gui/Makefile
                 player/src/win_gui/hlp/Makefile
                 player/src/win_gui/res/Makefile
                 server/Makefile
                 server/mp4creator/Makefile
                 server/mp4live/Makefile
                 server/mp4live/gui/Makefile
                 server/mp4live/h261/Makefile
                 server/util/Makefile
                 server/util/avi2raw/Makefile
                 server/util/avidump/Makefile
                 server/util/lboxcrop/Makefile
                 server/util/mp4encode/Makefile
                 server/util/rgb2yuv/Makefile
                 server/video/Makefile
                 util/Makefile
                 util/h264/Makefile
                 util/iptv/Makefile
                 util/mpeg2/Makefile
                 util/mpeg4vol/Makefile
                 util/yuv/Makefile])
AC_CONFIG_SUBDIRS([common/video/iso-mpeg4])
AC_OUTPUT
