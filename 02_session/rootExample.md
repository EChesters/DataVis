---
id: litvis
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

```elm {l=hidden}
import VegaLite exposing (..)
```

# Bicycle Hire Datavis Designs

Here is the dataset we will use to explore alternative designs:

```elm {l}
data =
  dataFromUrl "https://gicentre.github.io/data/bicycleHiresLondon.csv"
```
