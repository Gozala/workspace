- What should one see when they access [ipfs://bafybeicaokx57ayyfig6e6zo6mxqzkxxc5m4ffghi65tgsuk6ftadby7fu](https://ipfs.io/ipfs/bafybeicaokx57ayyfig6e6zo6mxqzkxxc5m4ffghi65tgsuk6ftadby7fu) ?
- Anything other a thing producing that hash seems antithetical to [[content addressing]], yet if you load that URL IPFS gateway will not serve you a binary blob representing that [[UnixFS]] directory, instead it will render an HTML page with a links to directory entries. In some abstract sense it does serve content corresponding to the hash, but in absolute terms it does not
  ```
  curl -s https://ipfs.io/ipfs/bafybeicaokx57ayyfig6e6zo6mxqzkxxc5m4ffghi65tgsuk6ftadby7fu | ipfs add --cid-version=1
  added bafkreicujuudjn3oipx7in5lhzeujmxsbnlxasqgnnyagvzszh4ftolazm bafkreicujuudjn3oipx7in5lhzeujmxsbnlxasqgnnyagvzszh4ftolazm
  ```
- If this is not too bad consider loading [ipfs://QmSgEEQaKStnsX7aEApfjTT76FWRbAUYUr5pFV8XpRS6Mi](https://ipfs.io/ipfs/QmSgEEQaKStnsX7aEApfjTT76FWRbAUYUr5pFV8XpRS6Mi) I do not get a binary blob corresponding [[UnixFS]] directory, instead I get an HTML page with a list of files in that
-