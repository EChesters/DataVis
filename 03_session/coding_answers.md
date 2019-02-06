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
