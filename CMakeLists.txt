project(openssh)

cmake_minimum_required(VERSION 2.4.0)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

include(darling_exe)

include_directories(BEFORE
	${CMAKE_CURRENT_SOURCE_DIR}/gensrc
	${CMAKE_CURRENT_SOURCE_DIR}/openssh
)

include_directories(
	${CMAKE_SOURCE_DIR}/src/libc/include/FreeBSD
	${CMAKE_SOURCE_DIR}/src/libc/include/NetBSD/
	${CMAKE_SOURCE_DIR}/src/external/openssl/src/include
	${CMAKE_SOURCE_DIR}/src/libinfo/include
	${CMAKE_SOURCE_DIR}/src/external/zlib
	${CMAKE_SOURCE_DIR}/src/sandbox
)

add_definitions(
	-D_FORTIFY_SOURCE=2
	-DSSHDIR="/etc"
	-D_PATH_SSH_PROGRAM="/usr/bin/ssh"
	-D_PATH_SSH_ASKPASS_DEFAULT="/usr/libexec/ssh-askpass"
	-D_PATH_SFTP_SERVER="/usr/libexec/sftp-server"
	-D_PATH_SSH_KEY_SIGN="/usr/libexec/ssh-keysign"
	-D_PATH_SSH_PKCS11_HELPER="/usr/libexec/ssh-pkcs11-helper"
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
	-Wno-pointer-sign
	-Wno-visibility
)

set(LIBS crypto098 z resolv-darwin)

set(LIBEXEC_PERMS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -nostdinc -fwrapv -fPIC")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -nostdinc++ -fwrapv -fPIC")

add_library(openssh OBJECT
	openssh/ssh_api.c
	openssh/ssherr.c
	openssh/sshbuf.c
	openssh/sshkey.c
	openssh/sshbuf-getput-basic.c
	openssh/sshbuf-misc.c
	openssh/sshbuf-getput-crypto.c
	openssh/krl.c
	openssh/bitmap.c

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

add_library(compat OBJECT
	openssh/openbsd-compat/base64.c
	openssh/openbsd-compat/basename.c
	openssh/openbsd-compat/bcrypt_pbkdf.c
	openssh/openbsd-compat/bindresvport.c
	openssh/openbsd-compat/blowfish.c
	openssh/openbsd-compat/daemon.c
	openssh/openbsd-compat/dirname.c
	openssh/openbsd-compat/fmt_scaled.c
	openssh/openbsd-compat/getcwd.c
	openssh/openbsd-compat/getgrouplist.c
	openssh/openbsd-compat/getopt_long.c
	openssh/openbsd-compat/getrrsetbyname.c
	openssh/openbsd-compat/glob.c
	openssh/openbsd-compat/inet_aton.c
	openssh/openbsd-compat/inet_ntoa.c
	openssh/openbsd-compat/inet_ntop.c
	openssh/openbsd-compat/mktemp.c
	openssh/openbsd-compat/pwcache.c
	openssh/openbsd-compat/readpassphrase.c
	openssh/openbsd-compat/reallocarray.c
	openssh/openbsd-compat/realpath.c
	openssh/openbsd-compat/rresvport.c
	openssh/openbsd-compat/setenv.c
	openssh/openbsd-compat/setproctitle.c
	openssh/openbsd-compat/sha1.c
	openssh/openbsd-compat/sha2.c
	openssh/openbsd-compat/rmd160.c
	openssh/openbsd-compat/md5.c
	openssh/openbsd-compat/sigact.c
	openssh/openbsd-compat/strlcat.c
	openssh/openbsd-compat/strlcpy.c
	openssh/openbsd-compat/strmode.c
	openssh/openbsd-compat/strnlen.c
	openssh/openbsd-compat/strptime.c
	openssh/openbsd-compat/strsep.c
	openssh/openbsd-compat/strtonum.c
	openssh/openbsd-compat/strtoll.c
	openssh/openbsd-compat/strtoul.c
	openssh/openbsd-compat/strtoull.c
	openssh/openbsd-compat/timingsafe_bcmp.c
	openssh/openbsd-compat/vis.c
	openssh/openbsd-compat/blowfish.c
	openssh/openbsd-compat/bcrypt_pbkdf.c
	openssh/openbsd-compat/explicit_bzero.c
	openssh/openbsd-compat/arc4random.c
	openssh/openbsd-compat/bsd-asprintf.c
	openssh/openbsd-compat/bsd-closefrom.c
	openssh/openbsd-compat/bsd-cray.c
	openssh/openbsd-compat/bsd-cygwin_util.c
	openssh/openbsd-compat/bsd-getpeereid.c
	openssh/openbsd-compat/getrrsetbyname-ldns.c
	openssh/openbsd-compat/bsd-err.c
	openssh/openbsd-compat/bsd-misc.c
	openssh/openbsd-compat/bsd-nextstep.c
	openssh/openbsd-compat/bsd-openpty.c
	openssh/openbsd-compat/bsd-poll.c
	openssh/openbsd-compat/bsd-setres_id.c
	openssh/openbsd-compat/bsd-snprintf.c
	openssh/openbsd-compat/bsd-statvfs.c
	openssh/openbsd-compat/bsd-waitpid.c
	openssh/openbsd-compat/fake-rfc2553.c
	openssh/openbsd-compat/openssl-compat.c
	openssh/openbsd-compat/xmmap.c
	openssh/openbsd-compat/xcrypt.c
	openssh/openbsd-compat/kludge-fd_set.c
	openssh/openbsd-compat/port-aix.c
	openssh/openbsd-compat/port-irix.c
	openssh/openbsd-compat/port-linux.c
	openssh/openbsd-compat/port-solaris.c
	openssh/openbsd-compat/port-tun.c
	openssh/openbsd-compat/port-uw.c
)

add_darling_executable(ssh
	openssh/ssh.c
	openssh/readconf.c
	openssh/clientloop.c
	openssh/sshtty.c
	openssh/sshconnect.c
	openssh/sshconnect1.c
	openssh/sshconnect2.c
	openssh/mux.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)

target_link_libraries(ssh ${LIBS})
install(TARGETS ssh DESTINATION libexec/darling/usr/bin)

add_library(sshd_umac OBJECT openssh/umac.c)
set_target_properties(sshd_umac PROPERTIES COMPILE_FLAGS "-DUMAC_OUTPUT_LEN=16 -Dumac_new=umac128_new \
            -Dumac_update=umac128_update -Dumac_final=umac128_final \
			-Dumac_delete=umac128_delete -Dumac_ctx=umac128_ctx")

add_darling_executable(sshd
	$<TARGET_OBJECTS:sshd_umac>
	openssh/sshd.c
	openssh/platform-pledge.c
	openssh/monitor_fdpass.c
	openssh/auth-rhosts.c
	openssh/auth-passwd.c
	openssh/auth-rsa.c
	openssh/auth-rh-rsa.c
	openssh/audit.c
	openssh/audit-bsm.c
	openssh/audit-linux.c
	openssh/platform.c
	openssh/sshpty.c
	openssh/sshlogin.c
	openssh/servconf.c
	openssh/serverloop.c
	openssh/auth.c
	openssh/auth1.c
	openssh/auth2.c
	openssh/auth-options.c
	openssh/session.c
	openssh/auth-chall.c
	openssh/auth2-chall.c
	openssh/groupaccess.c
	openssh/auth-skey.c
	openssh/auth-bsdauth.c
	openssh/auth2-hostbased.c
	openssh/auth2-kbdint.c
	openssh/auth2-none.c
	openssh/auth2-passwd.c
	openssh/auth2-pubkey.c
	openssh/monitor_mm.c
	openssh/monitor.c
	openssh/monitor_wrap.c
	openssh/auth-krb5.c
	openssh/auth2-gss.c
	openssh/gss-serv.c
	openssh/gss-serv-krb5.c
	openssh/loginrec.c
	openssh/auth-pam.c
	openssh/auth-shadow.c
	openssh/auth-sia.c
	openssh/md5crypt.c
	openssh/sftp-server.c
	openssh/sftp-common.c
	openssh/sandbox-null.c
	openssh/sandbox-rlimit.c
	openssh/sandbox-systrace.c
	openssh/sandbox-darwin.c
	openssh/sandbox-seccomp-filter.c
	openssh/sandbox-capsicum.c
	openssh/sandbox-pledge.c
	openssh/sandbox-solaris.c
	openssh/ttymodes.c
	openssh/sshkey.c
	openssh/opacket.c
	openssh/xmalloc.c
	$<TARGET_OBJECTS:compat>
	openssh/uidswap.c
	openssh/rsa.c
	openssh/hostfile.c
	openssh/kexgexs.c
	openssh/dh.c
	openssh/atomicio.c
	openssh/packet.c
	openssh/sshbuf-getput-basic.c
	openssh/sshbuf-getput-crypto.c
	openssh/sshbuf-misc.c
	openssh/sshbuf.c
	openssh/crc32.c
	openssh/dispatch.c
	openssh/ssh-ecdsa.c
	openssh/digest-openssl.c
	openssh/entropy.c
	openssh/misc.c
	openssh/mac.c
	openssh/log.c
	openssh/key.c
	openssh/authfile.c
	openssh/authfd.c
	openssh/kex.c
	openssh/canohost.c
	openssh/compat.c
	openssh/cipher.c
	openssh/channels.c
	openssh/bufaux.c
	openssh/addrmatch.c
	openssh/umac.c
	openssh/ssh-dss.c
	openssh/ssh-ed25519.c
	openssh/ssh-rsa.c
	openssh/cipher-ctr.c
	openssh/hmac.c
	openssh/match.c
	openssh/chacha.c
	openssh/kexc25519s.c
	openssh/nchan.c
	openssh/buffer.c
	openssh/cipher-chachapoly.c
	openssh/kexdhs.c
	$<TARGET_OBJECTS:openssh>
)

target_link_libraries(sshd ${LIBS} sandbox z ssl098 crypto098)

install(TARGETS sshd DESTINATION libexec/darling/usr/sbin)

add_darling_executable(scp
	openssh/scp.c
	openssh/progressmeter.c
	openssh/bufaux.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(scp ${LIBS})
install(TARGETS scp DESTINATION libexec/darling/usr/bin)

add_darling_executable(ssh-add
	openssh/ssh-add.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(ssh-add ${LIBS})
install(TARGETS ssh-add DESTINATION libexec/darling/usr/bin)

add_darling_executable(ssh-agent
	openssh/ssh-agent.c
	openssh/ssh-pkcs11-client.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(ssh-agent ${LIBS})
install(TARGETS ssh-agent DESTINATION libexec/darling/usr/bin)

add_darling_executable(ssh-keygen
	openssh/ssh-keygen.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(ssh-keygen ${LIBS})
install(TARGETS ssh-keygen DESTINATION libexec/darling/usr/bin)

add_darling_executable(ssh-keysign
	openssh/ssh-keysign.c
	openssh/readconf.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(ssh-keysign ${LIBS})
install(TARGETS ssh-keysign
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec)

add_darling_executable(ssh-pkcs11-helper
	openssh/ssh-pkcs11-helper.c
	openssh/ssh-pkcs11.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(ssh-pkcs11-helper ${LIBS})
install(TARGETS ssh-pkcs11-helper
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec)

add_darling_executable(ssh-keyscan
	openssh/ssh-keyscan.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(ssh-keyscan ${LIBS})
install(TARGETS ssh-keyscan DESTINATION libexec/darling/usr/bin)

add_darling_executable(sftp-server
	openssh/sftp-server.c
	openssh/sftp-common.c
	openssh/sftp-server-main.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(sftp-server ${LIBS})
install(TARGETS sftp-server
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec)

add_darling_executable(sftp
	openssh/progressmeter.c
	openssh/sftp.c
	openssh/sftp-client.c
	openssh/sftp-common.c
	openssh/sftp-glob.c
	$<TARGET_OBJECTS:compat>
	$<TARGET_OBJECTS:openssh>
)
target_link_libraries(sftp ${LIBS})
install(TARGETS sftp DESTINATION libexec/darling/usr/bin)

# Manpages and shell scripts
install(DIRECTORY usr DESTINATION libexec/darling USE_SOURCE_PERMISSIONS)
