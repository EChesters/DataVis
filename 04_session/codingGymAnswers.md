---
id: litvis
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

### Task 1 - Pipe Function 1

```elm {l}
q1 : Float
q1 =
  sqrt (2+2)
```

```elm {l}
a1 : Float
a1 =
  2 + 2 |> sqrt
```

```elm {l=hidden, raw}
result : Float
result =
  a1
```

#### Pipe Function 2

```elm {l, raw}
a2 : List String
a2 =
  "Father Charles goes down and ends battle" |> String.toUpper |> String.words |> List.reverse
```

#### Pipe Function 3

```elm {l, raw}
a3 : List Float
a3 =
  List.range 1 10 |> List.map toFloat
```

#### Pipe Function 4

```elm {l, raw}
a4 : Float
a4 =
  List.range 1 10 |> List.map toFloat |> List.map (\x -> x/2) |> List.sum
```

#### Pipe Function 5

```elm {l, raw}
a5 : Int -> Int -> Int
a5 minVal maxVal =
  List.range minVal maxVal |> List.map (\cube -> cube * cube * cube) |> List.sum

result5 : Int
result5 =
  a5 10 15
```

---
