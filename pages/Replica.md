- Our [[replication protocol]] is defined in terms of atomic, immutable, content addressed “operations” 
  which are wrapped in a container structure that adds casual ordering through hash-links. *(We define this container structure using generic [[Revision]] interface.
- Revision is defined in terms of set of **changes** applied to some prior revision. It can also be viewed as a log of **operations**.
- ```ts
  export interface Revision<Operation> {
    // Prior revision to which changes are applied. If omitted
    // revision represents a first change.
    of?: Link<Revision<Operation>>
    // Set of operations to be applied
    do: Operation[]
  }
  ```
- Revision interface intentionally does not prescribe semantics making it possible to apply different ones at will. Better yet same revision could be
- ```ts
  export interface Replica<Change> {
    of: Link<Revision<Change>>
    as: Link<Code>
  }
  
  export type Code =
    | { type: "rest", url: `https:` }
  ```
- Idea is that `Code` represents an `HTTP` endpoint which one could `POST` a [[CAR]] encoded `Input` (Input MUST be an only root) as defined below:
  ```ts
  export interface Input<State, Change> {
    state: State
    delta: Change[]
  }
  
  export interface Endpoint<State, Change> {
    post(input: CAR<[Input<State, Change>]>): CAR<[State]>
  }
  
  interface CAR<Roots extends [...unknown[]]> extends Uint8Array {
    [phantomData]?: { roots: Roots }              
  }
  
  declare var phantomData:symbol
  ```
- For example we could implement `Total` replica as follows
  ```ts
  export const post = (input:CAR<[Input<number, number>]>): number => {
    const [{state, delta}] = decodeCAR(input)
    let total = state
    for (const n of delta) {
      total += n
    }
    return total
  }
  ```
- Note that we could memoize `Replica` by it's CID and never have to recompute it again.