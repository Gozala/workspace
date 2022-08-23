- We can define [[replication protocol]] of atomic, immutable, content addressed "operations" - [[UCAN]] invocations that are casual ordering through hash links. Arbitrary state can be expressed in terms of [[Revision]]s defined as follows:
- ```ts
  interface Revision<T extends Operation> extends UCAN.Invocation<T> {
  }
  
  interface Operation extends Capability {
    origin?: Link<Revision<this>>
  }
  ```
- This means we can represent arbitrary DAG as follows
  ```ts
  interface Append {
    type: "append"
    shards: CARLink[]
    origin: Link<DAGRevision>
  }
  
  interface Join {
    type: "join"
    forks: Link<DAGRevision>[]
    origin: Link<DAGRevision>
  }
  
  interface DAGRevision extends Revision<Append|Join> {}
  ```
- You can publish interpretation of the `Revision` using ``
-