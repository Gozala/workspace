- We can think of arbitrary [[did:key]] as an account in the network. If we have never heard of DID it is a new account with `0` balance and we don't need to track it until it's balance had been increased by moving some funds into it.
- This creates some interesting opportunities as programs could open new accounts simply by generating cryptographic keypairs. And if program had been delegated UCAN with a capability to transfer $5 from some existing account it will be able to fund this newly created account by transferring those funds to it. Even more interestingly program can delegate all the account capabilities to new account to a known agent and never retain account private key.
- In many ways this starts to look similar to how [[solana]]'s [programming model](https://docs.solana.com/developing/programming-model/overview) except without a blockchain to drive it.