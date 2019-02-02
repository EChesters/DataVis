---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml
---

@import "../css/datavis.less"

<!-- Everything above this line should probably be left untouched. -->

# Coding Gym 1

## Induction

These exercises are designed to help you build confidence programming with Elm. They assume no prior experience of coding in any language and comprise simple tasks that keep up your familiarity with the process of coding. In many cases they involve repetition of limited coding tasks, but just like repeats in a gym, the more you do, the easier it gets.

The exercises will not focus on visualization (for that, see the main lecture notes), but instead concentrate on basic principles of coding in a functional language.

If you are viewing this document in Atom with litvis you can simply add your own code blocks to this document. If not, open up the Atom editor, create a new blank file (`File->New File` or the keyboard shortcut `Ctrl/Cmd-N`) and immediately save it as `gymExercises.md` in your `session01` folder. Make sure you have the litvis preview window open (`Packages->Markdown Preview Enhanced with litvis->Toggle` or the keyboard shortcut `Shift-Ctrl-M`). You editor should now look similar to this:

![blank litvis document in Atom](https://staff.city.ac.uk/~jwo/datavis2019/session01/images/blankLitvis.png)

## Limbering Up: Placing Elm code blocks in a litvis document

- Create an empty Elm _code block_ in your document by using a pair of triple backticks as follows:

````
```elm {l raw}


```
````

The `l` bit of the codeblock header means display the literate code in the preview window. The `raw` bit means that value of any functions you create in the bock will also be displayed directly.

## 1. Simple functions

Our first coding exercises will involve creating some simple functions that return a value of some kind. Here are three examples. You can give a function any name you like as long as it starts with a lowercase letter. The first line of a function definition is known as its _type signature_ and reports what kind of value it will generate. In the examples below, the `myName` generates a `String` (some text), `myYearOfBirth` generates an integer (whole number) and `myFavouriteNumber` generates a floating point number (a number with a decimal point).

Below the type signature you name the function again followed by `=` and then on a new line indented should be the code that does the work of generating a value to return.

```elm {l siding}
myName : String
myName =
    "Ada Lovelace"


myYearOfBirth : Int
myYearOfBirth =
    1815


myFavouriteNumber : Float
myFavouriteNumber =
    2.71828
```

{(task|}

Create separate functions that display the following, each in their own `raw` code block. If created successfully, you should see the result of each function appear in the preview window.

- your own first name
- your own last name
- your own year of birth
- The number [π](https://www.piday.org/million/) rounded to six decimal places (you can just provide the number to 6 d.p. rather than relying on any _round_ function)
- A quote about data visualization
- The number of legs of a titmouse.

{|task)}

## 2. Simple functions with operators

As well as assigning numbers or strings directly to a function, you can also get the code to perform calculations with _operators_. For example, to add two numbers together you might have a function:

```elm {l siding}
daysWorking : Int
daysWorking =
    5 + 6
```

Other operators you might use include `-` (subtraction), `*` (multiplication), `//` (integer division), `/` (floating point division), `^` (exponentiation, such as `4^2` for four-squared) and `++` for concatenating (joining) strings. These operators can be applied directly to numbers (or strings) or to functions that generate numbers (or strings). If you need to control the order in which operators apply, you can use brackets. For example `(2*3) + 4` is 10, not 14.

{(task|}

Try these exercises by creating functions to:

- The sum of the integers 1 to 5
- 22 divided by 7 to at least 12 decimal places
- calculate the number of seconds in a leap-year
- Your _James Bond name_ (i.e. in the form "Bond, James Bond")
- The mean (average) of the numbers 1 to 5

{|task)}
