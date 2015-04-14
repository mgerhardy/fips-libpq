get_filename_component(FIPS_LIBPQ_ROOT_DIR "." ABSOLUTE)
include(CheckFunctionExists)
include(CheckIncludeFile)
include(CheckStructHasMember)
include(CheckTypeSize)

check_include_file(string.h HAVE_STRING_H)
check_include_file(strings.h HAVE_STRINGS_H)

fips_begin_lib(pq)
	include_directories(postgresql/src/include/libpq)
	include_directories(postgresql/src/include)

	fips_dir(postgresql/src/interfaces/libpq)
	fips_files(
		fe-auth.c
		fe-connect.c
		fe-exec.c
		fe-misc.c
		fe-print.c
		fe-lobj.c
		fe-protocol2.c
		fe-protocol3.c
		pqexpbuffer.c
		fe-secure.c
		libpq-events.c
	)
	fips_dir(postgresql/src/port)
	fips_files(
		chklocale.c
		inet_net_ntop.c
		noblock.c
		pgstrcasecmp.c
		pqsignal.c
		thread.c
		# libpgport C files that are needed if identified by configure
		crypt.c
		inet_aton.c
		strerror.c
		strlcpy.c
	)
	fips_dir(postgresql/src/backend/libpq)
	fips_files(
		ip.c
		md5.c
	)
	fips_dir(postgresql/src/backend/utils/mb)
	fips_files(
		encnames.c
		wchar.c
	)

	set(PG_INT64_TYPE "long int")
	set(HAVE_DECL_SNPRINTF 1)
	set(HAVE_DECL_SNPRINTF 1)
	set(HAVE_LONG_INT_64 1)
	set(FLEXIBLE_ARRAY_MEMBER 1)

	check_struct_has_member("struct tm" "tm_zone" "sys/types.h" HAVE_TM_ZONE)
#	check_struct_has_member("struct sockaddr_storage

	file(WRITE ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/port/pg_config_paths.h "
#define PGBINDIR \"$(bindir)\"
#define PGSHAREDIR \"$(datadir)\"
#define SYSCONFDIR \"$(sysconfdir)\"
#define INCLUDEDIR \"$(includedir)\"
#define PKGINCLUDEDIR \"$(pkgincludedir)\"
#define INCLUDEDIRSERVER \"$(includedir_server)\"
#define LIBDIR \"$(libdir)\"
#define PKGLIBDIR \"$(pkglibdir)\"
#define LOCALEDIR \"$(localedir)\"
#define DOCDIR \"$(docdir)\"
#define HTMLDIR \"$(htmldir)\"
#define MANDIR \"$(mandir)\"")

	if (FIPS_GCC)
		set(PG_PRINTF_ATTRIBUTE "gnu_printf")
	endif()

	if (FIPS_WINDOWS)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/port/win32.h ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_os.h COPYONLY)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_ext.h.win32 ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_ext.h COPYONLY)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config.h.win32 ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config.h COPYONLY)
		fips_dir(postgresql/src/port)
		fips_files(
			getaddrinfo.c
			snprintf.c
			pgsleep.c
			open.c
			system.c
			win32error.c
			win32setlocale.c
		)
		fips_dir(postgresql/src/interfaces/libpq)
		fips_files(
			win32.c
			#libpqrc.c
		)
		fips_libs(crypt des com_err crypto k5crypto krb5 gssapi_krb5 gss gssapi ssl socket nsl resolv intl shell32 ws2_32 secur32 eay32 ssleay32 comerr32 krb5_32)
	else()
		if (FIPS_MACOS)
			configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/port/darwin.h ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_os.h COPYONLY)
		else()
			configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/port/linux.h ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_os.h COPYONLY)
		endif()
		fips_dir(postgresql/src/port)
		fips_files(
			getpeereid.c
		)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/pg_config_ext.h.in ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_ext.h)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/pg_config.h.in ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config.h)
		fips_libs(crypt des com_err crypto k5crypto krb5 gssapi_krb5 gss gssapi ssl socket nsl resolv intl)
	endif()
fips_end_lib()
