# Java Keytool
*Java Keytool* is a key and certificate management tool that is used
to manipulate Java Keystores, and is included with Java. A *Java
Keystore* is a container for authorization certificates or public
key certificates, and is often used by Java-based applications for
encryption, authentication, and serving over HTTPS. Its entries are
protected by a keystore password. A keystore entry is identified by
an *alias* and it consists of keys and certificates that form a
trust chain.

## Creating and Importing Keystore Entries

### Generate Keys in New/Existing Keystore
Use this method if you want to use HTTP (HTTP over TLS) to secure
your Java application. This will create a new key pair in a new or
existing Java Keystore, which can be used to create a CSR to obtain
an SSL certificate from a CA.

This command generates a 2048-bit RSA key pair, under the specified
alias (`domain`), in the specified keystore file (`keystore.jks`):

```bash
keytool -genkeypair -alias domain -keyalg RSA -keystore keystore.jks
```

If the specified keystore does not already exist, it will be created
after the requested information is supplied. This will prompt for
the keystore password (new or existing), followed by a Distinguished
Name prompt (for the private key), then the desired private key
password.

### Generate CSR for Existing Private Key
Use this method if you want to generate a CSR (Certificate Signing 
Request) that you can send to a CA to request the issuance of a CA-
signed SSL certificate. It requires that the keystore and alias
already exist; you can use the previous command to ensure this.

This command creates a CSR (`domain.csr`) signed by the private key
identified by the alias (`domain`) in the (`keystore.jks`) keystore:

```bash
keytool -certreq -alias domain -file domain.csr \ 
-keystore keystore.jks
```

After entering the keystore's password, the CSR will be generated.

### Import Signed/Root/Intermediate Certificate
Use this method if you want to import a signed certificate, e.g.
a certificate signed by a CA, into your keystore, it must match
the private key that exists in the specified alias. You may also
use this same command to import *root* or *intermediate*
certificates that your CA may require to complete a chain of trust.
Simply specify a unique alias, such as `root` instead of `domain`,
and the certificate that you want to import.

This command imports the certificate (`domain.crt`) into the
keystore (`keystore.jks`), under the specified alias (`domain`).
If you are importing a signed certificate, it must correspond to the
private key in the specified alias:

```bash
keytool -importcert -trustcacerts -file domain.crt -alias domain \
-keystore keystore.jks
```

You will be prompted for the keystore password, then for a
confirmation of the import action.

**Note**: You may also use the command to import a CA's certificates
into your Java truststore, which is typically located in 
`$JAVA_HOME/jre/lib/security/cacerts` assuming `$JAVA_HOME` is where
your JRE or JDK is installed.

### Generate Self-Signed Certificate in New/Existing Keystore
Use this command if you want to generate a self-signed certificate
for your Java applications. This is actually the same command that
is used to create a new key pair, but with the validity lifetime
specified in days.

This command generates a 2048-bit RSA key pair, valid for 365 days,
under the specified alias (`domain`), in the specified keystore file
(`keystore.jks`):

```bash
keytool -genkey -alias domain -keyalg RSA -validity 365 \
-keystore keystore.jks
```

If the specified keystore does not already exist, it will be
created after the requested information is supplied. This will
prompt for the keystore password (new or existing), followed by a
Distinguished Name prompt (for the private key), then the desired
private key password.

## View Keystore Entries

### List Keystore Certificate Fingerprints
This command lists the SHA fingerprints of all of the certificates
in the keystore (`keystore.jks`), under their respective aliases:

```bash
keytool -list -keystore keystore.jks
```

You will be prompted for the keystore's password. You may also
restrict the output to a specific alias by using the
`-alias domain` option, where "domain" is the alias name.

### List Verbose Keystore Contents
This command lists verbose information about the entries a
keystore (`keystore.jks`) contains, including certificate chain
length, fingerprint of certificates in the chain, distinguished
names, serial number, and creation/expiration date, under their
respective aliases:

```bash
keytool -list -v -keystore keystore.jks
```

You will be prompted for the keystore's password. You may also
restrict the output to a specific alias by using the 
`-alias domain` option, where "domain" is the alias name.

**Note**: You may also use this command to view which
certificates are in your Java truststore, which is typically
located in `$JAVA_HOME/jre/lib/security/cacerts` assuming
`$JAVA_HOME` is where your JRE or JDK is installed.

### Use Keytool to View Certificate Information
This command prints verbose information about a certificate file
(`certificate.crt`), including its fingerprints, distinguished
name of owner and issuer, and the time period of its validity:

```bash
keytool -printcert -file domain.crt
```

You will be prompted for the keystore password.

### Export Certificate
This command exports a binary DER-encoded certificate 
(`domain.der`), that is associated with the alias (`domain`), in the
keystore (`keystore.jks`):

```bash
keytool -exportcert -alias domain -file domain.der \
-keystore keystore.jks
```

You will be prompted for the keystore password.

## Modifying Keystore

### Change Keystore password
This command is used to change the password of a keystore
(`keystore.jks`):

```bash
keytool -storepasswd -keystore keystore.jks
```

You will be prompted for the current password, then the new
password. You may also specify the new password in the command
by using the `-new newpass` option, where "newpass" is the password.

### Delete Alias
This command is used to delete an alias (`domain`) in a keystore
(`keystore.jks`):

```bash
keytool -delete -alias domain -keystore keystore.jks
```

You will be prompted for the keystore password.

### Rename Alias
This command will rename the alias (`domain`) to the destination
alias (`newdomain`) in the keystore (`keystore.jks`):

```bash
keytool -changealias -alias domain -destalias newdomain \
-keystore keystore.jks
```

You will be prompted for the keystore password.