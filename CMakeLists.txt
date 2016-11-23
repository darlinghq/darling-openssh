project(openssh)

cmake_minimum_required(VERSION 2.4.0)

include(darling_exe)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/openssh)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/FreeBSD)

add_definitions(
	-D_FORTIFY_SOURCE=2
	-DSSHDIR=\"/usr/bin/etc\"
	-D_PATH_SSH_PROGRAM=\"/usr/bin/bin/ssh\"
	-D_PATH_SSH_ASKPASS_DEFAULT=\"/usr/bin/libexec/ssh-askpass\"
	-D_PATH_SFTP_SERVER=\"/usr/bin/libexec/sftp-server\"
	-D_PATH_SSH_KEY_SIGN=\"/usr/bin/libexec/ssh-keysign\"
	-D_PATH_SSH_PKCS11_HELPER=\"/usr/bin/libexec/ssh-pkcs11-helper\"
	-D_PATH_SSH_PIDDIR=\"/var/run\"
	-D_PATH_PRIVSEP_CHROOT_DIR=\"/var/empty\"
	-DHAVE_CONFIG_H
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -nostdinc -fwrapv -fPIC -ggdb")
set(CMAKE_SHARED_LINKER_FLAGS "-nodefaultlibs -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")
set(CMAKE_EXE_LINKER_FLAGS "-nodefaultlibs -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/darling")
SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)


#add_darling_executable(ssh ${ssh_sources})

#install(TARGETS ssh DESTINATION libexec/darling/usr/bin)

# Manpages and shell scripts
install(FILES usr DESTINATION libexec/darling)
