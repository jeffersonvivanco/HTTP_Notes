# OpenSSL Notes

OpenSSL is a versatile command line tool that can be used for a large
variety of tasks related to Public Key Infrastructure (PKI) and HTTPS
(HTTP over TLS).

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

