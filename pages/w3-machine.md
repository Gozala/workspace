reply-to:: https://www.notion.so/bucket-vm-73c610906fe44ded8117fd81913c7773

- This document is an attempt to articulate how I think about building w3-vm a.k.a [[bucket-vm]].
- ## Task Scheduling
	- [[UCAN invocation]]s represent VM instructions - tasks that can be scheduled. This implies that all the existing operations like `store/add`, `store/remove` are VM instructions.
	- Set **concurrent** [[UCAN invocations]] can conveniently be represented via [[CAR]] files. This effectively