# How to create a self-signed certificate

## Steps

1. Generate a key pair that we will use to sign your self-signed certificate with.
  You can use `keytool` or `openssl`.


# How to create your own SSL Certificate Authority for Local HTTPS development

## How it works
To request a certificate from a CA like Verisign, you send them a
Certificate Signing Request (CSR), and they give you a certificate
in return that they signed using their root certificate and private
key. All browsers have a copy (or access a copy from the OS) of
Verisign's root certificate, so the browser can verify that your
certificate was signed by a trusted CA.

That's why when you generate a self-signed certificate the browser
doesn't trust it. It's self-signed. It hasn't been signed by a CA.
But we can generate our own root certificate and private key, add
the root certificate to all the devices we own just once, and then
all certificates that we generate and sign will be inherently
trusted.

## Becoming a (tiny) Certificate Authority
It's kind of ridiculous how easy it is to generate the files needed
to become a certificate authority. It only takes two commands.
First, we generate our private key:

`openssl genrsa -des3 -out myCa.key 2048`

You will be prompted for a pass phrase, which I recommend not
skipping and keeping safe. The pass phrase will prevent anyone who gets your private key from generating a root certificate of their
own. 

Then we generate a root certificate:

`openssl req -x509 -new -nodes -key myCa.key -sha256 -days 30 -out myCa.pem`

You will be prompted for the pass phrase of your private key (that
you just chose) and a bunch of questions. The answers to those
questions aren't that important. They show up when looking at the
certificate, which you will almost never do. I suggest making the
Common Name something that you'll recognize as your root
certificate in a list of other certificates. That's really the
only thing that matters.

You should now have two files: myCa.key (your private key) and 
myCa.pem (your root certificate). Congrats, you're now a CA sort
of. To become a real CA, you need to get your root certificate on
all the devices in the world. Let's start with the ones you own.

## Installing your root certificate

We need to add the root certificate to any laptops, desktops,
tablets, and phones that will be accessing your HTTPS sites. This
can be a bit of a pain, but the good news is that we only have to
do it once. Once our root certificate is on each device, it will be
good until it expires.

## Creating CA-Signed Certificates for your dev sites
Now that we're a CA on all our devices, we can sign certificates
for any new dev sites that need HTTPS. First, we create a private
key:

`openssl genrsa -out dev.mergebot.com.key 2048`

Then we create a CSR

`openssl req -new -key dev.mergebot.com.key -out dev.mergebot.com.csr`

You'll get the same questions as you did above and, again, your
answers don't matter. In fact, they matter even less because you
won't be looking at this certificate in a list next to others.

Next we'll create the certificate using our CSR, the CA private
key, the CA certificate, and a config file, but first we need to
create that config file.

The config file is needed to define the Subject Alternative Name
(SAN) extension we discussed in my last article. I created a new
file named `dev.mergebot.com.ext` and added the following contents:

```ext
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = dev.mergebot.com
DNS.2 = dev.mergebot.com.192.168.1.19.xip.io
```

The X509 command is needed to do the signing with the root
certificate and private key.

**What is the SSL Certificate Subject Alternative Name?**
The SAN is an extension to the X.509 specification that allows users
to specify additional host names for a single SSL certificate. The
use of the SAN extension is standard practice for SSL certificates,
and it's on its way to replacing the use of the common name.

**SAN certificates**
A SAN certificate is a term often used to refer to a multi-domain SSL
certificate. An SSL certificate with more than one name is associated
using the SAN extension. There's a subtle difference though. When
using the term "multi-domain certificates", we're generally referring
to an SSL certificate that has the ability to cover multiple host
names (domains). If we use the term "SAN certificates", we're
probably referring to a particular certificate that includes any
name in the SAN extension. From a technical standpoint, every
certificate issued today is effectively a SAN certificate, as the
CA/B forum requires the certification authority to add the content of
the common name to the SAN as well. Even if the certificate covers a
single name, it will still use the SAN extension and include that
single name.

Background

The X.509 specifications regulate the *Internet X.509 Public Key
Infrastructure Certificate*, which includes the SSL certificates
format. Originally, SSL certificates only allowed the designation of
a single host name in the certificate subject called Common Name. The
common name represents the host name that's covered by the SSL
certificate. Trying to use the certificate for a website that doesn't
match the common name will result in a security error, also known as
*hosy name mismatch* error. After the original specification, it
became clear it would be helpful to have a single certificate to
cover multiple host names. The most common example is a single
certificate covering both the root domain and the www subdomain.
In fact, it's common to reuse the same SSL certificate for
`example.com` and `www.example.com`. The X.509 specification allows
users to define extensions to be attached to a CSR and the final
server certificate. Using the SAN extension, it's possible to
specify several host names in the `subjectAltName` field of a
certificate. Each of these names will be considered protected by the
SSL certificate.

Now we run the command to create the certificate

`openssl x509 -req -in dev.mergebot.com.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial -out dev.mergebot.com.crt -days 1825 -sha256 -extfile dev.mergebot.com.ext`

You should be prompted for your CA private key pass phrase

I now have 3 files: dev.mergebot.com.key (the private key),
dev.mergebot.com.csr (the certificate signing request), and
dev.mergebot.com.crt (the signed certificate). I can now
configure my web server with the private key and the certificate.
For any other dev sites, we can just repeat this last part of
creating a certificate. No need to install any new certificates on
any of my devices. Much better.