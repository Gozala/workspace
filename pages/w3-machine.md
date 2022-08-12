reply-to:: https://www.notion.so/bucket-vm-73c610906fe44ded8117fd81913c7773

- This document is an attempt to articulate how I think about building w3-vm a.k.a [[bucket-vm]].
- ## Task Scheduling
	- [[UCAN invocation]]s represent VM instructions - tasks that can be scheduled. This implies that all the existing operations like `store/add`, `store/remove` are VM instructions.
	- Set of **concurrent** [[UCAN invocations]] conveniently can be represented via [[CAR]] files. This 
	   important trait is called [[homoiconicity]] in [[land of lisp]] and refer to the *duality between code and data*, that allows computers to treat instructions in a [programming language](https://en.wikipedia.org/wiki/Programming_language) as data handled by a [running program](https://en.wikipedia.org/wiki/Execution_(computing)).
	- So in order to to schedule some tasks you need to supply set of instructions in [[CAR]] file. And we already have an interface for that represented via [[store/add capability]]