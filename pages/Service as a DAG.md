- [Lack of functions](https://github.com/ipld/ipld/issues/263) in [[IPLD Schema]] motivates workarounds in order to describe web3.storage protocol in terms of it. I have pretty much submitted to defining functions as structs like
  
  ```ipldsch
  type W3 struct {
    store: Store
    upload: Upload
  }
  
  type Store {
    add: { in: UCAN<> }
  }
  ```