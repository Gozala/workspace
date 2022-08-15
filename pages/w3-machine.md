reply-to:: https://www.notion.so/bucket-vm-73c610906fe44ded8117fd81913c7773

- This document is an attempt to articulate how I think about building w3-vm a.k.a [[bucket-vm]].
- ## Task Scheduling
	- [[UCAN invocation]]s represent VM instructions - tasks that can be scheduled. This implies that all the existing operations like `store/add`, `store/remove` are VM instructions.
	- Set of **concurrent** [[UCAN invocations]] conveniently can be represented via [[CAR]] files. This 
	   important trait is called [[homoiconicity]] in [[land of lisp]] and refers to the *duality between code and data*, that allows computers to treat instructions in a [programming language](https://en.wikipedia.org/wiki/Programming_language) as data handled by a [running program](https://en.wikipedia.org/wiki/Execution_(computing)).
	- To schedule some task(s) agent needs to supply system a set of invocations in form of [[CAR]] file. We already have a protocol for this represented by [[store/add capability]]
	  
	  ```ts
	  interface StoreAdd {
	     can: 'store/add'
	     with: AccountDID
	     link: CarCID
	  }
	  
	  type CarCID = CID
	  type AccountDID = `did:key:${string}`
	  ```
		- When service gets `store/add` invocation it issues [AWS presigned URL](https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html) where [[CAR]] with that exact [[CID]] can be written.
		- Conceptually this in equivalent of allocating some memory where you can write some data.
	- As we have established we have some nice [[homoiconicity]] going for us, meaning agent can
		- Encode set of invocations/instructions in a [[CAR]] file.
		- Make instruction set available to a VM using `store/add`.
	- Schedule invocations using `system/spawn` invocation
	  
	  > Let's get nerdy here! We've just introduces [meta-circular evaluator](https://en.wikipedia.org/wiki/Meta-circular_evaluator) 🤯. You know how in lisp you can quote arbitrary expressions so they can be transformed as lists and evaluated at will ? Well that is what we have going on here, our [s-expression](https://en.wikipedia.org/wiki/S-expression) is a [[CAR]], [[store/add]] capability corresponds to quoting, and [[system/enqueue]] corresponds to [[eval]]
- So we could schedule task(s) for execution via [[system/spawn capability]] that can be represented as follows
  
  ```ts
  interface SystemSpawn {
    can: "system/spawn"
    with: AccountDID // account that is billed for execution
    task: CarCID
  }
  ```
	- Idea is that VM will unpacks the [[CAR]] and schedules each [[UCAN invocation]] in a concurrent task.
- ## Control Flow
	- For our VM to be of any use we need to support a some more instructions, e.g we could map a [[leveldown]] APIs to corresponding capabilities. Still, any real program needs some control flow instructions
		- While defining control flow instructions and creating a new programming language in the process is exciting, it would be an overkill, instead our VM will have a Foreign Function Interface ([[FFI]])
	- Foreign functions can be deployed via [[system/deploy capability]] which associates HTTP REST endpoint with an [[Account DID]].
	  > ⚠️ It is important to deploy functions because that gives it a [[did:key]] that can be used to delegate it other capabilities it can utilize
	  
	  > General idea is that dev will create an account for the program expose it's private key to program so that when invoked it could authorize/sign followup actions and make use of capabilities delegated to it.
	- We can define [[system/deploy capability]] as follows
	  ```ts
	  interface SystemDeploy {
	    can: "system/deploy"
	    with: AccountDID
	    code: { type: "rest", url: URL }
	  }
	  ```
		- In the future we could allow deploying [[WASM]] programs or [[JS]] functions that we could run.
	- Deployed [[FFI]] functions can be invoked using [[system/invoke capability]] defined as
	- interface SystemInvoke {
	    can: "system/invoke"
	    with: ForeignFunctionDID
	    data: CarCID  // DATA send to endpoint
	  }
	  
	  // It is just an Account DID where function is deployed
	  type ForeignFunctionDID = AccountDID
	- When scheduler executes [[system/invoke capability]] it sends `data` [[CAR]] file to the designated `url` via HTTP POST request.
	- Response from the endpoint MUST be a [[CAR]] file that represents a [[continuation]], which is associated with a task that initiated the invocation.
		- This allows foreign functions to write results into arbitrary accounts as long as they have capabilities to do so. Furthermore data passed into invocation could include delegated capabilities allowing it to decide where to write what dynamically.
- Now that we have primitives to spawn tasks and invoke foreign functions our VM can do just about anything. Furthermore we could define additional capabilities like [[leveldown]] API and expose those through the VM natively or better yet via [[FFI]]
- It is also worth pointing out that [[ucanto]] fits perfectly into all this! ucanto endpoint has associated [[Service DID]] which can be used as an [[Account DID]] for deployment. [[ucanto]] services also just takes CAR with [[UCAN Invocation]]s and acts on them.
	- Only thing that does not fit perfectly yet is that response is [[CBOR]] as opposed to [[CAR]] but we could easily fix that.
	- As a result we end up with a network of services that can delegate tasks to one another.