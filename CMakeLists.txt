project(openpam)

include(InstallSymlink)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -msse -msse2 -msse3 -nostdinc -fblocks -fPIC")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse -msse2 -msse3 -nostdinc -nostdinc++ -fblocks -fPIC")

add_definitions(
	-D__DARWIN_UNIX03
	-DLIB_MAJ=2
	-DOPENPAM_MODULES_DIR='/usr/lib/pam/\\$<SEMICOLON>/usr/local/lib/pam/'
	-D_GNU_SOURCE
)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_CURRENT_SOURCE_DIR}/include

	${DARLING_TOP_DIRECTORY}/platform-include
)

set(DYLIB_INSTALL_NAME "/usr/lib/libpam.2.dylib")
add_darling_library(pam.2 SHARED
	lib/openpam_borrow_cred.c
	lib/openpam_configure.c
	lib/openpam_dispatch.c
	lib/openpam_dynamic.c
	lib/openpam_findenv.c
	lib/openpam_free_data.c
	lib/openpam_free_envlist.c
	lib/openpam_get_option.c
	lib/openpam_load.c
	lib/openpam_log.c
	lib/openpam_nullconv.c
	lib/openpam_readline.c
	lib/openpam_restore_cred.c
	lib/openpam_set_option.c
	lib/openpam_static.c
	lib/openpam_ttyconv.c
	lib/pam_acct_mgmt.c
	lib/pam_authenticate.c
	lib/pam_chauthtok.c
	lib/pam_close_session.c
	lib/pam_end.c
	lib/pam_error.c
	lib/pam_get_authtok.c
	lib/pam_get_data.c
	lib/pam_get_item.c
	lib/pam_get_user.c
	lib/pam_getenv.c
	lib/pam_getenvlist.c
	lib/pam_info.c
	lib/pam_open_session.c
	lib/pam_prompt.c
	lib/pam_putenv.c
	lib/pam_set_data.c
	lib/pam_set_item.c
	lib/pam_setcred.c
	lib/pam_setenv.c
	lib/pam_start.c
	lib/pam_strerror.c
	lib/pam_verror.c
	lib/pam_vinfo.c
	lib/pam_vprompt.c
	lib/pam_unsetenv.c
)
make_fat(pam.2)

set(DYLIB_INSTALL_NAME "/usr/lib/libpam.1.dylib")
add_darling_library(pam.1 SHARED
	compat/pam_shim.c
)
make_fat(pam.1)

set(DYLIB_INSTALL_NAME "/usr/lib/pam/pam_deny.so.2")
add_darling_library(pam_deny SHARED
	modules/pam_deny/pam_deny.c
)
make_fat(pam_deny)
set_target_properties(pam_deny PROPERTIES
	SUFFIX ".so"
	PREFIX ""
)

set(DYLIB_INSTALL_NAME "/usr/lib/pam/pam_permit.so.2")
add_darling_library(pam_permit SHARED
	modules/pam_permit/pam_permit.c
)
make_fat(pam_permit)
set_target_properties(pam_permit PROPERTIES
	SUFFIX ".so"
	PREFIX ""
)


target_link_libraries(pam.2 system)
target_link_libraries(pam.1 system)
target_link_libraries(pam_deny system)
target_link_libraries(pam_permit system)

install(TARGETS pam.1 pam.2 DESTINATION libexec/darling/usr/lib)
InstallSymlink(libpam.2.dylib ${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/lib/libpam.dylib)
install(TARGETS pam_deny pam_permit DESTINATION libexec/darling/usr/lib/pam)

install(DIRECTORY man DESTINATION libexec/darling/usr/share)
install(DIRECTORY pam.d DESTINATION libexec/darling/etc)
