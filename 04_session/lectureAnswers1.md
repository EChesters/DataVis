---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

## Task 1

_Change value of scExponent above from 1.0 to 1.2_

```elm {v l=hidden highlight=[25,26,27]}
logBubble : Spec
logBubble =
    let
        data =
            dataFromUrl "https://vega.github.io/vega-lite/data/gapminder-health-income.csv"

        enc =
            encoding
                << position X
                    [ pName "income"
                    , pMType Quantitative
                    , pScale [ scType scLog ]
                    , pAxis [ axGrid False ]
                    ]
                << position Y
                    [ pName "health"
                    , pMType Quantitative
                    , pScale [ scZero False ]
                    , pAxis [ axGrid False ]
                    ]
                << size
                    [ mName "population"
                    , mMType Quantitative
                    , mScale
                        [ scRange (raNums [ 0, 5000 ])
                        , scType scPow -- Area proportional to datum raised to power of some exponent
                        -- , scExponent 1.0 -- Default value (i.e. area directly proportional to datum)
                        , scExponent 1.2
                        ]
                    ]
    in
    toVegaLite [ width 600, height 350, data [], circle [], enc [] ]
```

**Is scaling a circle area by raising it to the power other than 1 'cheating'? Which is more honest, to ensure the graphical size is consistently proportional to the magnitude of the data it represents, or to scale it so it is perceived as proportional to magnitude?**
Using exponent of size 1 can be perceived as more honest, but depends on the interpretation of the data. If increasing the size makes the data easier to read and the representations are relative, then this may not affect interpretation.
