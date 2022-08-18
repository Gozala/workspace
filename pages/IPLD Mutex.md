- [[Replica]] represents a state that can be computed by interpreting related instructions in their causal order.
- ```ts
  interface Chronicle<Change> {
    on?: Link<Chronicle<Change>>
    do: Change[]
  }
  ```