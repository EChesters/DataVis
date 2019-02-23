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

```elm {l=hidden}
vvTable =
    let
        table =
            """visualVariable,    q, o, s, a, d
               colour hue,        1, 1, 1, 1, 1
               colour saturation, 2, 2, 2, 2, 2
               colour lightness,  1, 1, 1, 1, 1
               orientation,       3, 3, 3, 3, 3
               shape,             1, 1, 1, 1, 1
               texture,           2, 2, 2, 2, 2
               arrangement,       1, 1, 1, 1, 1
               size,              3, 3, 3, 3, 3
               focus,             1, 1, 1, 1, 1
               location,          1, 1, 1, 1, 1"""
                |> fromCSV
                |> gather "property"
                    "rating"
                    [ ( "q", "quantitative" )
                    , ( "o", "orderable" )
                    , ( "s", "selectable" )
                    , ( "a", "associative" )
                    , ( "d", "dissociative" )
                    ]
    in
    dataFromColumns []
        << dataColumn "visualVariable" (table |> strColumn "visualVariable" |> strs)
        << dataColumn "property" (table |> strColumn "property" |> strs)
        << dataColumn "rating" (table |> numColumn "rating" |> nums)
```

```elm {l=hidden v}
summary : Spec
summary =
    let
      myShapes = -- is a function, not var
        categoricalDomainMap
          [ ("1", "square")
          , ("2", "circle")
          , ("3", "triangle-up")
          ]

      shapeColors =
        categoricalDomainMap
            [ ( "1", "red" )
            , ( "2", "blue" )
            , ( "3", "green" )
            ]
      enc =
          encoding
              << position Y [ pName "visualVariable", pMType Nominal ]
              << position X [ pName "property", pMType Nominal ]
              << shape [mName "rating", mMType Ordinal, mScale myShapes ]
              << color [mName "rating", mMType Ordinal, mScale shapeColors ]



    in
    toVegaLite [ width 400, height 400, vvTable [], enc [], point [maFilled True, maSize 200, maOpacity 1] ]
```

Update the **1**s in this function to reflect your own ratings. Then see if you can produce a graphical summary of the table by encoding the ratings with one or more channels that you think provides a descriptive graphical summary.
