- DID holding agent (the delegator) MAY delegate capability to a [[did:email]] identified agent (the delegate), in which case it MUST provide verifiable [[did:email]] document in the `fct.dkim` field.
- `fct.dkim` field MUST be a CID to a DAG-CBOR block conforming to a following IPLD schema which is a data model capturing [rfc6376](https://www.rfc-editor.org/rfc/rfc6376.html)
- ```ipldsch
  type DomainKeysIdentifiedMail {
    version DKIMVersion (rename "v" implicit "1")
    publicKey PublicKey (rename "k")
    hashingAlgorithm Algorithm (rename "a" implicit "0x12")
    
    domain string (rename "d") -- email domain (part after "@")
    user string (rename "u") -- email username (part before "@" sign)
    selector string (rename "s") - DKIM selector
    payload Payload (rename "h") -- payload that was signed
    -- Message canonicalization
    canonicalization (rename "c" implicit Simple) Canonicalization
    
    hash bytes (rename "bh") -- The hash of the canonicalized body
    signature VarSig (rename "b") -- signature
    
  }
  
  type Payload {String: String}
  
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
  
  type Canonicalization {
    | Simple
    | Relaxed
  }
  ```
- DomainKeys Identified Mail ([[DKIM]]) protocol is widely used protocol that can be utilized by a principal  delegate