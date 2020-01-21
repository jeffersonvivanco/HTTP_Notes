# Java Keytool
*Java Keytool* is a key and certificate management tool that is used
to manipulate Java Keystores, and is included with Java. A *Java
Keystore* is a container for authorization certificates or public
key certificates, and is often used by Java-based applications for
encryption, authentication, and serving over HTTPS. A keystore is a
password protected file often with JKS extension. Keystore is not a
digital certificate. Its entries are protected by a keystore
password. A keystore entry is identified by an *alias* and it
consists of keys and certificates that form a trust chain.

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
The above command extracts required information such as public key,
DN and put it in a standard CSR format in file domain.csr. A 
commercial CA should verify all information before they can issue a
certificate with their signature.

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
keytool -genkey -alias Deb -keyalg RSA -validity 365 \
-keystore keystore.p12
```

If the specified keystore does not already exist, it will be
created after the requested information is supplied. This will
prompt for the keystore password (new or existing), followed by a
Distinguished Name prompt (for the private key), then the desired
private key password.

So now `keystore.p12` has a private and public key. Keystore is not a
digital certificate. It is a store to keep certificates and private
keys. Now let us export a certificate containing the public key.
**You cannot export the private key using keytool**. To extract the
private key you need to use the Java Cryptography API. Use the
following command to export the public key as a certificate.
`keytool -export -alias Deb -file Deb.cer -keystore keystore.12`. Now
`Deb.cer` is the certificate containting your public key. `Deb.cer`
is a self-signed certificate. Its owner and Issuer have the same DN.
This certificate is signed by the private key of Deb.

### -gencert
Generates a certificate as a response to a certificate request file
(which can be created by the `keytool -certreq` command). The
command reads the request from *infile* (if omitted, from the
standard input), signs it using alias's private key, and output the
X.509 certificate into *outfile* (if omitted, to the standard
output). If -rfc is specified, output format is BASE64-encoded PEM;
otherwise, a binary DER is created.

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

## CACERTS
The `cacerts` file represents a system-wide keystore with CA
certificates. System administrators can configure and manage that
file using keytool, specifying `jks` as the keystore type. The
`cacerts` keystore file ships with several root CA certificates. The
initial password of the `cacerts` keystore file is `changeit`.
System administrators should change that password and the default
access permission of that file when installing the SDK.

**Important**
Verify your `cacerts` file. Since you trust the CAs in the
`cacerts` file as entities for signing and issuing certificates
to ther entities, you must manage the `cacerts` file carefully.
The `cacerts` file should contain only certificates of the CAs you
trust. It is your responsibility to verify the trusted root CA
certificates bundled in the `cacerts` file and make your own
trust decisions. To remove an untrusted CA certificate from the
`cacerts` file, use the `delete` option of the keytool command.

## Difference between a Java Keystore and a Truststore
In most cases, we use a keystore and a truststore when our
application needs to communicate over SSL/TLS. Usually, these
are password-protected files that sit on the same file as our
running application. The default format used for these files is
JKS until Java 8.

Since Java 9, though, the default keystore format is PKCS12. The
biggest difference between JKS and PKCS12 is that JKS is a format
specific to Java, while PKCS12 is a standardized and language-
neutral way of storing encrypted private keys and certificates.

### Java KeyStore
A Java keystore stores private key entries, certificates with
public keys or just secret keys that we may use for various
cryptographic purposes. It stores each by an alias for ease of
lookup.

Generally speaking, keystores hold keys that our application owns
that we can use to prove the integrity of a message and the
authenticity of the sender, say by signing payloads.

Usually, we'll use a keystore when we are a server and want to use
HTTPS. During an SSL handshake, the server looks up the private key
from the keystore and presents its corresponding public key and
certificate to the client.

Correspondingly, if the client also needs to authenticate
itself--a situation called mutual authentication--then the client
also has a keystore and also presents its public key and
certificate.

There's no default keystore, so if we want to use an encrypted
channel, we'll have to set `javax.net.ssl.keyStore` and
`javax.net.ssl.keyStorePassword`. If our keystore format is 
different than the default, we could use 
`javax.net.ssl.keyStoreType` to customize it.

Of course, we can use these keys to service other needs as well.
Private keys can sign or decrypt data, and public keys can verify
or encrypt data. Secret keys can perform these functions as well.
A keystore is a place that we can hold onto these keys.

We can also interact with the keystore programmatically.

### Java TrustStore
A truststore is the opposite--while a keystore typically holds
onto certificates that identify us, a truststore holds onto
certificates that identify others.

In Java, we use it to trust the third party we're about to
communicate with. Take our earlier example, if a client talks to
a Java-based server over HTTPS, the server will look up the
associated key from its keystore and present the public key and
certificate to the client.

We, the client, then look up the associated certificate in our
truststore. If the certificate or Certificate Authorities
presented by the external server is not in our truststore, we'll
get an `SSLHandshakeException` and the connection won't be set up
successfully.

Java has bundled a truststore called `cacerts` and it resides in
the `$JAVA_HOME/jre/lib/security` directory.

It contains default, trusted Certificate Authorities:

```bash
$ keytool -list -cacerts
Enter keystore password
Keystore type: JKS
Keystore provider: SUN

Your keystore contains 92 entries

verisignclass2g2ca [jdk], 2018-06-13, trustedCertEntry,
Certificate fingerprint (SHA1): B3:EA:C4:47:76:C9:C8:1C:EA:F2:9D:95:B6:CC:A0:08:1B:67:EC:9D
```

We see here that the truststore contains 92 trusted certificate
entries and one of the entries is the *verisignclass2gca* entry.
This means that the JVM will automatically trust certificates
signed by *verisignclass2gca*.

Here, we can override the default truststore location via the
`javax.net.ssl.trustStore` property. Similarly, we can set
`javax.net.ssl.trustStorePassword` and 
`javax.net.ssl.trustStoreType` to specify the truststore's
password and type.





































