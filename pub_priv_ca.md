# Private and Public CA

The choices that you have for obtaining your certificates include:

* Public Certificates - Purchasing your certificates from a public
Internet Certificate Authority (CA).

  * Public CAs issue certificates to all the users who applies for
  it and pays the appropriate fees based on some proof of identity.
  This leel of proof varies depending on the identification policy
  of the CA. These policies have to be evaluated by the users
  requesting for the certificate as to whether it suits the security
  needs of the organisation. This can be preferred only in scenarios
  like, a limited number of certificates are required where a local/
  private CA is not affordable.

* Private Certificates - Operating your own local CA to issue
private certificates for your users and applications.

  * In case of a local/Private CA, certificates are issued to
  systems and users within a more limited scope confined to a
  company or an organisation. The privileges of creating and
  issueing certificates will be limited only to those users who are
  trusted members of your group. Therefore this technique provides
  better security. But it comes at a cost of time and resources that
  must be invested. In the longer run for a large scale 
  organisation, it is preferable to have a Private CA.

* Self Signed Certificates - Created and issued by the individual
himself who is using the application server.

  * As the name suggests, here the certificates are signed by the
  owner himself. Self-signed certificates are generally utilized
  for testing local servers and cannot be deployed in production
  environments as it has no relation with the identity of the
  person or organisation who issued it. In this case even though
  the certificate delivers the same level of security to data that
  flows in the tunnel between browser and server, the owner of a web
  service stays anonymous. Thus to avoid mis-issuances and other
  sorts of fraudulent behavior a Trusted CA authorizes a Self signed
  certificate. Now the web-browsers will accept the certificate without throwing the error *"This certificate is not trusted
  because it is self signed"* and such certificates can be deployed
  on production environments as well.

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

We need to ass the root certificate to any laptops, desktops,
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
keyUsage = digitalSignature, nonRepudation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = dev.mergebot.com
DNS.2 = dev.mergebot.com.192.168.1.19.xip.io
```

The X509 command is needed to do the signing with the root
certificate and private key.

Now we run the command to create the certificate

`openssl x509 -req -in dev.mergebot.com.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial -out dev.mergebot.com.crt -days 1825 -sha256 -extfile dev.mergebot.com.ext`

You should be prompted for your CA private key pass phrase

I now have 3 files: dev.mergebot.com.key (the private key),
dev.mergebot.com.csr (the certificate signing request), and
dev.mergebot.com.crt (the signed certificate). I can now
configure my web server with the private jey and the certificate.
For any other dev sites, we can just repeat this last part of
creating a certificate. No need to install any new certificates on
any of my devices. Much better.
