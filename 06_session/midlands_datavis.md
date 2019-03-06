---
id: litvis

elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import VegaLite exposing (..)
```

```elm {l=hidden, v}
crimeData =
    dataFromUrl "https://gicentre.github.io/data/westMidlands/westMidsCrimesAggregated.tsv"

crimeColours =
    categoricalDomainMap
        [ ( "Anti-social behaviour", "rgb(59,118,175)" )
        , ( "Burglary", "rgb(81,157,62)" )
        , ( "Criminal damage and arson", "rgb(141,106,184)" )
        , ( "Drugs", "rgb(239,133,55)" )
        , ( "Robbery", "rgb(132,88,78)" )
        , ( "Vehicle crime", "rgb(213,126,190)" )
        ]

encHighlight =
    encoding
        << position X [ pName "month", pMType Temporal, pAxis [ axTitle "Month" ] ]
        << position Y [ pName "reportedCrimes", pMType Quantitative, pAxis [axTitle "Reported Crimes"] ]
        << color
            [ mSelectionCondition (selectionName "mySelection")
                [ mName "crimeType", mMType Nominal, mScale crimeColours ]
                [ mStr "black" ]
            ]
        << opacity
            [ mSelectionCondition (selectionName "mySelection")
                [ mNum 1 ]
                [ mNum 0.1 ]
            ]
```

```elm {l=hidden v }
singleSelection : Spec
singleSelection =
    let
        sel =
            selection
                << select "mySelection" seSingle []
    in
    toVegaLite
        [ width 540
        , crimeData [] -- Data specified in code block above
        , encHighlight [] -- Encoding specified in code block above
        , line [ maInterpolate miMonotone ]
        , sel []
        ]
```
