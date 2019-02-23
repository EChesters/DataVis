---
id: litvis
follows: simpleData
---

@import "../css/datavis.less"

## Simple Visualization

```elm {l v}
initialDesign : Spec
initialDesign =
    let
        data =
            dataFromColumns []
                << dataColumn "category" (strColumn "a" myDataTable |> strs)
                << dataColumn "value" (numColumn "b" myDataTable |> nums)

        enc =
            encoding
                << position X [ pName "category", pMType Ordinal ]
                << position Y [ pName "value", pMType Quantitative ]
    in
    toVegaLite [ data [], bar [], enc [] ]
```
