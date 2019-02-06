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
