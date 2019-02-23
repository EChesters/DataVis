---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml
---

@import "../css/datavis.less"

<!-- Everything above this line should probably be left untouched. -->

# Coding Gym 5

_These exercises are designed to help you build confidence programming with Elm. They assume no prior experience of coding in any language other than that arising from previous weeks of this module. This document is designed to be viewed in Atom with litvis so you can add your own code blocks to answer each question._

### Boolean Operators

Most of the operators and expressions you have created so far in Elm have been to derive either new numbers or new strings from existing numbers and strings (e.g. `3 + 4 * 5` or `"hot" ++ " potatoes"`). We can also create expressions that evaluate to either a Boolean `True` or `False`. For example:

```elm {l}
isDatavis : String -> Bool
isDatavis moduleCode =
    moduleCode == "INM402" || moduleCode == "IN3030"
```

```elm {l r}
test1 : Bool
test1 =
    isDatavis "IN1007"


test2 : Bool
test2 =
    isDatavis "IN3030"
```

Valid Boolean operators include:

| Operator | Description                           | Example                                            |
| -------- | ------------------------------------- | -------------------------------------------------- |
| `==`     | 'Equal to' test                       | `moduleCode == "INM402"`                           |
| `/=`     | 'Not equal to' test                   | `moduleCode /= "INM402"`                           |
| `<`      | 'Less than' test                      | `age < 18`                                         |
| `>`      | 'Greater than' test                   | `record > 0`                                       |
| `<=`     | 'Less than or equal to' test          | `population < 50000`                               |
| `>=`     | 'Greater than or equal to' test       | `temperature >= 30`                                |
| `&&`     | 'AND' operator                        | `temperature > -5 && temperature <= 30`            |
| `||`     | 'OR' operator                         | `moduleCode == "INM402" || moduleCode == |IN3030"` |
| `not`    | Negate (reverse) a Boolean expression | `not (favourite == "cat")`                         |

{(task|}

Using Boolean operators, write functions to do the following. You should also add test functions to check they behave as you expect them to (like `test1` and `test2` above).

- Determine if a number provided as a parameter is positive or not
- Determine if a string provided as a parameter matches the word `password`
- Determine if a number provided as a parameter is even (hint, have a look at [modBy](https://package.elm-lang.org/packages/elm/core/latest/Basics#modBy) to help.)
- Determine if a list of numbers provided as a parameter has at least 4 entries
- Determine if a number is not divisible by 9 and not even.

{|task)}

### Filtering Lists

Functions that take a value as input and return either `True` or `False` like the ones you have created in the previous task are called _predicates_. They can be usefully applied to a list of items, _filtering_ the values where the predicate is `True`. For example:

```elm {l r}
smallNumbers : List Int
smallNumbers =
    List.filter (\n -> n > 0 && n < 10) (List.range -100 100)
```

{(task|}

Filter the following lists using the predicates you created in the previous task:

- Filter only the positive numbers in `List.range -100 15`
- Filter only words that match `password` in the following list `["tiddles","abc123","systemDefault","password","7August1993"]`
- Filter only odd numbers in `List.range 0 50`
- Filter only lists of at least 4 entries from the following list of lists:
  ```elm
  [[1,2], [10,11,21,24], [], [0,0,0,0,0,0], [-4,-4,-4]]
  ```
- Provide a list of all the odd numbers between 1 and 49 that are not divisible by 9

{|task)}
