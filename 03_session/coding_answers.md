---
id: litvis
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

### Function to multiple together 2 numbers as parameters

```elm {l}
multiply : Int -> Int -> Int
multiply firstNum secondNum =
  firstNum * secondNum
```

```elm {l, raw}
result : Int
result =
  multiply 5 3
```

### Function to create list of ints from 1 to a parameter

<!-- ```elm {l}
listOfInts : Int -> Int
listOfInts =
-- needs a loop
``` -->

### Function to print favourite colour

```elm {l}
favouriteColour : String -> String
favouriteColour colour =
  "My favourite colour is " ++ colour
```

```elm {l, raw}
myColour : String
myColour =
  favouriteColour "cyan"
```
