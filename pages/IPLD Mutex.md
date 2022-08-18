- [[Replica]] represents a state that can be computed by interpreting related instructions in their causal order.
- ```ts
  export interface Edition<Change> {
    on?: Link<Chronicle<Change>>
    do: Change[]
  }
  ```
- ```ts
  export interface Replica<Change> {
    of: Chronicle<Change>
    code: 
  }
  ```