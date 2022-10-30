- DID holding agent (the delegator) MAY delegate capability to a [[did:email]] identified agent (the delegate), in which case it MUST provide verifiable [[did:email]] document in the `fct.dkim` field.
- `fct.dkim` field MUST be a CID to a DAG-CBOR block conforming to a following IPLD schema
- ```ipldsch
  ```
- DomainKeys Identified Mail ([[DKIM]]) protocol is widely used protocol that can be utilized by a principal  delegate