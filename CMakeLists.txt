project(openpam)

include(InstallSymlink)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/darling")
SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -msse -msse2 -msse3 -nostdinc -fblocks -fPIC")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse -msse2 -msse3 -nostdinc -nostdinc++ -fblocks -fPIC")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

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

add_library(pam.2 SHARED
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

add_library(pam.1 SHARED
	compat/pam_shim.c
)

add_library(pam_deny SHARED
	modules/pam_deny/pam_deny.c
)

add_library(pam_permit SHARED
	modules/pam_permit/pam_permit.c
)

target_link_libraries(pam.2 system)
target_link_libraries(pam.1 system)
target_link_libraries(pam_deny system)
target_link_libraries(pam_permit system)

install(TARGETS pam.1 pam.2 DESTINATION ${CMAKE_INSTALL_LIBDIR}/darling)
InstallSymlink(pam.so ${CMAKE_INSTALL_LIBDIR}/darling/libpam.1.so)
install(TARGETS pam_deny pam_permit DESTINATION ${CMAKE_INSTALL_LIBDIR}/darling/pam)

install(DIRECTORY man DESTINATION libexec/darling/usr/share)
install(DIRECTORY pam.d DESTINATION libexec/darling/etc)
