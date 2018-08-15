# pkg-cruft

A small Ruby script for helping deal with cruft on pkgng (FreeBSD etc) systems.

## Requirements

* pkgng (tested on FreeBSD 11.2)
* procstat (for checkrestart)
* Ruby (tested on 2.4.4 and 2.5.1); no gems used or needed.

## Synopsis

```
% pkg-cruft [ help | defunct | files | dirs | libcheck | checkrestart ]
```

## Subcommands:

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
[MISSING EXECUTABLE] (tmux-2.7)? running as 17319 (tmux)
[MISSING EXECUTABLE] (zsh-5.5.1)? running as 20115 (zsh)
[MISSING EXECUTABLE] (weechat-2.2)? running as 36747 (weechat)
/usr/local/bin/mosh-server (mosh-1.3.2_4) running as 53815 (mosh-server)
```

### files

List files in PREFIX that are not provided by any installed package, ignoring
files specified in env `IGNORE_UNPACKAGED`.

```
# pkg-cruft files
/usr/local/apache-tomcat-6.0/conf/Catalina/localhost/host-manager.xml
/usr/local/apache-tomcat-6.0/conf/Catalina/localhost/manager.xml
...
```

### dirs

List directories in PREFIX that do not contain any packaged files, ignoring
any specified in `IGNORE_UNPACKAGED`.

```
# pkg-cruft dirs
/usr/local/openjdk8/jre/lib/applet
/usr/local/share/texmf/tex/latex
...
```

### defunct

List local packages that are not available from remote repositories.

```
% pkg-cruft defunct
bsdpan-Mail-SpamAssassin-CompiledRegexps-body_0
```

## Configuration

Configuration is via environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `PREFIX`   | `/usr/local` | Installation prefix |
| `CONCURRENCY` | `16` | Workers to use for `libcheck` |
| `IGNORE_UNPACKAGED` | `www/*:poudriere/*:varnish/*` | :-separated patterns to ignore in `unpackaged` |
| `IGNORE_LLD` | `go/src/*/testdata/*` | :-separated patterns to ignore in `libcheck` |
