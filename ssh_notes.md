# SSH Notes
OpenSSH SSH client (remote login program)

```bash
ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface] [-b bind_address] [-c cipher_spec]
    [-D [bind_address:]port] [-E log_file] [-e escape_char] [-F configfile] [-I pkcs11] [-i identity_file]
    [-J destination] [-L address] [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]
    [-Q query_option] [-R address] [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]] destination
    [command]
```

`ssh` (SSH client) is a program for logging into a remote machine and for executing commands on a remote
machine. It is intended to provide secure encrypted communication between two untrusted hosts over an
insecure network. X11 connections, arbitrary TCP ports and Unix-domain sockets can also be forwarded over
the secure channel.

`ssh` connects and logs into the specified destination, which may be specified as either *[user@]hostname*
or a URI of the form *ssh://[user@]hostname[:port]*. The user must prove his/her identity to the remote
machine using one of the several methods.

If a command is specified, it is executed on the remote host instead of a login shell.

The options are as follows:
* `-4` - forces `ssh` to use IPv4 addresses only.
* `-6` - forces `ssh` to use IPv6 addresses only.
* `-i _identity_file_` - selects a file from which the identity (private key) for public key authentication
  is read. The default is *~/.ssh/id_dsa*, *~/.ssh/id_ecdsa*, *~/.ssh/id_ed25519* and *~/.ssh/id_rsa*. Identity
  files may also be specified on a per-host basis in the configuration file. It is possible to have multiple
  `-i` options (and multiple identities specified in the configuration files). If no certificates have been
  explicitly specified by the *CertificateFile* directive, `ssh` will also try to load certificate information
  from the filename obtained by appending *-cert.pub* to identity filenames.