- UCANs
  - Shipped UCAN 0.9 support in @ipld/dag-ucan & ucanto and helped migrate our services to 0.9
  - Implemented RSA key algorithm support needed in browsers to make keys non extractable.
    - https://github.com/web3-storage/ucanto/pull/102
  - Implemented support for optionals in ucanto validator and schema capabilities
    - https://github.com/web3-storage/ucanto/pull/124
  - Expanded support for principals other than `did:key`
    - To enable interop with ceramic CACAO
    - Implemented support in @ipld/dag-ucan
    - Implemented support in ucanto
- Specs
  - Designed [space providers](https://github.com/web3-storage/specs/blob/main/w3-space.md) protocol allowing us to meet desired funding models described in [user stories](https://www.notion.so/2022-11-14-User-stories-and-how-to-meet-them-3170c5c8ef82420ea97a9fec2f8ed9d3)
    - It also incorporates paid tears
  - Designed [account](https://github.com/web3-storage/specs/blob/main/w3-account.md) protocol that addresses some of the UX challenges we previously had around key management and syncing
  - Drafted did:mailto specification https://github.com/ucan-wg/did-mailto/pull/2 to support did's beyond `did:key`
  - Drafted ucan:mailto specification https://github.com/ucan-wg/ucan-mailto/pull/1
- Varsig
  - Introduced a [varsig](https://github.com/ucan-wg/ucan-ipld/#25-signature) multiformat for ipld-ucan in order to support principals beyond `did:key`
  - Which is getting adopted beyond ucans https://github.com/ChainAgnostic/varsig/pull/6
  -
- Key rotations
  - Designed solution allowing us to rotate our service keys https://github.com/web3-storage/w3protocol/issues/265
  - https://github.com/web3-storage/specs/pull/7/files
  - Implemented support for this into ucanto

## What were the top areas where you created impact, either within your team or the PLN?

- Identified need for non-cryptographic identifiers (DIDs beyond [did:key](https://w3c-ccg.github.io/did-method-key/)) in order to improve usability of [UCANs](https://github.com/ucan-wg/spec/) and more specifically:

  1. Address UX challenges in w3up product
  2. Create non-disruptive key rotation flow into our services

  Organized the effort into several sub-projects in order to reduce complexity and to enable multiple engineers to contribute to individual sub-projects concurrently

  1. Defined [did:mailto](https://github.com/ucan-wg/did-mailto/blob/draft/README.md) DID method to lay groundwork for cross organization adoption
     - This has already been incorporated into [Name Name System](https://talk.fission.codes/t/nns-the-name-name-system/3684) from Fission.
     - [Subconcious](https://subconscious.substack.com/p/noosphere-a-protocol-for-thought) team also expressed interest in incorporating this into their product.
  2. Designed [w3 account](https://github.com/web3-storage/specs/blob/main/w3-account.md) protocol which leverages [did:mailto](https://github.com/ucan-wg/did-mailto/blob/draft/README.md) identifiers to

     - Free our common customer from key management concerns & consequently allow us to divert our efforts to other important work
     - Drastically simplify delegation flows so our customers get to enjoy familiar "sharing" flows (just type peers email address) and free our team from having to implement capabilities synchronization protocol across various user devices

     _Implementation effort of this protocol is currently lead by @bengo, which leverages implementation efforts from myself in @ipld/dag-ucan and ucanto libraries_

  3. Designed & specified [key rotation](https://github.com/web3-storage/specs/blob/feat/ucan-web%2Bdns/ucan-dns%2Bweb.md) solution using `did:web` identifiers so that

     - We could rotate our service worker keys without having to perform (coordinated) upgrade of our client libraries
     - We could rotate our service worker keys without disrupting clients due to invalidated delegations.
     - We could operate our service workers without giving them access to our primary key

     _This was concerted implementation effort across @ipld/dag-ucan, ucanto, w3protocol and w3up projects first two lead by myself with @bengo's assitance and last two independently by @bengo_

  4. Designed [(HTTP cookie inspired) solution](https://github.com/web3-storage/specs/blob/main/w3-account.md#update) enabling us to ship support for `did:mailto` and key rotation without having to implement full support that would require more spec and implementation efforts around [DKIM](https://www.rfc-editor.org/rfc/rfc6376.html) signatures.

     This enables us to ship our products and make them even more decentralization in the future without breakages.

- Designed [space providers](https://github.com/web3-storage/specs/blob/main/w3-space.md) protocol allowing us to meet desired funding models described in our product [user stories](https://www.notion.so/2022-11-14-User-stories-and-how-to-meet-them-3170c5c8ef82420ea97a9fec2f8ed9d3)

  - This also leverages `did:mailto` identifiers (without been blocked by prior work streams) to simplify "free plan" enrollments.

  _Implementation is currently lead by @hugomrdias and leverages some of my work in @ipld/dag-ucan & ucanto libraries_

- Prior effort in [varsig multiformat designed in ipld ucan](https://github.com/ucan-wg/ucan-ipld/#25-signature) end up creating a greater impact

  - Invocations specification been implemented by IPVM is [leveraging varsigs](https://github.com/ucan-wg/invocation/blob/rough/README.md#14-signatures)
  - Varsig [specification is now in progress](https://github.com/ChainAgnostic/varsig/blob/first-draft/README.md) lead by @oed from Ceramic and @expede from Fission in order to be leveraged in contexts beyond even UCANs.
  - This also [unlocked possibility for shared data model](https://github.com/ucan-wg/ucan-cacao/tree/initial) across UCAN and CACAO protocol families.

- Lead various other efforts to help transition to UCAN based protocols

  1. Shipped UCAN 0.9 support in @ipld/dag-ucan & ucanto libraries that required almost no changes in our consumer code.
  2. Shipped [RSA key principals](https://github.com/web3-storage/ucanto/pull/102) in ucanto library so that in-browser clients are able to use non extractable keys preventing malicious actors stealing them.
  3. Shipped [support for optional capability fields](https://github.com/web3-storage/ucanto/pull/124) in ucanto reducing amount of code needed to be written / maintained / tested in our service handlers.

## In what ways could you have increased your impact on your team or the PLN? Identify goals you can set that could help you grow, develop and increase your impact this quarter ?

- Libraries like [ucanto](https://github.com/web3-storage/ucanto/) had been designed to as a general RPC on top of IPLD, yet it had not been communicated much in PLN missing opportunity to have greater impact with other teams.
- Our new upload protocol in many ways is superior to [pinning API](https://ipfs.github.io/pinning-services-api-spec/) yet we missed opportunity e.g. with Brave because our API is not positioned as standard API that has gone through [IPIP](https://github.com/ipfs/specs#interplanetary-improvement-process-ipip).
