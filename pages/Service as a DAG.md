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
- This might also better capture the fact that our invocations either
	- Schedule a task that goes through a system pipeline which updates state as it makes the progress. In fact we already want a way to observe these state changes.
	- Query state over overall state DAG.
- Representing whole system as an actual IPLD DAG is going to be prohibitively impractical, however we could still model a system as a DAG without materializing one.
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
  # Entire system is modeled as map of user spaces keyed
  # by space DIDs
  type W3 struct {
    DID Space
  }
  
  # Spaces MAY have varios providers e.g storage provider, upload provider etc...
  type Space struct {
    # Storege provider is map keyed by CARs stored. Users can send commands to
    # update or query it.
    store { &CAR: Receipt<Store, StoreStatus> }
    # Upload provider is a map keyed by upload roots. Users can send commands to
    # add / remove and list items it stores
    upload { &Any: Receipt<Upload, unit> }
  }
  
  # Every store entry is a state machine and it can be in one of the following states
  type StoreStatus union {
    # When user submits dag/put command it will appears as queued
    | unit "queued"
    # System MAY update state from queued to pending in order to
    # provide presigned upload URL for user to complete the task.
    | StorePending pending
    # System MAY update state from queued or pending to done.
    | unit "done"
    # System MAY update state from pending to expired if upload
    # is not complete. User may submit another store request to retry.
    | unit "expried"
    # System MAY update state from queued or pending to failed
    # e.g if space is out of storage ran out of storage capacity.
    | StoreFailed "failed"
  } representation inline {
    discriminantKey "status"
  }
  
  
  type Store struct {
    link &CAR
    size Int
    origin optional &CAR
  }
  
  type Upload struct {
    root &Any
    shards optional [&CAR]
  }
  
  type StoreFailed struct {
    reason String
  }
  
  type StorePending struct {
    url String
    headers Headers
  }
  
  type Headers { String: String }
  
  ```
-