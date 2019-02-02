---
id: litvis
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

### Task 1

```elm {l=hidden raw}
myFirstName : String
myFirstName =
  "Elizabeth"
```

```elm {l=hidden raw}
myLastName : String
myLastName =
  "Chesters"
```

```elm {l=hidden raw}
myYear : Int
myYear =
  1992
```

```elm{l=hidden raw}
myPi : Float
myPi =
  pi
```

---

### Task 2

```elm {l=hidden raw}
mySum : Int
mySum =
  1+2+3+4+5
```

```elm {l=hidden raw}
myTotal : Float
myTotal =
  22/7
```

```elm{l=hidden raw}
leapYearSeconds : Int
leapYearSeconds =
  60 * 60 * 24 * 366
```

```elm {l=hidden raw}
bondName : String
bondName =
  myLastName ++ ", " ++ myFirstName ++ " " ++ myLastName
```

```elm {l=hidden raw}
average : Int
average =
  (1+2+3+4+5) // 5
```
