source:: https://github.com/ucan-wg/spec/blob/91e936d515a958854790978ac47095d6dacdf95e/README.md#52-principal-alignment

- In delegation, the  `aud`  field of every witness MUST match the  `iss`  field of the outer UCAN (the one being delegated to). This alignment  MUST form a chain back to the originating principal for each resource.