---
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

# My first Litvis document

```elm {l v}
import VegaLite exposing (..)
helloLitvis : Spec
helloLitvis =
  let
    data =
      dataFromColumns []
        << dataColumn "x" (nums [ 1, 2, 3 ])
    enc =
      encoding << position X [ pName "x", pMType Ordinal ]
  in
  toVegaLite [ data [], circle [], enc [] ]
```
