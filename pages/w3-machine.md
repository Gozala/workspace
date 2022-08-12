reply-to:: https://www.notion.so/bucket-vm-73c610906fe44ded8117fd81913c7773

- This document is an attempt to articulate how I think about building w3-vm a.k.a [[bucket-vm]].
- ## Task Scheduling
	- [[UCAN invocation]]s represent VM instructions - tasks that can be scheduled. This implies that all the existing operations like `store/add`, `store/remove` are VM instructions.
	- Set of **concurrent** [[UCAN invocations]] conveniently can be represented via [[CAR]] files. This 
	   important trait is called [[homoiconicity]] in [[land of lisp]] and refer to the *duality between code and data*, that allows computers to treat instructions in a [programming language](https://en.wikipedia.org/wiki/Programming_language) as data handled by a [running program](https://en.wikipedia.org/wiki/Execution_(computing)).
	- To schedule some task(s) agent needs to supply system a set of invocations in form of [[CAR]] file. We already have an protocol for this represented by [[store/add capability]]
	  
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
		- Encode set of invocations/instructions in a [[CAR]]
		- Make instruction set available to a VM using `store/add`.
	- Schedule invocations using `system/enqueue` invocation
	  
	  > Let's get nerdy here! We've just introduces [meta-circular evaluator](https://en.wikipedia.org/wiki/Meta-circular_evaluator) 🤯. You know how in lisp you can quote arbitrary expressions so they can be transformed as lists and evaluated at will ? Well that is what we have going on here, our [s-expression](https://en.wikipedia.org/wiki/S-expression) is a [[CAR]], [[store/add]] capability corresponds to quoting, and [[system/enqueue]] corresponds to [[eval]]
- So we could schedule task(s) for execution via [[system/enqueue capability]] that can be represented as follows
  
  ```ts
  interface SystemEnqueue {
    can: "system/enqueue"
    with: AccountDID // account that is billed for execution
    task: CarCID
  }
  ```
	- Idea is that VM will unpack the [[CAR]] and schedule each [[UCAN invocation]] for an execution.
- ## Control Flow
	- For our VM to be of any use we need to support a lot more instructions. Some obvious ones would just correspond to [[leveldown]] APIs, still any real program would needs some control flow instructions
		- While defining control flow instructions and creating a new programming language in the process is exciting, it is overkill, instead our VM will have a Foreign function interface ([[FFI]])
	- We can define `system/invoke` capability/instruction which would delegate invocation to a foreign functions over HTTP API.
	- ```ts
	  interface SystemInvoke {
	    can
	  }
	  ```