- ## Motivation
- Identifying all participating agents in UCAN delegation via [[did:key]] introduces several UX challenges:
	- The delegator must obtain [[did:key]] of the delegate in order to issue a UCAN token. (Alice needs to ask Bob what his [[did:key]] is before she can delegate any capability to him. Bob needs to find his [[did:key]] and write it down for Alice as neither would be able to remember one).
	- Familiar email based key recovery is only possible by delegating capabilities to the service who can delegate those capabilities back to a new key.  This in turn implies that service could potentially abuse this if account key is used by user across several services.
- Both problems could be addressed through WhoCAN specification which defines [[did:mailto]] identifiers which can play both a role of the delegator and/or the delegate.
-
-
- DID holding agent (the delegator) MAY delegate capability to a [[did:mailto]] identified agent (the delegate), in which case it MUST provide verifiable [[did:mailto]] document in the `fct.dkim` field.
- `fct.dkim` field MUST be a CID to a DAG-CBOR block conforming to a following IPLD schema which is an IPLD representation of `DKIM-Signature` header and the header of the as per [rfc6376](https://www.rfc-editor.org/rfc/rfc6376.html)
- type DomainKeysIdentifiedMail {
    version DKIMVersion (rename "v" implicit "1")
    publicKey PublicKey (rename "k")
    hashingAlgorithm Algorithm (rename "a" implicit "0x12")
    -- email domain (part after "@")
    domain string (rename "d")
    -- email username (part before "@" sign)
    user string (rename "u")
    - DKIM selector
    selector string (rename "s")
    -- headers that were signed
    headers Headers (rename "h")
    -- public key of the user (subject MUST contain it)
    userKey PublicKey (rename "i")
    
    signature VarSig (rename "b") -- signature
    
  }
  
  type Headers {String: String} representation listpairs
  
  -- Public key with a multiformat code tag
  type PublicKey bytes
  
  -- Multiformat code corresponding to hashing Algorithm used before signing 
  type Algorithm {
    | sha1 ("0x11")
    | sha2_256 ("0x12")
    | sha2_512 ("0x13")
  } representation int
  
  -- DKIM Version
  type DKIMVersion {
    | One ("1")
  } representation string
	- [Header Canonicalization](https://www.rfc-editor.org/rfc/rfc6376.html#section-3.4.2) MUST be applied to the data represented by the model.
	- When message contains multiple `DKIM-Signature` headers it is up to the implementer to decide which one to encode as a data model, however it is RECOMMENDED to optimize for size and there for choose one containing less headers.
	- Email `subject` MUST contain `did:key` corresponding to `userKey`
- DomainKeys Identified Mail ([[DKIM]]) protocol is widely used protocol that can be utilized by a principal  delegate
-