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
