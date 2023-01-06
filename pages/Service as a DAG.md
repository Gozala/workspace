- [Lack of functions](https://github.com/ipld/ipld/issues/263) in [[IPLD Schema]] motivates workarounds in order to describe web3.storage protocol in terms of it. I have pretty much submitted to defining functions as structs like
  
  ```ipldsch
  type Protocol {
    DID: Space
  }
  
  type Space struct {
    store Store
    upload Upload
  }
  
  type Store {
    add StoreAdd
    remove StoreRemove
    list StoreList
  }
  
  type Upload {
   add UploadAdd
   remove UploadRemove
   list UploadList
  }
  
  type StoreAddIn struct {
    link &CAR
    size Int
    origin optional &CAR
  }
  
  type StoreAddOut enum {
    | Done StoreAddOutDone 
    | Upload StoreAddOutUpload
  }
  
  type StoreAddOutDone struct {
    with DID
    link &CAR
  }
  
  type StoreAddOutUpload struct {
    headers {String: String}
    url String
    with DID
    link &CAR
  }
  
  # This is actually a function
  type StoreAdd struct {
  	in StoreAddIn
      out StoreAddOut
  }
  
  # You get the idea
  ```
- Raising various issues for IPLD schemas, working on [UCAN invocation receipts](https://github.com/web3-storage/ucanto/issues/151) and the fact that invocations start tasks with observable [event streams](https://github.com/web3-storage/w3infra/issues/117) got me thinking that perhaps instead of designing service as a UCAN RPC we should instead be designing it as an IPLD DAG 💡.
- If we design it that way we'll have "writable" part of the DAG and readable part of the DAG. Users could send "requests", or rather write "commands" into "writable" part of the DAG and read / observe results from the readable "receipts" part of the DAG.
- I think this might more natural model the fact that our invocations either schedule some async task that our system processes and consequently updates it's state, which can be read or observed. Tasks that simply read state aren't really a tasks they are in fact queries over the DAG.
- Now representing whole system as an actual IPLD DAG is going to be prohibitively impractical, however we could still model a system as a DAG without materializing one.
- Here is how we could re-envision our service as a DAG and avoid need for functions in [[IPLD schema]]
- Instead of defining capabilities in terms of actions one could perform, we could take page from [IPLD Patch](https://ipld.io/specs/patch/fixtures/fixtures-1/) book and define universal set of operations
	- It is worth calling out similarity with HTTP small and fairly universal set of HTTP methods
	- Another analogy could be made with SQL which also has small set of operations `insert`, `select`, `update`, `delete`
- We could model this as follows
- ```ipldsch
  # Command is one of the standard operation tasks
  type Command union {
    # Writes this task at the specified IPLD path. If identical task
    # at specified path already exists command is noop. If different
    # entry exists under specified path operation is denied unless
    # entry has failed or deleted status.
    Task<{ path IPLDPath value Any }> "dag/put"
    # Reads entry at the specified IPLD path.
    Task<{ path IPLDPath }> "dag/get"
    # Deletes entry from the specified IPLD path. 
    Task<{ path IPLDPath }> "dag/remove"
    # Selects entries from the target IPLD path. IPLD path MUST target
    # map, list or an entry with in them. If targets an entry selects
    # next set of entries.
    Task<{ path IPLDPath limit optional Int }> "dag/select"
  } representation inline {
    discriminantKey "do"
  }
  
  # Task is similar to one in UCAN invocation draft spec, except it
  # has extra fields from invocation to make it self-contained as per
  # https://github.com/ucan-wg/invocation/issues/6
  type struct Task<Input> {
    with DID
    do String
    input Input
    meta {String : Any} (implicit {})
    nnc optional String
    sig Varsig
    prf [&UCAN]
  }
  ```
- With the above operation set in place we can represent web3.storage as a DAG with a following schema on which users could execute above defined set of operations
- ```ipldsch
  type struct W3 {
    DID: Space
  }
  
  type struct Space {
    # Store is a CAR storage. Actors can send operations to query / mutate it.
    store { &CAR: C }
    # Upload is basically a list of user uploads that user can add / remove items to.
    # It is keyed by upload roots
    upload { &Any: Upload }
  }
  
  # Every store entry is a state machine and it can be in one of the following states
  type union Store {
     # User can write a request
     | Task<StoreRequest>
     
     # System can update state from Request to Pending providing
     # presigned URL allowing user to complete store request.
     | Receipt<StoreRequest, StorePending, unit> "pending"
     
     # System can update state from Request or Pending to Done
     | Receipt<StoreRequest, StoreDone, StorePending> "done"
     
     # System can update state from Pending to Expired
     | Receipt<StoreRequest, StoreExpired, StorePending> "expried"
     
     # System can update state from Request or Pending to Failed
     | Receipt<StoreRequest, StoreFailed, StorePending> "failed"
  } representation keyed
  
  # Initial state
  type struct StoreRequest {
    link &CAR
    size Int
    origin optional &CAR
  }
  
  
  type struct StorePending {
    link &CAR
    url String
    headers {String: String}
    
    receipt &Receipt#<StoreRequest, Null>
  }
  
  type UCAN = any # UCAN See https://github.com/ucan-wg/ucan-ipld/#21-principal
  type Task#<Entry>
  struct {
    with DID
    can IPLDPath
    nb Operation#<Entry>
  }
  
  type struct Receipt #<Request Out Origin>
  {
    # Link to the request this is receipt of
    request &UCAN#<Task<Request>>
    # Current state
    out Out
    
    # When system updates state it will link to a prior receipt
    # providing complete trace of changes
    origin optional &Receipt#<Origin>
  
    # Signature from the actor that perform the update
    sig Varsig
  
    # All the other metadata
    meta { String: Any }
  }
  
  
  type struct StoreDone#<Request>
  {
     link &CAR
     size Int
     origin optional &CAR
     
     receipt &Receipt#<Request, Null|StorePending>
  }
  
  
  type struct StoreExpired#<Request>
  {
    link &CAR
    reason String
    
    receipt &Receipt#<Request, StorePending>
  }
  
  type struct StoreFailed {
    link &Car
    reason String
    
    receipt
  }
  
  type enum Task {
    | Store StoreTask
    | Upload UploadTask
  }
  
  type struct StoreTask {
  
  }
  ```
-