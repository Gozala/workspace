- DID holding agent (the delegator) MAY delegate capability to a [[did:email]] identified agent (the delegate), in which case it MUST provide verifiable [[did:email]] document in the `fct.dkim` field.
- `fct.dkim` field MUST be a CID to a DAG-CBOR block conforming to a following IPLD schema
- ```ipldsch
  type DomainKeysIdentifiedMail {
    version DKIMVersion (rename "v" implicit "1")
    publicKey PublicKey (rename "k")
    hashingAlgorithm Algorithm (rename "a" implicit "0x12")
    
    domain string (rename "d") -- email domain (part after "@")
    user string (rename "u") -- email username (part before "@" sign)
    signature VarSig (rename "s") -- signature
  }
  
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
  ```
- DomainKeys Identified Mail ([[DKIM]]) protocol is widely used protocol that can be utilized by a principal  delegate