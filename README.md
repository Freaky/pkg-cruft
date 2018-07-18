# pkg-cruft

A small Ruby script for helping deal with cruft on pkgng (FreeBSD etc) systems.

## Synopsis

### defunct

List local packages that are not available from remote repositories.

```
% pkg-cruft defunct
bsdpan-Mail-SpamAssassin-CompiledRegexps-body_0
```

### unpackaged

List files in PREFIX that are not provided by any installed package, ignoring
files specified in env `IGNORE_UNPACKAGED`.

```
% pkg-cruft unpackaged
/usr/local/apache-tomcat-6.0/conf/Catalina/localhost/host-manager.xml
/usr/local/apache-tomcat-6.0/conf/Catalina/localhost/manager.xml
...
```

### libcheck

Check for packaged files that link against unpackaged, compat, or obsolete
libraries, ignoring files specified in env `IGNORE_LLD`.

```
% pkg-cruft libcheck
compat10x-amd64-10.3.1003000.20170608: /usr/local/lib/compat/pam_ssh.so.5 missing library libssh.so.5
compat10x-amd64-10.3.1003000.20170608: /usr/local/lib32/compat/pam_ssh.so.5 missing library libssh.so.5
```
