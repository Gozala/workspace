- I have been advocating for a controversial idea of doing all of our "accounting" using [[w3-account]] data model. Accounting as in:
  > **the system of recording and summarizing business and financial transactions and analyzing, verifying, and reporting the results**
- To put it differently I think all of the **mutable state** in the system should be addressed using [[did:key]] identifiers that correspond to an account. Doing this has following benefits:
	- Addressing by [[did:key]] implies corresponding **private key** holder or their delegate can mutate the state.
	- There is no hierarchy! Which simplifies things a lot. Fo example consider two ``