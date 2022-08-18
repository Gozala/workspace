- [[Replica]] represents a state that can be computed by interpreting related instructions in their causal order.
- ```ts
  export interface Revision<Change> {
    of?: Link<Revision<Change>>
    : Change[]
  }
  ```
- ```ts
  export interface Replica<Change> {
    of: Link<Revision<Change>>
    code: 
  }
  ```