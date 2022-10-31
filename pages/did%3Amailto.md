- ## Format
- The format for the `did:mailto` method conforms to the [[DID-CORE](https://w3c-ccg.github.io/did-method-key/#bib-did-core)]
  specification and is simple. It consists of the  `did:mailto`  prefix,
  followed by a valid email address as per [rfc6854](https://datatracker.ietf.org/doc/html/rfc6854) specification
  
  The ABNF for the key format is described below:
- ```
  did-mailto-format := did:key:<user>@<domain>
  user   		       = dot-atom-text / quoted-string
  domain             = dot-atom-text / "[" *dtext-no-obs "]"
  dtext-no-obs       = %d33-90 / ; Printable US-ASCII
                       %d94-126  ; characters not including
                                 ; "[", "]", or "\"
  ```
- A simple example of a valid `did:mailto` DID is:
- ```
  did:mailto:alice@web.mail
  ```
- ## Operations
  
  The following section outlines the DID operations for the `did:mailto`
  method. Implementers might note that this DID Method is derives document from the email message send from the given email address with DKIM-Signature.
-
- #### Create
            
  Creating a DID is done by:
- 1. Registering an email address via traditional means with a service supporting DomainKeys Identified Mail (**DKIM**) email authentication method.
####                 Resolve
- The following steps MUST be executed to resolve a DID to a DID document using the `did:mailto` method.
- Send email to an email address that follows `did:mailto:` prefix with TBD message format
- Receive a response from the email address that follows `did:mailto:` prefix with TBD subject line and valid DKIM-Signature header.
- Verify a