# Let's Encrypt How It Works?

The objective of Let's Encrypt and the ACME protocol is to make it possible to set up an
HTTPS server and have it automatically obtain a browser-trusted certificate, without any
human intervention. This is accomplished by running a certificate management server agent
on the web server.

To understand how the technology works, let's walk through the process of setting up
`https://example.com/` with a certificate management agent that supports Let's Encrypt.

There are 2 steps to this process. First, the agent provides to the CA that the web server
controls the domain. Then, the agent can request, renew, and revoke certificates for that
domain.

## Domain Validation
Let's Encrypt identifies the server administrator by public key. The first time the agent
software interacts with Let's Encrypt, it generates a new key pair and proves to the Let's
Encrypt CA 