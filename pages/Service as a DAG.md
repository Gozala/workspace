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
- All our UCANs can be modeled as follows
	- `with` - Encodes mutable space identifier `did:key` or `did:mailto`
	- `can` - Encodes IPLD path within the space (MUST target entry in either map or a list)
	- `nb` - Encodes one of the universal `Operation` to be executed (as per schema below)
- ```ipldsch
  type struct Task {
    with DID
    do Command
    meta {String : Any} (implicit {})
    nnc optional String
    sig Varsig
    prf [&UCAN]
  }
  
  type Command union {
    # Writes entry at the target IPLD path. If entry already exists
    # at that path it overwrites.
    Put "dag/put"
    # Writes data at the target IPLD path. If entry already exists
    # write fails. If IPLD path targets list element item is
    # item is inserted after the target entry. If IPLD path targets
    # list entry is added to the list.
    Post "dag/post"
    # Reads data at the target IPLD path
    Get "dag/get"
    # Deletes entry from the target IPLD path. 
    Delete "dag/delete"
    # Selects entries from the target IPLD path. IPLD path MUST target
    # map, list or an entry with in them. If targets an entry selects
    # next set of entries.
    Select "dag/select"
  } representation keyed
  
  type struct Put {
    at IPLDPath
    data Any 
  }
  
  type struct Post {
    at IPLDPath
    data Any
  }
  
  type struct Delete {
    at IPLDPath
  }
  
  type struct Get {
    at IPLDPath
  }
  
  type Select {
    at IPLDPath
    limit optional Int
  }
  
  # Task is a UCAN that invokes single operation encoded
  # as capability
  type Task = UCAN<[Command<Any>]>
  
  # Command is just a UCAN capability
  type Command<Insruction> struct {
    # Encodes (mutable) user space identifier
    with DID
    # Encodes IPLD Path within the current space DAG
    can IPLDPath
    # Encodes operation targeting specified DAG path
    nb Operation<Instruction>
  }
  
  
  type Request
  
  # We support univeras set of operations across the whole
  # DAG. Any request to a service is simply a put 
  type Operation<Entry> union {
    # Writes entry at the target IPLD path. If entry already exists
    # at that path it overwrites.
    Entry "put"
    # Writes data at the target IPLD path. If entry already exists
    # write fails. If IPLD path targets list element item is
    # item is inserted after the target entry. If IPLD path targets
    # list entry is added to the list.
    Entry "post"
    # Reads data at the target IPLD path
    Get "get"
    # Deletes entry from the target IPLD path. 
    Delete "delete"
    # Selects entries from the target IPLD path. IPLD path MUST target
    # map, list or an entry with in them. If targets an entry selects
    # next set of entries.
    Select "select"
  } representation keyed
  
  type struct OperationCapability<Entry> {
    with DID
    can IPLDPath
    nb Patch<Entry>
  } 
  
  
  
  # Delete and Get do not have any input, so they are just empty maps
  # allowing us to extend those in the future.
  type Delete unit representation emptymap
  type Get unit representation emptymap
  
  # Selects number of entries from the target IPLD path. If target is a
  # list or map reads first `limit` entries. If target is entry within
  # list or map reads next `limit` entries.
  type Select struct {
    limit optional Int
  }
  ```
- With above operation set in place we can represent web3.storage as a DAG matching following schema. Users could send above defined operations encoded as UCANs with qualified proof chains
- ```ipldsch
  type struct W3 {
    # Entire system is map of user spaces keyed by DID
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