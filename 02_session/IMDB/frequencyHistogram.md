---
follows: imdbRoot
---

## A Frequency histogram

```elm {l=hidden v}
histogram : Spec
histogram =
  let
    cfg =
      configure
        << configuration (coView [vicoStroke Nothing ])
        << configuration
            (coAxis
              [ axcoTicks False
              , axcoDomain False
              , axcoLabelAngle 0
              ]
            )

    enc =
      encoding
        << position X [ pName "IMDB_Rating",
                        pMType Ordinal,
                        pBin [],
                        pAxis []
                      ]
        << position Y [ pMType Quantitative,
                        pAggregate opCount,
                        pAxis []
                      ]
        << color [ mName "IMDB_Rating",
                   mMType Ordinal
                 ]
  in
  toVegaLite [ cfg [], data [], circle [], enc [] ]
```
