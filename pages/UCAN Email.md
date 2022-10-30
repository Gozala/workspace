- DID holding agent (the delegator) MAY delegate capability to a [[did:email]] identified agent (the delegate), in which case it MUST provide verifiable [[did:email]] document in the `fct.dkim` field.
- `fct.dkim` field MUST be a CID to a DAG-CBOR block conforming to a following IPLD schema
- ```ipldsch
  type DomainKeysIdentifiedMail {
    version DKIMVersion (rename "v" implicit "1")
    publicKey PublicKey (rename "k")
    hashingAlgorithm Algorithm (rename "h")
  }
  
  -- Public key with a multiformat code tag
  type PublicKey bytes
  -- DKIM Version
  type DKIMVersion {
    | One ("1")
  }
  ```
- DomainKeys Identified Mail ([[DKIM]]) protocol is widely used protocol that can be utilized by a principal  delegate