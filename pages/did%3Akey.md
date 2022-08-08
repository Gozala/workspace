title:: did:key

- The format of the [`did:key`](https://w3c-ccg.github.io/did-method-key/) method conforms to the [[DID-CORE]] specification and is simple. It consists of the  `did:key`  prefix,
  followed by a Multibase [[MULTIBASE](https://w3c-ccg.github.io/did-method-key/#bib-multibase)] base58-btc encoded value that is a
  concatenation of the Multicodec [[MULTICODEC](https://w3c-ccg.github.io/did-method-key/#bib-multicodec)] identifier for the public key type and the raw bytes associated with the public key format.