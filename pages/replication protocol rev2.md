- We can define [[replication protocol]] of atomic, immutable, content addressed "operations" - [[UCAN]] invocations that are casual ordering through hash links. Arbitrary state can be expressed in terms of [[Revision]]s defined as follows:
- ```ts
  interface Revision<T extends Operation> extends UCAN.Invocation<T> {
  }
  
  interface Operation extends Capability {
    origin?: Link
  }
  ```