# TLS Protocol Overview

# How TLS Protocol work?

![Untitled](images/TLS%20Protocol%20Overview%20c44ab5a364344201bd8522c9a225b6bb/Untitled.png)

1. The client accesses a web page using a secure URL(https).
2. The server sends the client its certificate provided from a Trusted Root Certification Authority that containt a public key.
3. The client verifies this certificate with Trusted Root Certification Authority of that certificate to ensure that certificate is legitimate.
4. The client and server negotiate the strongest type of encryption that both can support.
5. The client encrypt a session key(secret key) with the server’s public key and then send it back to the server.
6. The server decrypts the client communication with its private key to get the session key and the session is established.
7. The session key(here is symmetric encryption) is now used to encrypt and decrypt data transmiited between the client and server.
    
    From this time, both client and server are now using HTTPS(SSL/TLS + HTTP) for exchange data. HTTPS works over port 443 instead of port 80 of HTTP. Once you leave the website, the session is destroy and the key are discards. On the next visit, a new handshake is negotiated and new key are generated. 
    

# Explain Certificate Chains

We have many Root Certificate Authorities but our certificates are not signed directly from these root CAs because of security practice, instead, we have intermediate CAs  each signed within a root CA having the responsibility to issue SSL certificates for other intermediate CAs or websites, IP. 

While the root CA itself is sufficient to implement SSL security, in reality, CAs make use of intermediate certificates. Intemediate certificates also help control the number of root certificates and helping mitigate security risks and fraud. It add an addtional layer of security and helps manage security incident easier. For example, in case of security attack, it is easier to revoke only the intermediate CA that afftected by the attack instead of revoke the root CA while revoking the root CA will revoke a huge amount certificates of that root CA, that will be a nighmare. Having too many number of root CAs can lead to serious security implications, which intermediate certificates aim to resolve. One more things, intermediate also can be replicated in high numbers without compromising the security framework and helping establish the Chain of trust.

![Untitled](images/TLS%20Protocol%20Overview%20c44ab5a364344201bd8522c9a225b6bb/Untitled%201.png)

The image above describes how certificate chains work. It is a list of certificates used to authenticate an entity. This list is a hierarchy tree with a root CA, one or many intermediate CAs and the last child is an entity certificate.

But how do these magic things work? The key is asymmetric encryption algorithms. These algorithms are not only used to encrypt and decrypt, one certificate can be signed by a parent CA using that CA private key, and then we can use issues’s(CA) signature to validate if that certificate is signed by the trusted CA. 

# SSL Certificate Extensions

**Version**

This field describes the version of the encoded certificate.  For SSL certificates, the X.509 version is 3 since certificate extensions are used.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/version.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/version.png)

**Serial Number**

The Serial Number is an alphanumeric string assigned by DigiCert to each SSL certificate.  It is unique for each certificate issued by DigiCert (i.e. the issuer name and serial number identify a unique certificate).

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/serial-number.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/serial-number.png)

**Signature Algorithm**

The Signature Algorithm identifies the cryptographic algorithm used by DigiCert to sign this certificate. DigiCert signs all SSL certificates using the SHA-256 Algorithm.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/signature-algorithm.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/signature-algorithm.png)

**Issuer**

The Issuer field identifies the entity that has signed and issued the certificate. For SSL certificates, this would contain the Distinguished Name (DN) information for the Intermediate CA Certificate.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/issuer.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/issuer.png)

**Valid From** and **Valid To**

These fields indicate the validity period of the SSL certificate.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/valid-from-valid-to.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/valid-from-valid-to.png)

**Subject**

This contains the Distinguished Name (DN) information for the certificate.  The fields included in a typical SSL certificate are:

*Common Name (CN)*

*Organization (O)Locality or City (L)State or Province (S)Country Name (C)*For Extended Validation (EV) SSL certificates, these additional fields are also included:*Business CategorySerial Number (Business Registration Number)Jurisdiction StateJurisdiction Locality*

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/subject.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/subject.png)

**Public Key**

This field is used to identify the algorithm with which the SSL certificate is used, such as RSA or ECC. It also displays the size of the key in bits (e.g.: 2048 bits).

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/public-key.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/public-key.png)

**Subject Alternative Name (SAN)**

The Subject Alternative Name extension allows additional identities to be bound to the subject of the certificate. The **DNS name** (dNSName) extension is used to add an additional fully qualified domain name to an SSL certificate.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/subject-alternative-name.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/subject-alternative-name.png)

**Basic Constraints**

This extension indicates whether a certificate is a Certificate Authority (CA) or not. If the certificate is a CA, then additional information, such as the depth of the hierarchy it can sign, is specified. SSL certificates are end-entity certificates, not CA certificates.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/basic-constraints.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/basic-constraints.png)

**Subject Key Identifier (SKI)**

The Subject Key Identifier extension provides a means of identifying certificates that contain a particular public key. This is a hash value of the SSL certificate.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/subject-key-identifier.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/subject-key-identifier.png)

**Key Usage**

The Key Usage extensions define what a particular certificate may be used for (assuming the application can parse this extension). The following extensions are included in an SSL certificate:

*Digital Signature*: (Taken from [http://www.ietf.org/rfc/rfc3280.txt](http://www.ietf.org/rfc/rfc3280.txt)) The digitalSignature bit is asserted when the subject public key is used with a digital signature mechanism to support security services other than certificate signing (bit 5), or CRL signing (bit 6). Digital signature mechanisms are often used for entity authentication and data origin authentication with integrity.

*Key Encipherment*: (Taken from [http://www.ietf.org/rfc/rfc3280.txt](http://www.ietf.org/rfc/rfc3280.txt)) The keyEncipherment bit is asserted when the subject public key is used for key transport. An example of Key Encipherment is the SSL handshake, where the two applications use asymmetric encryption to wrap around the exchange of a secret key that is ultimately used for the session.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/key-usage.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/key-usage.png)

**CRL Distribution Points**

The CRL Distribution Points extension provides the location of the corresponding Certificate Revocation List (CRL) for the SSL certificate.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/crl-distribution-points.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/crl-distribution-points.png)

**Certificate Policies**

The Certificate Policies extension defines the legal rules associated with a particular certificate’s usage.  For DigiCert SSL certificates, a link to the DigiCert Relying Party Agreements is provided: https://www.digicert.com/legal-repository

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/certificate-policies.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/certificate-policies.png)

**Extended Key Usage (EKU)**

Also referred to as Enhanced Key Usage, this extension indicates one or more purposes for which the certified public key may be used, in addition to or in place of the basic purposes already indicated in the key usage extension.DigiCert SSL Certificates include the following extensions:

*Server Authentication*: (Taken from [http://www.ietf.org/rfc/rfc3280.txt](http://www.ietf.org/rfc/rfc3280.txt)) TLS WWW server authentication. Key usage bits that may be consistent: digitalSignature, keyEncipherment or keyAgreement

*Client Authentication:* (Taken from [http://www.ietf.org/rfc/rfc3280.txt](http://www.ietf.org/rfc/rfc3280.txt)) TLS WWW client authentication. Key usage bits that may be consistent: digitalSignature and/or keyAgreement

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/extended-key-usage.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/extended-key-usage.png)

**Authority Key Identifier (AKI)**

The Authority Key Identifier extension provides the key identifier of the Issuing CA certificate that signed the SSL certificate. This AKI value would match the SKI value of the Intermediate CA certificate.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/authority-key-identifier.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/authority-key-identifier.png)

**Authority Info Access**

The Authority Info Access extension provides information about how to access information about a CA, such as OCSP validation and CA policy data.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/authority-info-access.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/authority-info-access.png)

**SCT List**

This contains a list of signed certificate timestamps as required for Certificate Transparency (CT). CT is a framework of logs that assist domain owners to oversee digital certificates issued for their domains.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/sct-list.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/sct-list.png)

**Thumbprint**

This extension provides the actual hash to ensure that the certificate has not been tampered with.

![https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/thumbprint.png](https://knowledge.digicert.com/content/dam/kb/attachments/ssl-tls-certificates/certificate-extensions/thumbprint.png)

This information has been taken from RFC 3280: [http://www.ietf.org/rfc/rfc3280.txt](http://www.ietf.org/rfc/rfc3280.txt)

# References

[https://www.ibm.com/docs/en/ibm-mq/8.0?topic=certificates-how-certificate-chains-work](https://www.ibm.com/docs/en/ibm-mq/8.0?topic=certificates-how-certificate-chains-work)

[https://www.f5.com/glossary/ssl-tls-encryption](https://www.f5.com/glossary/ssl-tls-encryption)

[https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake)