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
- ###   Create
  
  Creating a did:mailto value consists of creating a cryptographic key pair
  and encoding the public key using the format provided in  Section [§ 2. The did:key Format](https://w3c-ccg.github.io/did-method-key/#format). The creation of a DID Document is also performed by taking
  the public key value and expanding it into DID Document. An example is given
  below that expands the ed25519 did:key
   `did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK`  into its
  associated DID Document:
-