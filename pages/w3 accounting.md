- I have been advocating for a controversial idea of doing all of our "accounting" using [[w3-account]] data model. Accounting as in:
  > **the system of recording and summarizing business and financial transactions and analyzing, verifying, and reporting the results**
- To put it differently I think all of the **mutable state** in the system should be addressed using [[did:key]] identifiers that correspond to an account. Doing this has following benefits:
	- Addressing by [[did:key]] implies corresponding **private key** holder or their delegate can mutate the state.
	- There is no hierarchy just unions!
		- This simplifies things a lot. For example consider hierarchical alternative with concurrent writes that change `did:key:zAlice:/home/pictures` and `did:key:zAlice/home/pictures/cat.png`.
			- You end up with a conflict that needs to be resolved
			- You'll likely have to throw away one of the writes
		- None of this applies if accounts are just sets, things simply converge
- One of the ideas I have put forward is to use [[w3-account]]s to represent **upload state** a.k.a session. Which could simply look as following sketch
  ```ts
  import { SigningAuthority } from "@ucanto/athority"
  import { invoke, delegate } from "@ucanto/client"
  
  async upload = (cars, {connection, agent, account, service}) => {
    // generate session account
    const session = await SigningAuthority.generate()
    
    // Create a transaction that will
    const transaction = [
      // 1. Subsidize a session with user account
      invoke({
        issuer: agent,
        audience: service,
        capability: {
          can: "account/subsidize",
          with: account,
          account: session
        }
      }),
      // 2. Adds all the CARs to the user account
      ...cars.map(car => invoke({
          issuer: agent,
          audience: service,
          capability: {
            can: "account/add",
            with: account,
            link: car.cid
          }
        }))
      // 3. Links all CARs with an upload session
      ...cars.map(car => invoke({
      	issuer: session,
          audience: service,
          capability: {
            can: "account/link",
            with: session,
            link: car.cid
          }
      }))
    ]
    
    const results = await connection.execute(...transaction)
    // ...
  }
  ```
- An Alternative proposal