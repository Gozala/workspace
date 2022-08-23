- We can define [[replication protocol]] of atomic, immutable, content addressed "operations" - [[UCAN]] invocations that are casual ordering through hash links. Arbitrary state can be expressed in terms of [[Revision]]s defined as follows:
- ```ts
  interface Revision<T extends Operation> extends UCAN.Invocation<T> {
  }
  
  interface Operation extends Capability {
    origin: Link<Revision<this>> | null
  }
  ```
- This means we can represent arbitrary DAG as follows
  ```ts
  interface Append {
    can: "dag/append"
    with: DID
    shards: CARLink[]
    origin: Link<DAGRevision>
  }
  
  interface Join {
    can: "dag/join"
    with: DID
    forks: Link<DAGRevision>[]
    origin: Link<DAGRevision>
  }
  
  interface DAGRevision extends Revision<Append|Join> {}
  ```
- You can publish interpretation of the `Revision` using `Publish` protocol
- ```ts
  export type Code =
    | { type: "rest", url: `https:` }
  
  interface Replica<T extends Operation> extends UCAN.Invocation<Publish<T>> {
    
  }
  
  interface Publish<T extends Operation> {
    can: "replica/publish"
    with: DID
    revision: Link<Revision<T>>
    semantics: Link<Code>
  }
  ```
-