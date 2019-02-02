---
follows: imdbRoot
---

## A Frequency histogram

```elm {l=hidden v}
histogram : Spec
histogram =
  let
    enc =
      encoding
        << position X [ pName "IMDB_Rating",
                        pMType Ordinal,
                        pBin []
                      ]
        << position Y [ pMType Quantitative,
                        pAggregate opCount
                      ]
        << color [ mName "IMDB_Rating",
                   mMType Ordinal
                 ]
  in
  toVegaLite [ data [], bar [], enc [] ]
```
