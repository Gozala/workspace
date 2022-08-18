- What if we defined kind of Lisp in CAR format. We just need a minimal set of operations that would need to be supported
- ```ts
  type FFI =
   { syntax: "FFI", type: "rest", url: `${string}:${string}` }
  
  
  type Switch = {
    syntax: "switch"
    term: Term
    cases: Case[]
  }
  
  type Do = {
    syntax: "do"
    body: Term[]
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
  	| { syntax: "<", left: Term, right: Term }
      | { syntax: ">", left: Term, right: Term }
      | { syntax: ">=", left: Term, right: Term }
  	| { syntax: "<=", left: Term, right: Term }
      | { syntax: "or", left: Term, right: Term }
      | { syntax: "and", left: Term, right: Term }
      | { syntax: "=", left: Term, right: Term }
      | { syntax: "not", body: Term }
  
  type Reference = Link
  
  
  type Term =
    | Switch
    | Invoke
    | Reference
  ```