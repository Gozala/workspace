- [[Replica]] represents a state that can be computed by interpreting related instructions in their causal order.
- ```ts
  export interface Revision<Change> {
    of?: Link<Revision<Change>>
    do: Change[]
  }
  ```
- ```ts
  export interface Replica<Change> {
    of: Link<Revision<Change>>
    as: Link<Code>
  }
  
  export type Code =
    | { type: "rest", url: `https:` }
  ```
- Idea is that `Code` represents an `HTTP` endpoint which one could `POST` a [[CAR]] file with a root block corresponding to:
-