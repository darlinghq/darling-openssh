project(openssh)

cmake_minimum_required(VERSION 2.4.0)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

include(darling_exe)

include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/gensrc)
include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/openssh)

include_directories(
	${DARLING_TOP_DIRECTORY}/src/libc/include/FreeBSD
	${DARLING_TOP_DIRECTORY}/src/libc/include/NetBSD/
	${DARLING_TOP_DIRECTORY}/src/external/openssl/src/include
	${DARLING_TOP_DIRECTORY}/src/libinfo/include
	${DARLING_TOP_DIRECTORY}/src/external/zlib
)

add_definitions(
	-D_FORTIFY_SOURCE=2
	-DSSHDIR="/usr/bin/etc"
	-D_PATH_SSH_PROGRAM="/usr/bin/bin/ssh"
	-D_PATH_SSH_ASKPASS_DEFAULT="/usr/bin/libexec/ssh-askpass"
	-D_PATH_SFTP_SERVER="/usr/bin/libexec/sftp-server"
	-D_PATH_SSH_KEY_SIGN="/usr/bin/libexec/ssh-keysign"
	-D_PATH_SSH_PKCS11_HELPER="/usr/bin/libexec/ssh-pkcs11-helper"
	-D_PATH_SSH_PIDDIR="/var/run"
	-D_PATH_PRIVSEP_CHROOT_DIR="/var/empty"
	-DHAVE_CONFIG_H
)

# Hide warnings
add_definitions(
	-Wno-macro-redefined
	-Wno-deprecated-declarations
	-Wno-implicit-function-declaration
	-Wno-incompatible-pointer-types
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -nostdinc -fwrapv -fPIC -ggdb")
set(CMAKE_SHARED_LINKER_FLAGS "-nodefaultlibs -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")
set(CMAKE_EXE_LINKER_FLAGS "-nodefaultlibs -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/darling")
SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

add_library(openssh STATIC
	openssh/ssh_api.c
	openssh/ssherr.c
	openssh/sshbuf.c
	openssh/sshkey.c
	openssh/sshbuf-getput-basic.c
	openssh/sshbuf-misc.c
	openssh/sshbuf-getput-crypto.c
	openssh/krl.c
	openssh/bitmap.c
)

add_library(ssh STATIC
	openssh/authfd.c
	openssh/authfile.c
	openssh/bufaux.c
	openssh/bufbn.c
	openssh/bufec.c
	openssh/buffer.c
	openssh/canohost.c
	openssh/channels.c
	openssh/cipher.c
	openssh/cipher-aes.c
	openssh/cipher-aesctr.c
	openssh/cipher-bf1.c
	openssh/cipher-ctr.c
	openssh/cipher-3des1.c
	openssh/cleanup.c
	openssh/compat.c
	openssh/crc32.c
	openssh/deattack.c
	openssh/fatal.c
	openssh/hostfile.c
	openssh/log.c
	openssh/match.c
	openssh/md-sha256.c
	openssh/moduli.c
	openssh/nchan.c
	openssh/packet.c
	openssh/opacket.c
	openssh/readpass.c
	openssh/rsa.c
	openssh/ttymodes.c
	openssh/xmalloc.c
	openssh/addrmatch.c
	openssh/atomicio.c
	openssh/key.c
	openssh/dispatch.c
	openssh/mac.c
	openssh/uidswap.c
	openssh/uuencode.c
	openssh/misc.c
	openssh/utf8.c
	openssh/monitor_fdpass.c
	openssh/rijndael.c
	openssh/ssh-dss.c
	openssh/ssh-ecdsa.c
	openssh/ssh-rsa.c
	openssh/dh.c
	openssh/msg.c
	openssh/progressmeter.c
	openssh/dns.c
	openssh/entropy.c
	openssh/gss-genr.c
	openssh/umac.c
	gensrc/umac128.c
	openssh/ssh-pkcs11.c
	openssh/smult_curve25519_ref.c
	openssh/poly1305.c
	openssh/chacha.c
	openssh/cipher-chachapoly.c
	openssh/ssh-ed25519.c
	openssh/digest-openssl.c
	openssh/digest-libc.c
	openssh/hmac.c
	openssh/sc25519.c
	openssh/ge25519.c
	openssh/fe25519.c
	openssh/ed25519.c
	openssh/verify.c
	openssh/hash.c
	openssh/blocks.c
	openssh/kex.c
	openssh/kexdh.c
	openssh/kexgex.c
	openssh/kexecdh.c
	openssh/kexc25519.c
	openssh/kexdhc.c
	openssh/kexgexc.c
	openssh/kexecdhc.c
	openssh/kexc25519c.c
	openssh/kexdhs.c
	openssh/kexgexs.c
	openssh/kexecdhs.c
	openssh/kexc25519s.c
	openssh/platform-pledge.c
	openssh/platform-tracing.c
)

# Manpages and shell scripts
install(DIRECTORY usr DESTINATION libexec/darling)
