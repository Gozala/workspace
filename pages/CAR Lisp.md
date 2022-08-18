- What if we defined kind of Lisp in CAR format. We just need a minimal set of operations that would need to be supported
- ```ts
  type FFI =
   { syntax: "FFI", type: "rest", url: `${string}:${string}` }
  
  
  type Switch = {
    syntax: "switch"
    term: Term
    cases: Case[]
  }
  
  type Case = {
    syntax: "case"
    match: Pattern
    body: Term
  }
  
  type Invoke = {
    syntax: "invoke"
    target: Term | FFI | BuiltIn
    params: Term[]
  }
  
  type BuiltIn =
  	| { syntax: "<" }
      | { syntax: ">" }
      | { syntax: ">=" }
  	| { syntax: "<=" }
      | { syntax: "or" }
      | { syntax: "and" }
      | { syntax: "equal?" }
      | { syntax: "not" }
  
  type Greater = {
    term: Term
  }
  type Term =
    | Switch
    | Invoke
    | Link
  
  
  
  ```