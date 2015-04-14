get_filename_component(FIPS_LIBPQ_ROOT_DIR "." ABSOLUTE)

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

	if (FIPS_WINDOWS)
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
		fips_dir(postgresql/src/port)
		fips_files(
			getpeereid.c
		)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_ext.h.in ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config_ext.h)
		configure_file(${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config.h.in ${FIPS_LIBPQ_ROOT_DIR}/postgresql/src/include/pg_config.h)
		fips_libs(crypt des com_err crypto k5crypto krb5 gssapi_krb5 gss gssapi ssl socket nsl resolv intl)
	endif()
fips_end_lib()
