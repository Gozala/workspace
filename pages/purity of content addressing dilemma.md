- What should one see when they access [ipfs://bafybeicaokx57ayyfig6e6zo6mxqzkxxc5m4ffghi65tgsuk6ftadby7fu](https://ipfs.io/ipfs/bafybeicaokx57ayyfig6e6zo6mxqzkxxc5m4ffghi65tgsuk6ftadby7fu) ?
- Anything other a thing producing that hash seems antithetical to [[content addressing]], yet if you load that URL IPFS gateway will not serve you a binary blob representing that [[UnixFS]] directory, instead it will render an HTML page with a links to directory entries. In some abstract sense it does serve content corresponding to the hash, but in absolute terms it does not
  ```
  curl -s https://ipfs.io/ipfs/bafybeicaokx57ayyfig6e6zo6mxqzkxxc5m4ffghi65tgsuk6ftadby7fu | ipfs add --cid-version=1
  added bafkreicujuudjn3oipx7in5lhzeujmxsbnlxasqgnnyagvzszh4ftolazm bafkreicujuudjn3oipx7in5lhzeujmxsbnlxasqgnnyagvzszh4ftolazm
  ```
- Now consider loading another directory [ipfs://bafybeibciltrcxuymp22e4x6fr4x2mbwp45r2sl7nrhpg4e4mcsomnfl3y](https://ipfs.io/ipfs/bafybeibciltrcxuymp22e4x6fr4x2mbwp45r2sl7nrhpg4e4mcsomnfl3y) here gateway will not even render dir
-