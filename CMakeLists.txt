project(openssh)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

include(darling_exe)

include_directories(BEFORE
	${CMAKE_CURRENT_SOURCE_DIR}/openssh
)

add_compile_definitions(
	__APPLE_LAUNCHD__
	__APPLE_MEMBERSHIP__
	__APPLE_KEYCHAIN__
	__APPLE_SANDBOX_NAMED_EXTERNAL__
	__APPLE_TMPDIR__
	__APPLE_DISPLAY_VAR__
	#__APPLE_NW_CONNECTION__ # no libnetwork (yet)

	# macOS doesn't include ssh-askpass anyways
	# we used to define this, but Apple leaves this unspecified, so we should do the same
	#_PATH_SSH_ASKPASS_DEFAULT="/usr/libexec/ssh-askpass"
)

add_link_options(
	-Wl,-pie
)

set(BASE_LIBS
	Kerberos
	OpenDirectory
	bsm.0
	crypto44
	pam.2
	z
)

set(SSH_LIBS
	openbsd_compat
	libssh
)

set(COMMON_LIBS
	resolv-darwin
	${SSH_LIBS}
	${BASE_LIBS}
)

# Hide warnings
add_compile_options(
	-nostdinc
	-fwrapv
	-Wno-macro-redefined
	-Wno-deprecated-declarations
	-Wno-implicit-function-declaration
	-Wno-incompatible-pointer-types
	-Wno-pointer-sign
	-Wno-visibility
)

set(LIBEXEC_PERMS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

#
# libssh
#

add_darling_static_library(libssh FAT SOURCES
	openssh/addrmatch.c
	openssh/atomicio.c
	openssh/authfd.c
	openssh/authfile.c
	openssh/bitmap.c
	openssh/canohost.c
	openssh/chacha.c
	openssh/channels.c
	openssh/cipher-aes.c
	openssh/cipher-chachapoly.c
	openssh/cipher-ctr.c
	openssh/cipher.c
	openssh/cleanup.c
	openssh/compat.c
	openssh/crc32.c
	openssh/dh.c
	openssh/digest-openssl.c
	openssh/dispatch.c
	openssh/dns.c
	openssh/ed25519.c
	openssh/entropy.c
	openssh/fatal.c
	openssh/fe25519.c
	openssh/ge25519.c
	openssh/gss-genr.c
	openssh/hash.c
	openssh/hmac.c
	openssh/hostfile.c
	openssh/kex.c
	openssh/kexc25519.c
	openssh/kexc25519c.c
	openssh/kexc25519s.c
	openssh/kexdh.c
	openssh/kexdhc.c
	openssh/kexdhs.c
	openssh/kexecdh.c
	openssh/kexecdhc.c
	openssh/kexecdhs.c
	openssh/kexgex.c
	openssh/kexgexc.c
	openssh/kexgexs.c
	openssh/krl.c
	openssh/log.c
	openssh/mac.c
	openssh/match.c
	openssh/misc.c
	openssh/moduli.c
	openssh/monitor_fdpass.c
	openssh/msg.c
	openssh/nchan.c
	openssh/opacket.c
	openssh/packet.c
	openssh/poly1305.c
	openssh/readpass.c
	openssh/rijndael.c
	openssh/sc25519.c
	openssh/smult_curve25519_ref.c
	openssh/ssh_api.c
	openssh/ssh-dss.c
	openssh/ssh-ecdsa.c
	openssh/ssh-ed25519.c
	openssh/ssh-pkcs11.c
	openssh/ssh-rsa.c
	openssh/ssh-xmss.c
	openssh/sshbuf-getput-basic.c
	openssh/sshbuf-getput-crypto.c
	openssh/sshbuf-misc.c
	openssh/sshbuf.c
	openssh/ssherr.c
	openssh/sshkey-xmss.c
	openssh/sshkey.c
	openssh/ttymodes.c
	openssh/uidswap.c
	openssh/umac.c
	openssh/umac128.c
	openssh/uuencode.c
	openssh/verify.c
	openssh/xmalloc.c
	openssh/xmss_commons.c
	openssh/xmss_fast.c
	openssh/xmss_hash_address.c
	openssh/xmss_hash.c
	openssh/xmss_wots.c
)
set_target_properties(libssh PROPERTIES
	OUTPUT_NAME "ssh"
)

#
# libopenbsd_compat
#

add_darling_static_library(openbsd_compat FAT SOURCES
	openssh/authfd.c
	openssh/authfile.c
	openssh/canohost.c
	openssh/channels.c
	openssh/cipher-aes.c
	openssh/cipher-ctr.c
	openssh/cipher.c
	openssh/compat.c
	openssh/crc32.c
	openssh/fatal.c
	openssh/hostfile.c
	openssh/match.c
	openssh/moduli.c
	openssh/openbsd-compat/arc4random.c
	openssh/openbsd-compat/base64.c
	openssh/openbsd-compat/basename.c
	openssh/openbsd-compat/bcrypt_pbkdf.c
	openssh/openbsd-compat/bindresvport.c
	openssh/openbsd-compat/blowfish.c
	openssh/openbsd-compat/bsd-asprintf.c
	openssh/openbsd-compat/bsd-closefrom.c
	openssh/openbsd-compat/bsd-cygwin_util.c
	openssh/openbsd-compat/bsd-getpeereid.c
	openssh/openbsd-compat/bsd-misc.c
	openssh/openbsd-compat/bsd-nextstep.c
	openssh/openbsd-compat/bsd-openpty.c
	openssh/openbsd-compat/bsd-poll.c
	openssh/openbsd-compat/bsd-setres_id.c
	openssh/openbsd-compat/bsd-signal.c
	openssh/openbsd-compat/bsd-snprintf.c
	openssh/openbsd-compat/bsd-statvfs.c
	openssh/openbsd-compat/bsd-waitpid.c
	openssh/openbsd-compat/daemon.c
	openssh/openbsd-compat/dirname.c
	openssh/openbsd-compat/explicit_bzero.c
	openssh/openbsd-compat/fake-rfc2553.c
	openssh/openbsd-compat/fmt_scaled.c
	openssh/openbsd-compat/getcwd.c
	openssh/openbsd-compat/getgrouplist.c
	openssh/openbsd-compat/getopt_long.c
	openssh/openbsd-compat/getrrsetbyname-ldns.c
	openssh/openbsd-compat/getrrsetbyname.c
	openssh/openbsd-compat/glob.c
	openssh/openbsd-compat/inet_aton.c
	openssh/openbsd-compat/inet_ntoa.c
	openssh/openbsd-compat/inet_ntop.c
	openssh/openbsd-compat/kludge-fd_set.c
	openssh/openbsd-compat/libressl-api-compat.c
	openssh/openbsd-compat/md5.c
	openssh/openbsd-compat/mktemp.c
	openssh/openbsd-compat/openssl-compat.c
	openssh/openbsd-compat/port-aix.c
	openssh/openbsd-compat/port-irix.c
	openssh/openbsd-compat/port-linux.c
	openssh/openbsd-compat/port-net.c
	openssh/openbsd-compat/port-solaris.c
	openssh/openbsd-compat/port-uw.c
	openssh/openbsd-compat/readpassphrase.c
	openssh/openbsd-compat/reallocarray.c
	openssh/openbsd-compat/realpath.c
	openssh/openbsd-compat/rmd160.c
	openssh/openbsd-compat/rresvport.c
	openssh/openbsd-compat/setenv.c
	openssh/openbsd-compat/setproctitle.c
	openssh/openbsd-compat/sha1.c
	openssh/openbsd-compat/sha2.c
	openssh/openbsd-compat/sigact.c
	openssh/openbsd-compat/strlcat.c
	openssh/openbsd-compat/strlcpy.c
	openssh/openbsd-compat/strmode.c
	openssh/openbsd-compat/strnlen.c
	openssh/openbsd-compat/strptime.c
	openssh/openbsd-compat/strsep.c
	openssh/openbsd-compat/strtoll.c
	openssh/openbsd-compat/strtonum.c
	openssh/openbsd-compat/strtoul.c
	openssh/openbsd-compat/strtoull.c
	openssh/openbsd-compat/timingsafe_bcmp.c
	openssh/openbsd-compat/vis.c
	openssh/openbsd-compat/xcrypt.c

	# not listed in Xcode build files, but necessary
	openssh/openbsd-compat/freezero.c
	openssh/openbsd-compat/recallocarray.c
)

#
# ssh (exectuable)
#

add_darling_executable(ssh
	openssh/clientloop.c
	openssh/keychain.m
	openssh/mux.c
	openssh/platform-misc.c
	openssh/platform-pledge.c
	openssh/readconf.c
	openssh/ssh.c
	openssh/sshconnect.c
	openssh/sshconnect2.c
	openssh/sshtty.c
	openssh/utf8.c
)

target_link_libraries(ssh
	#libnetwork # missing
	Security
	objc
	${COMMON_LIBS}

	# not in Xcode build files, but necessary
	Foundation
)

#
# sshd
#

add_darling_executable(sshd
	openssh/audit-bsm.c
	openssh/audit.c
	openssh/auth-bsdauth.c
	openssh/auth-krb5.c
	openssh/auth-options.c
	openssh/auth-pam.c
	openssh/auth-passwd.c
	openssh/auth-rhosts.c
	openssh/auth-shadow.c
	openssh/auth-sia.c
	openssh/auth-skey.c
	openssh/auth.c
	openssh/auth2-chall.c
	openssh/auth2-gss.c
	openssh/auth2-hostbased.c
	openssh/auth2-kbdint.c
	openssh/auth2-none.c
	openssh/auth2-passwd.c
	openssh/auth2-pubkey.c
	openssh/auth2.c
	openssh/groupaccess.c
	openssh/gss-serv-krb5.c
	openssh/gss-serv.c
	openssh/kexc25519s.c
	openssh/kexdhs.c
	openssh/kexecdhs.c
	openssh/kexgexs.c
	openssh/loginrec.c
	openssh/md5crypt.c
	openssh/monitor_wrap.c
	openssh/monitor.c
	openssh/platform-misc.c
	openssh/platform-pledge.c
	openssh/platform-tracing.c
	openssh/platform.c
	openssh/progressmeter.c
	openssh/sandbox-darwin.c
	openssh/sandbox-null.c
	openssh/servconf.c
	openssh/serverloop.c
	openssh/session.c
	openssh/sftp-common.c
	openssh/sftp-server.c
	openssh/sshd.c
	openssh/sshlogin.c
	openssh/sshpty.c
)

target_link_libraries(sshd
	${COMMON_LIBS}
)

#
# scp
#

add_darling_executable(scp
	openssh/platform-misc.c
	openssh/progressmeter.c
	openssh/scp.c
	openssh/utf8.c
)
target_link_libraries(scp
	${COMMON_LIBS}
)

#
# ssh-add
#

add_darling_executable(ssh-add
	openssh/keychain.m
	openssh/platform-misc.c
	openssh/ssh-add.c
)
target_link_libraries(ssh-add
	Security
	objc
	${COMMON_LIBS}

	# again: not in Xcode build files, but necessary
	Foundation
)

#
# ssh-agent
#

add_darling_executable(ssh-agent
	openssh/platform-misc.c
	openssh/platform-pledge.c
	openssh/platform-tracing.c
	openssh/ssh-agent.c
	openssh/ssh-pkcs11-client.c
)
target_link_libraries(ssh-agent
	${COMMON_LIBS}
)

#
# ssh-keygen
#

add_darling_executable(ssh-keygen
	openssh/platform-misc.c
	openssh/ssh-keygen.c
	openssh/utf8.c
)
target_link_libraries(ssh-keygen
	${COMMON_LIBS}
)

#
# ssh-keysign
#

add_darling_executable(ssh-keysign
	openssh/platform-misc.c
	openssh/readconf.c
	openssh/ssh-keysign.c
)
target_link_libraries(ssh-keysign
	${COMMON_LIBS}
)

#
# ssh-pkcs11-helper
#

add_darling_executable(ssh-pkcs11-helper
	openssh/platform-misc.c
	openssh/ssh-pkcs11-helper.c
	openssh/ssh-pkcs11.c
)
target_link_libraries(ssh-pkcs11-helper
	${SSH_LIBS}
	${BASE_LIBS}
)

#
# ssh-keyscan
#

add_darling_executable(ssh-keyscan
	openssh/platform-misc.c
	openssh/ssh-keyscan.c
)
target_link_libraries(ssh-keyscan
	${COMMON_LIBS}
)

#
# sftp-server
#

add_darling_executable(sftp-server
	openssh/platform-misc.c
	openssh/platform-pledge.c
	openssh/platform-tracing.c
	openssh/sftp-common.c
	openssh/sftp-server-main.c
	openssh/sftp-server.c
)
target_link_libraries(sftp-server
	${COMMON_LIBS}
)

#
# sftp
#

add_darling_executable(sftp
	openssh/platform-misc.c
	openssh/progressmeter.c
	openssh/sftp-client.c
	openssh/sftp-common.c
	openssh/sftp-glob.c
	openssh/sftp.c
	openssh/utf8.c
)
target_link_libraries(sftp
	${COMMON_LIBS}
)

#
# sshd-keygen-wrapper
#

add_darling_executable(sshd-keygen-wrapper
	sshd-keygen-wrapper.c
)
target_link_libraries(sshd-keygen-wrapper
	${BASE_LIBS}
)

#
# install everything
#

install(TARGETS ssh DESTINATION libexec/darling/usr/bin)
InstallSymlink("ssh" "${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/bin/slogin")

install(TARGETS sshd DESTINATION libexec/darling/usr/sbin)
install(FILES com.openssh.sshd.plist DESTINATION libexec/darling/System/Library/LaunchDaemons RENAME ssh.plist)
install(FILES pam.d/sshd DESTINATION libexec/darling/private/etc/pam.d)

install(TARGETS scp DESTINATION libexec/darling/usr/bin)

install(TARGETS ssh-add DESTINATION libexec/darling/usr/bin)

install(TARGETS ssh-agent DESTINATION libexec/darling/usr/bin)
install(FILES com.openssh.ssh-agent.plist DESTINATION libexec/darling/System/Library/LaunchAgents)

install(TARGETS ssh-keygen DESTINATION libexec/darling/usr/bin)

install(
	TARGETS ssh-keysign
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec
)

install(
	TARGETS ssh-pkcs11-helper
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec
)

install(TARGETS ssh-keyscan DESTINATION libexec/darling/usr/bin)

install(
	TARGETS sftp-server
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec
)

install(TARGETS sftp DESTINATION libexec/darling/usr/bin)

install(
	TARGETS sshd-keygen-wrapper
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/libexec
)

install(
	FILES openssh/contrib/ssh-copy-id
	PERMISSIONS ${LIBEXEC_PERMS}
	DESTINATION libexec/darling/usr/bin
)
