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
- ```ipldsch
  
  type varint Patch {
    Add "add"
    Remove "remove"
    
  }
  
  # Our whole system is simply a map of spaces with DID as they're keys
  # DID owner and delegates are only ones able to interact with it
  type struct W3 {
    DID: Space
  }
  
  type struct Space {
     command {
     	  store StoreControl
        upload UploadControl
     }
     upload { &Task: UploadStatus }
     store { &CAR: StoreStatus }
  }
  
  type union StoreStatus {
     # User can write a request
     | Request StoreRequest
     # System updates with presigned URL so that user
     # can complete the upload
     | Pending StorePending
     # System updates state when request is complete
     | Done StoreDone
     # System updates state when request expires
     | Expried StoreExpired
     # System update state when request fails
     | Failed StoreFailed
  }
  
  {
    "did:key:zAlice": {
    	command: {
      	store: {
          	request: {
              	link: 
              }
          }
      }
    	store: {
      	request: {
          	
          }
      }
    }
  }
  
  type struct StoreRequest {
    link &CAR
    size Int
    origin optional &CAR
  }
  
  type struct StorePending {
    link &CAR
    url String
    headers {String: String}
    
    receipt &Receipt
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
     
     receipt &Receipt
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