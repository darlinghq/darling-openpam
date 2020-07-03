project(openpam)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

add_compile_options(
	-nostdinc
	-fblocks
)

add_compile_definitions(
	LIB_MAJ=2
	"OPENPAM_MODULES_DIR=\"/usr/lib/pam/\;/usr/local/lib/pam/\""
	_GNU_SOURCE
)

include_directories(BEFORE
	${CMAKE_CURRENT_SOURCE_DIR}/openpam
	${CMAKE_CURRENT_SOURCE_DIR}/openpam/include
)

set(DYLIB_INSTALL_NAME "/usr/lib/libpam.2.dylib")
add_darling_library(pam.2 SHARED
	openpam/lib/openpam_borrow_cred.c
	openpam/lib/openpam_configure.c
	openpam/lib/openpam_dispatch.c
	openpam/lib/openpam_dynamic.c
	openpam/lib/openpam_findenv.c
	openpam/lib/openpam_free_data.c
	openpam/lib/openpam_free_envlist.c
	openpam/lib/openpam_get_option.c
	openpam/lib/openpam_load.c
	openpam/lib/openpam_log.c
	openpam/lib/openpam_nullconv.c
	openpam/lib/openpam_readline.c
	openpam/lib/openpam_restore_cred.c
	openpam/lib/openpam_set_option.c
	openpam/lib/openpam_static.c
	openpam/lib/openpam_ttyconv.c
	openpam/lib/pam_acct_mgmt.c
	openpam/lib/pam_authenticate.c
	openpam/lib/pam_chauthtok.c
	openpam/lib/pam_close_session.c
	openpam/lib/pam_end.c
	openpam/lib/pam_error.c
	openpam/lib/pam_get_authtok.c
	openpam/lib/pam_get_data.c
	openpam/lib/pam_get_item.c
	openpam/lib/pam_get_user.c
	openpam/lib/pam_getenv.c
	openpam/lib/pam_getenvlist.c
	openpam/lib/pam_info.c
	openpam/lib/pam_open_session.c
	openpam/lib/pam_prompt.c
	openpam/lib/pam_putenv.c
	openpam/lib/pam_set_data.c
	openpam/lib/pam_set_item.c
	openpam/lib/pam_setcred.c
	openpam/lib/pam_setenv.c
	openpam/lib/pam_start.c
	openpam/lib/pam_strerror.c
	openpam/lib/pam_unsetenv.c
	openpam/lib/pam_verror.c
	openpam/lib/pam_vinfo.c
	openpam/lib/pam_vprompt.c
)
make_fat(pam.2)

set(DYLIB_INSTALL_NAME "/usr/lib/libpam.1.dylib")
add_darling_library(pam.1 SHARED
	compat/pam_shim.c
)
make_fat(pam.1)

set(DYLIB_INSTALL_NAME "/usr/lib/pam/pam_deny.so.2")
add_darling_library(pam_deny SHARED
	openpam/modules/pam_deny/pam_deny.c
)
make_fat(pam_deny)
set_target_properties(pam_deny PROPERTIES
	SUFFIX ".so.2"
	PREFIX ""
)

set(DYLIB_INSTALL_NAME "/usr/lib/pam/pam_permit.so.2")
add_darling_library(pam_permit SHARED
	openpam/modules/pam_permit/pam_permit.c
)
make_fat(pam_permit)
set_target_properties(pam_permit PROPERTIES
	SUFFIX ".so.2"
	PREFIX ""
)


target_link_libraries(pam.2 system)
target_link_libraries(pam.1 system)
target_link_libraries(pam_deny system)
target_link_libraries(pam_permit system)

install(TARGETS pam.1 pam.2 DESTINATION libexec/darling/usr/lib)
InstallSymlink(libpam.2.dylib ${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/lib/libpam.dylib)
install(TARGETS pam_deny pam_permit DESTINATION libexec/darling/usr/lib/pam)

install(DIRECTORY openpam/doc/man DESTINATION libexec/darling/usr/share)
install(DIRECTORY pam.d DESTINATION libexec/darling/etc)

add_subdirectory(pam_modules)
