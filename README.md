# pkg-cruft

A small Ruby script for helping deal with cruft on pkgng (FreeBSD etc) systems.

## Requirements

* pkgng (tested on FreeBSD 11.2)
* procstat (for checkrestart)
* Ruby (tested on 2.4.4 and 2.5.1); no gems used or needed.

## Synopsis

```
% pkg-cruft [ help | defunct | unpackaged | libcheck | checkrestart ]
```

## Subcommands:

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

### checkrestart

Check for running processes that may require restarting due to replaced
executables or libraries.  Named for the similar Linux command.

Run as root to check all running processes.  False-positives are possible.

```
# pkg-cruft checkrestart
/home/freaky/.rbenv/versions/2.4.4/bin/ruby (unpackaged) running as 61068 (ruby)
/usr/local/bin/mosh-client (mosh-1.3.2_4) running as 59229 (mosh-client)
/usr/local/bin/perl5 (perl5-5.26.2) running as 21795 (perl)
/usr/local/bin/postgres (postgresql96-server-9.6.9_1) running as 36363 (postgres)
```

## Configuration

Configuration is via environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `PREFIX`   | `/usr/local` | Installation prefix |
| `CONCURRENCY` | `16` | Workers to use for `libcheck` |
| `IGNORE_UNPACKAGED` | `www/*:poudriere/*:varnish/*` | :-separated patterns to ignore in `unpackaged` |
| `IGNORE_LLD` | `go/src/*/testdata/*` | :-separated patterns to ignore in `libcheck` |
