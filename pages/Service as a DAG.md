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
- Instead of defining capabilities in terms of actions one could perform, we could take page from [IPLD Patch](https://ipld.io/specs/patch/fixtures/fixtures-1/) book and define universal set of capabilities
	- It is worth calling out similarity with HTTP small and fairly universal set of HTTP methods
	- Another analogy could be made with SQL which also has small set of operations `insert`, `select`, `update`, `delete`
- All our UCANs can be modeled as follows
	- `with` - Encodes mutable space identifier `did:key` or `did:mailto`
	- `can` - Encodes IPLD path within the space (MUST target entry in either map or a list)
	- `nb` - Encodes one of the universal `Operation` to be executed (as per schema below)
- ```ipldsch
  type Operation union {
    # Writes entry at the target IPLD path. If entry already exists
    # at that path it overwrites.
    Put "put"
    # Writes data at the target IPLD path. If entry already exists
    # write fails. If IPLD path targets list element item is
    # item is inserted after the target entry. If IPLD path targets
    # list entry is added to the list.
    Post "post"
    # Reads data at the target IPLD path
    Get "get"
    # Deletes entry from the target IPLD path. 
    Delete "delete"
    # Selects entries from the target IPLD path. IPLD path MUST target
    # map, list or an entry with in them. If targets an entry selects
    # next set of entries.
    Select "select"
  } representation keyed
  
  # Put and Post have any type, they are entries to be written under the
  # target path
  type Put any
  type Post any
  
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
    store { &CAR: Store }
    # Upload is basically a list of user uploads that user can add / remove items to.
    # It is keyed by upload roots
    upload { &Any: Upload }
  }
  
  # Every store entry is a state machine and it can be in one of the following states
  type union Store {
     # User can write a request
     | Request StoreRequest
     
     # System can update state from Request to Pending providing
     # presigned URL allowing user to complete store request.
     | Pending StorePending
     
     # System can update state from Request or Pending to Done
     | Done StoreDone
     
     # System can update state from Pending to Expired
     | Expried StoreExpired
     
     # System can update state from Request or Pending to Failed
     | Failed StoreFailed
  } representation keyed
  
  type struct StoreRequest {
    link &CAR
    size Int
    origin optional &CAR
  }
  
  type struct StorePending {
    link &CAR
    url String
    headers {String: String}
    
    receipt &Receipt # Links to StoreRequest
  }
  
  type struct Receipt {
    request &Any 
    sig Varsig
    rec &Receipt
    meta { String: Any }
  }
  
  
  type struct StoreDone {
     link &CAR
     size Int
     origin optional &CAR
     
     receipt &Receipt # Links to StoreRequest
  }
  
  
  type struct StoreExpired {
    link &CAR
    reason String
    
    receipt &Receipt
  }
  
  type enum Task {
    | Store StoreTask
    | Upload UploadTask
  }
  
  type struct StoreTask {
  
  }
  ```
-