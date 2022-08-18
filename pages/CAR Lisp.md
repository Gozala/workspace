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
    target: Term | FFI
    params: Term[]
  }
  
  type Term =
    | Switch
    | Invoke
    | Link
  
  
  
  ```