# OpenSSL Notes

OpenSSL is a versatile command line tool that can be used for a large
variety of tasks related to Public Key Infrastructure (PKI) and HTTPS
(HTTP over TLS).

## Openssl genrsa command

The `genrsa`  command generates an RSA private key, which essentially
involves the generation of two prime numbers. When generating the
key, various symbols will be output to indicate the progress of the
generation. A "." represents each number which has passed an initial
sieve test; "+" means a number has passed all the prime tests (the
actual number depends on the key size). command options:

`openssl genrsa [-3 | f-4] [-aes128 | -aes192 | -aes256 | -des | -des3] [-out file] [-passout arg] [numbits]`

* `-3 | f-4`: the public exponent to use, either 3 or 65537. The
  default is 65537.
* `-aes128 | -aes192 | -aes256 | -des | -des3`: encrypt the
  private key with the AES, DES, or triple DES ciphers, respectively,
  before outputting it. If none of these options are specified, no
  encryption is used. If encryption is used, a passphrase is prompted
  for, if it is not supplied via the -passout option.
* `-out file`: the output file to write to, or standard output
  if not specified.
* `-passout arg`: the output file password source
* `numbits`: the size of the private key to generate in bits. This
  must be the last option specified. The default is 2048.

## Openssl req command

The `req` command primarily creates and processes certificate requests
in PKCS#10 format. It can additionally create self-signed certificates,
for use as root CAs. command options:

`openssl req [-new] [-nodes] [-key file] [-keyout file] [-config file] [-md5 | -sha1 | -sha256] [-days num_of_days] [-out file] [-x509]`

* `-new`: generate a new certificate request. The user is prompted for
  the relevant field values. The actual fields prompted for and their
  max and min sizes are specified in the configuration file and any
  requested extensions. If the `-key` option is used, it will generate
  a new RSA private key using information specified in the config file.
* `-nodes`: do not encrypt the private key
* `-key file`: the file to read the private key from. It also
  accepts PKCS#8 format private keys for PEM format files.
* `-keyout file`: the file to write the newly created private
  key to. If this option is not specified, the filename present in
  the configuration file is used.
* `config file`: specify an alternative configuration file
* `-md5 | -sha1 | -sha256`: the message digest to sign the request
  with. This overrides the digest algorithm specified in the
  configuration file. Some public key algorithms may override this
  choice. For instance, DSA signatures always use SHA1.
* `-days num_of_dats`: specify the number of days to cerify the
  certificate for. The default is 30 days. Used with the -x509 option.
* `-out file`: the output file to write to, or standard output
  if not specified.
* `-x509`: output a self-signed certificate instead of a certificate
  request. This is typically used to generate a test certificate or
  a self-signed root CA. The extensions added to the certificate (if
  any) are specified in the config file. Unless specified using the
  `-set_serial` option, 0 is used for the serial number.

## Openssl x509 command

The `x509` command is a multi-purpose certificate utility. It can
be used to display certificate information, convert certificates
to various forms, sign certificate requests like a "mini CA", or
edit certificate trust settings. command options:

`openssl x509 [-signkey file] [-CA file] [-CAkey file] [-CAserial file] [-req] [-days arg] [-in file] [-out file] [-extensions section]`

* `-signkey file`: self-sign file using the supplied private key.
* `-CA file`: the CA certificate to be used for signing. When this
  option is present, `x509` behaves like a mini CA. The input file
  is signed by the CA using this option; that is, its issuer name is
  set to the subject name of the CA and it is digitally signed using
  the CA's private key. This option is normally combined with the 
  `-req` option. Without the `-req` option, the input is a certificate
  which must be self-signed.
* `-CAkey file`: set the CA private key to sign a certificate with.
  Otherwise it is assumed that the CA private key is present in the CA
  certificate file.
* `-CAserial file`: use the serial number in file to sign a certificate.
  The file should consist of one line containing an even number of
  hex digits with the serial number to use. After each use the serial
  number is incremented and written out to the file again. The default
  filename consists of the CA certificate file base name with `.srl`
  appended. For example, if the CA certificate file is called `mycacert.pem`,
  it expects to find a serial number file called `mycacert.srl`.
* `-req`: expect a certificate request on input instead of a certificate.
* `-days arg`: the number of days to make a certificate valid for.
  The default is 30 days.
* `-in file`: the input file to read from.
* `-out file`: the output file to write to, or standard output if
  none is specified.
* `-extensions section`: the section to add certificate extensions
  from. If this option is not specified, the extensions should either
  be contained in the unnamed (default) section or the default section
  should contain a variable called "extensions" which contains the
  sections to use. For example: `v3_ca`

## Openssl pkcs12 command

The `pkcs12` command allows PKCS#12 files (sometimes referred to as
PFX files) to be created and parsed. By default, a PKCS#12 file is
parsed; a PKCS#12 file can be created by using the `-export`
option. command options:

`openssl pkcs12 [-export] [-in file] [-inkey file] [-name name] [-out file]`

* `-export`: create a PKCS#12 file (rather than parsing one)
* `-in file`: the input file to read from, or standard input if
  not specified. The order doesn't matter but one private key and
  its corresponding certificate should be present. If additional
  certificates are present, they will also be included in the
  PKCS#12 file.
* `-inkey file`: file to read a private key from. If not present,
  a private key must be present in the input file.
* `-name name`: specify the "friendly name" for the certificate
  and private key. This name is typically displayed in list boxes
  by software importing the file.
* `-out file`: the output file to write to, or standard output
  if not specified.

## About Certificate Signing Requests (CSRs)

If you would like to obtain an SSL certificate from a certificate
authority (CA), you must generate a certificate signing request
(CSR). A CSR consists mainly of the public key of a key pair, and
some additional information. Both of these components are inserted
into the certificate when it is signed.

Whenever you generate a CSR, you will be prompted to provide
information regarding the certificate. This information is known as a
Distinguished Name (DN). An important field in the DN is the
**Common Name** (CN), which should be the exact Fully Qualified 
Domain Name (FQDN) of the host that you intend to use the certificate
with. It is also possible to skip the interactive prompts when 
creating a CSR by passing information via command line or from a 
file.

The other items in a DN provide additional information about your
business or organization. If you are purchasing an SSL certificate
from a certificate authority, it is often required that these 
additional fields, such as "Organization"; accurately reflect your
organization's details.

