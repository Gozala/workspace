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
  
  async upload = (cars, {connection, agent, account, service, proofs}) => {
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
        },
        proofs
      }),
      // 2. Adds all the CARs to the user account
      ...cars.map(car => invoke({
          issuer: agent,
          audience: service,
          capability: {
            can: "account/add",
            with: account,
            link: car.cid
          },
        	proofs
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
- An [alternative proposal](https://filecoinproject.slack.com/archives/C02BZPRS9HP/p1661188442189249?thread_ts=1660942938.783889&cid=C02BZPRS9HP) had been put forward:
	- Introduce `session/create` capability that can be used to create a "session" object of sorts identified by invocation `CID`.
	- Introduce `session/append` capability that can be used to associate specific CARs with that session.
- In practice code will look very similar
  ```ts
  import { SigningAuthority } from "@ucanto/athority"
  import { invoke, delegate } from "@ucanto/client"
  
  async upload = (cars, {connection, agent, account, service, proofs}) => {
    // generate session linked to the account
    const session = await delegate({
      issuer: agent,
      audience: service,
      capabilities: [{
        can: "session/create",
        with: account
      }],
      proofs
    })
    
    // Create a transaction that will
    const transaction = [
      // 1. Subsidize a session with user account
      session,
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
      // 3. Add all CARs to the upload session
      ...cars.map(car => invoke({
      	issuer: agent,
          audience: service,
          capability: {
            can: "session/append",
            with: 'ipfs://' + session.cid,
            link: car.cid
          }
      }))
    ]
    
    const results = await connection.execute(...transaction)
    // ...
  }
  ```
- At glance it may seem that only thing that has changed is verbs `account/subsidize → session/create` and `account/link → session/append`. However more importantly it introduced additional questions ?
	- What is the effect of `session/create` invocation ?
	- What happens if we did not submit `session/create` or do it after `session/append` ?
	- How do we know `agent` is allowed to `session/append` to a requested session ?
		- ⚠️ Note that previously we could simply check if issuer was a `session` or an agent to whom session delegated capability. But now we have to do some out of bound checks to see who created an agent.
- There is another important differentiator between two approaches, that is while any `account` is able to subsidize a `session` it does not necessarily have capability to append to it! This may not seem that useful in simple upload use case, but on the other hand it can be used to build powerful features like "workspaces" where users can invite each other and link relevant data.
- > You could potentially build something like that by introducing hierarchical addressing e.g. `did:key:zAlice:/bafy...session`, but then again hierarchies come with complexity and no clear advantage in this context
- There is yet another more general approach to representing state in form of [[replication protocol]], which is very similar to [[git]] in that it establishes causal order of operations using hash links and allows us to think of a state in terms of operations that would have produced it. Unlike above two alternatives it's deliberately permissionless, meaning you can create a new revision of the state without a permission from the author of the previous revision. This [[forkability]] is often accompanied with companion permissioned publishing capability. In other words why one can freely fork any revision they can only publish new revision under their own replica.
- With this approach we could represent multipart upload as follows:
  ```ts
  ```