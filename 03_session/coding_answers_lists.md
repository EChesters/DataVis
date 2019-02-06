---
id: litvis
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

### A function which adds 10 to each list item

```elm {l}
addsTen : List Int -> List Int
addsTen myList =
  let
    inc num =
      num + 10
  in
  List.map inc myList
```

```elm {l=hidden, raw}
listOne : List Int
listOne =
  addsTen [1, 2, 3, 4, 5]
```

### A function to double each list item

```elm {l}
doubleItem : List Int -> List Int
doubleItem mySecondList =
  let
    double num =
      num * 2
  in
  List.map double mySecondList
```

```elm{l=hidden, raw}
listTwo : List Int
listTwo =
  doubleItem [5, 6, 7, 8, 9, 10]
```

### A function to find sum of squares of all list items

```elm {l}
sumSquares : List Float -> Float
sumSquares thisList =
  let
    mySqrt num =
      sqrt num
  in
  List.sum (List.map mySqrt thisList)
```

```elm {l=hidden, raw}
results : Float
results =
  sumSquares [9, 25, 16, 36]
```

### A function to concatenate potato to each list item

```elm {l}
concatenatePotato : List String -> List String
concatenatePotato myStrList =
  let
    concatPotato str =
      str ++ " potato"
  in List.map concatPotato myStrList
```

```elm {l=hidden, raw}
myPotatoes : List String
myPotatoes =
  concatenatePotato ["I am a", "I like a", "sweet"]
```
