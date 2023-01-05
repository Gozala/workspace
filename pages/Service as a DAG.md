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
    | StoreAddDoneResponse
    | StoreAddUploadRequiredResponse
  }
  
  type 
  
  type StoreRemove struct {
    link &CAR
  }
  
  type StoreList = Cursor
  type UploadAdd struct {
    root &Any
    shards optional [&CAR]
  }
  
  type UploadRemove struct {
    root &Any
  }
  
  type UploadList = Cursor
  
  type Cursor struct {
    cursor optional String
    size optional Int
  }
  ```