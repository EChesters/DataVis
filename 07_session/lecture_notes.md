---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import VegaLite exposing (..)
```

<!-- Everything above this line should probably be left untouched. -->

# Session 7: Layout in Data Visualization

## Table of Contents

1. [Introduction](#1-introduction)
2. [Superposition](#2-superposition)
3. [Juxtaposition](#3-juxtaposition)
4. [Conclusions](#4-conclusions)
5. [Recommending Reading](#5-recommended-reading)
6. [Practical Exercises](#6-practical-exercises)

{(aim|}

This session is designed to show how graphical layout and positioning play an important part in the design of effective data visualization.
It considers how position can be controlled not just within a single chart, but in the arrangement of charts to form more complex assemblages of data views.

By the end of this session you should be able to:

- recognise the difference between _juxtaposition_, _superposition_ and _direct encoding_ to support visual comparison.
- make arrangement design choices that best support the comparison tasks of your data visualization
- use _layering_ in Vega-Lite/Litvis to combine views in the same space, including for annotation
- use _repeating_ in Vega-Lite/Litvis to juxtapose encodings of different data fields
- use _faceting_ in Vega-Lite/Litvis to split a view of data values into juxtaposed sub-views.
- use _concatenation_ to assemble different encodings into dashboard-like assemblages of data views.

{|aim)}

---

## 1. Introduction

{(infobox|}

Note: This document uses the latest versions of elm-vegalite and litvis, which were released on the 10th March 2019. To ensure you have the latest versions of both running:

- Close atom and then restart it.
- You should see a message in the bottom-right of Atom indicating one or more updates are availble:
  ![update available](https://staff.city.ac.uk/~jwo/datavis2019/images/updateAvailable.png)
  Click on this link and update all (this may take a couple of minutes).
- Close atom once the updates are complete
- Delete the litvis cache at `.mume/literate-elm` in your home directory
- Restart Atom

{|infobox)}

Right from the start of this module, we recognised that one of the main objectives of most data visualizations is to support the act of _comparison_, of being able to judge how similar or contrasting are different items of data. We also recognised, in Session 4, that _position_ is one of the most effective and expressive of the visual variables in being able to convey the properties to support comparison. This session considers how we can use position in the form of chart _layout_, or as Munzner (2015) calls it, _arrangement_, to improve the way visualization supports data comparison.

As an example, consider the following abstract data visualization that uses arrangement of shapes to afford certain types of comparison:

![Arranged symbols](https://staff.city.ac.uk/~jwo/datavis2019/session07/images/symbolsArranged.png)

{(task|}

Despite no context provided, what kinds of comparison can be made here? What do you think is being represented? What alternative arrangements could be used?

- could be showing perspective
- suggests ongoing data, that ther is more and the fact that it's cut off at the bottom
- uses the entire block
- these objects are buildings and when moved is a map of London
- showing building in this way may highlight patterns in building shapes
- actually shows building by size in order (not perspective)

We will discuss this further in class on Wednesday and reveal the data behind the visualization.

{|task)}

[Gleicher et al (2011)](#5-recommended-reading) considered the design choices available when supporting comparison in data visualization. They proposed a taxonomy of comparison approaches, identifying three main strategies:

- **Juxtaposition** affords comparison by placing representations next to each other (spatial juxtaposition) or as an animation in time sequence (temporal juxtaposition). It relies on our ability to mentally map parts of one image onto another in order to compare them. The example below shows juxtaposition of numbers of cycle hires per month and average length of hires per month for the London Cycle Hire Scheme.

```elm {v}
juxtapositionExample : Spec
juxtapositionExample =
    let
        enc =
            encoding
                << position X [ pName "Month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y [ pRepeat arFlow, pMType Quantitative ]
    in
    toVegaLite
        [ cycleHireData []
        , repeatFlow [ "NumberOfHires", "AvHireTime" ]
        , specification (asSpec [ width 275, enc [], line [] ])
        ]
```

- **Superposition** arranges representations "on top" of each other, allowing a direct visual comparison within the same graphical space. Here we superimpose the number of hires and average hire time using a 'dual-axis' line chart:

```elm {v}
superpositionExample : Spec
superpositionExample =
    let
        hireTimeColour =
            "rgb(160,80,50)"

        numHiresColour =
            "rgb(82,120,160)"

        enc1 =
            encoding
                << position X [ pName "Month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y
                    [ pName "NumberOfHires"
                    , pMType Quantitative
                    , pAxis [ axLabelColor numHiresColour, axTitleColor numHiresColour, axGrid False ]
                    ]

        spec1 =
            asSpec [ enc1 [], line [ maColor numHiresColour ] ]

        enc2 =
            encoding
                << position X [ pName "Month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y
                    [ pName "AvHireTime"
                    , pMType Quantitative
                    , pAxis [ axLabelColor hireTimeColour, axTitleColor hireTimeColour, axGrid False ]
                    ]

        spec2 =
            asSpec [ enc2 [], line [ maColor hireTimeColour ] ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite [ width 605, cycleHireData [], res [], layer [ spec1, spec2 ] ]
```

- **Explicit encoding** measures difference between data items and then symbolises those differences in some way. For example, when you considered visualizing global temperature anomalies in Session 3, the difference between each year's average temperature and a baseline temperature was provided as an 'anomaly' data field which you then visualized with bars, lines etc. The magnitude of those bars, position of line etc. represented the magnitude of those differences explicitly. Below is an example that standardizes number of bicycle hires and average hire time and calculates their difference explicitly (via z-scores):

```elm {v}
explicitEncodingExample : Spec
explicitEncodingExample =
    let
        trans =
            transform
                << joinAggregate [ opAs opMean "NumberOfHires" "avHires" ] []
                << joinAggregate [ opAs opStdev "NumberOfHires" "sdHires" ] []
                << calculateAs "(datum.NumberOfHires - datum.avHires) / datum.sdHires" "zHires"
                << joinAggregate [ opAs opMean "AvHireTime" "avHireTime" ] []
                << joinAggregate [ opAs opStdev "AvHireTime" "sdHireTime" ] []
                << calculateAs "(datum.AvHireTime - datum.avHireTime) / datum.sdHireTime" "zHireTime"
                << calculateAs "datum.zHires - datum.zHireTime" "zDiff"

        enc =
            encoding
                << position X [ pName "Month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y [ pName "zDiff", pMType Quantitative, pAxis [ axTitle "NumHires - HireTime" ] ]
    in
    toVegaLite [ width 640, cycleHireData [], trans [], enc [], bar [] ]
```

{(task|}What are the relative advantages and disadvantages of each of the three approaches to supporting comparison? Which works best in the case of the cycle hire data? Why?{|task)}

## 2. Superposition

Let's consider the Vega-Lite mechanism for superposing multiple views in the same space. This is one of several ways of specifying a _view composition_ [Satyanarayan et al (2017)](#5-recommended-reading). Using the bicycle hire data as an example, remember how we might specify a line chart of monthly hires:

```elm {l}
cycleHireData =
    dataFromUrl "https://gicentre.github.io/data/bicycleHiresLondon.json"
```

```elm {l v}
numHires : Spec
numHires =
    let
        enc =
            encoding
                << position X [ pName "Month", pMType Temporal ]
                << position Y [ pName "NumberOfHires", pMType Quantitative ]
    in
    toVegaLite [ width 600, cycleHireData [], enc [], line [] ]
```

Similarly, we might specify the average monthly hire time as follows:

```elm {l v}
hireTime : Spec
hireTime =
    let
        enc =
            encoding
                << position X [ pName "Month", pMType Temporal ]
                << position Y [ pName "AvHireTime", pMType Quantitative ]
    in
    toVegaLite [ width 600, cycleHireData [], enc [], line [] ]
```

To combine both views in the same space we do two things: Firstly, instead of sending each specification to Vega-Lite with [toVegaLite](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#toVegaLite), we reference them in their own functions with [asSpec](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#asSpec). Secondly we combine the two referenced specifications with [layer](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#layer) and send the list of layers to Vega-Lite.

This is how it might look:

```elm {l v highlight=[9,10,17,18,20]}
layersExample1 : Spec
layersExample1 =
    let
        encHires =
            encoding
                << position X [ pName "Month", pMType Temporal ]
                << position Y [ pName "NumberOfHires", pMType Quantitative ]

        specHires =
            asSpec [ width 600, cycleHireData [], encHires [], line [] ]

        encTimes =
            encoding
                << position X [ pName "Month", pMType Temporal ]
                << position Y [ pName "AvHireTime", pMType Quantitative ]

        specTime =
            asSpec [ width 600, cycleHireData [], encTimes [], line [] ]
    in
    toVegaLite [ layer [ specHires, specTime ] ]
```

Note how the combined visualization appears to show only one line though. This is because monthly cycle numbers range up to 1.2 million, but the average hire times range up to only 26 (minutes). By default, when layers are combined, they use the same scales so those values of 26 appear along the baseline when the scale also accommodates numbers up to 1.2m. If we want to use two independent scales on the y axis, we have to indicate that explicitly in our specification with [resolve](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#resolve). We can also make the specification a little more efficient by removing those elements common to both layers and placing them directly in the `toVegaLite` list:

```elm {l v highlight=[20-22,25-27]}
layersExample2 : Spec
layersExample2 =
    let
        encHires =
            encoding
                << position X [ pName "Month", pMType Temporal ]
                << position Y [ pName "NumberOfHires", pMType Quantitative ]

        specHires =
            asSpec [ encHires [], line [] ]

        encTimes =
            encoding
                << position X [ pName "Month", pMType Temporal ]
                << position Y [ pName "AvHireTime", pMType Quantitative ]

        specTime =
            asSpec [ encTimes [], line [] ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite
        [ width 600
        , cycleHireData []
        , res []
        , layer [ specHires, specTime ]
        ]
```

The design has plenty of scope for improvement as it is hard to know which line corresponds to which set of data. And the overlaid axis grid lines are confusing. See the superposition example towards the top of these notes to see how we might tweak the specification to overcome these problems. Nevertheless, [dual axis charts can be problematic](https://blog.datawrapper.de/dualaxis/).

More common and useful applications of superposition are when the multiple layers share a common set of positional scales. For example, when providing a text-based _annotation layer_ to help contextualise patterns in data.

```elm {v l highlight=14-30}
annotationExample : Spec
annotationExample =
    let
        -- Data layer
        enc =
            encoding
                << position X [ pName "Month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y [ pName "AvHireTime", pMType Quantitative, pAxis [ axGrid False ] ]

        spec =
            asSpec [ cycleHireData [], enc [], line [] ]

        -- Annotation layer
        annotationData =
            dataFromRows []
                << dataRow
                    [ ( "date", str "2011-04-29" )
                    , ( "annotation", str "Easter / royal wedding" )
                    , ( "hireTime", num 27 )
                    ]
                << dataRow
                    [ ( "date", str "2012-08-01" )
                    , ( "annotation", str "London Olympics" )
                    , ( "hireTime", num 23 )
                    ]
                << dataRow
                    [ ( "date", str "2018-02-22" )
                    , ( "annotation", str "Beast from the East" )
                    , ( "hireTime", num 13 )
                    ]

        encAnnotation =
            encoding
                << position X [ pName "date", pMType Temporal ]
                << position Y [ pName "hireTime", pMType Quantitative ]
                << text [ tName "annotation", tMType Nominal ]

        specAnnotation =
            asSpec [ annotationData [], encAnnotation [], textMark [ maDy -5 ] ]
-- maDy = once encoding data pos move it by 5 px up
        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])
    in
    toVegaLite
        [ width 600, cfg [], layer [ spec, specAnnotation ] ]
```

Here we create an additional small dataset containing the annotations we wish to add. The position of annotation is conveniently determined in 'data space' rather than 'graphics space', so we supply a date to position the annotation on the x axis and a hire time to position it on the y axis. For clarity, we add these annotations row at a time rather than defining columns, as each row corresponds to a single annotation.

We can apply the same principle to more complex arrangements of overlay, creating multiple layers each with their own graphical or textual annotation content. Here for example is a styled depiction of Leonardo DiCaprio's age and that of his various partners over the last 20 years. The design is based on [this example](https://www.reddit.com/r/dataisbeautiful/comments/azjti7/leonardo_dicaprio_refuses_to_date_a_woman_over_25/) combined with this ['model' from xkcd](https://xkcd.com/314/).

```elm {v}
diCaprio : Spec
diCaprio =
    let
        minAge age =
            toFloat age / 2 + 7

        maxAge age =
            toFloat ((age - 7) * 2)

        textColour =
            "rgb(143,154,174)"

        dcColour =
            "rgb(223,117,45)"

        partnerColour =
            "rgb(91,198,214)"

        dcData =
            dataFromColumns []
                << dataColumn "year" (List.range 1999 2019 |> List.map toFloat |> nums)
                << dataColumn "dcAge" (List.range 24 44 |> List.map toFloat |> nums)
                << dataColumn "minAge" (List.range 24 44 |> List.map minAge |> nums)
                << dataColumn "maxAge" (List.range 24 44 |> List.map maxAge |> nums)
                << dataColumn "partnerAge" ([ 18, 19, 20, 21, 22, 23, 20, 21, 22, 23, 24, 25, 23, 22, 20, 21, 25, 24, 25, 20, 21 ] |> nums)

        annotationData =
            dataFromRows []
                << dataRow
                    [ ( "name", str "Gisele Bundchen" )
                    , ( "start", num 1999 )
                    , ( "end", num 2004 )
                    ]
                << dataRow
                    [ ( "name", str "Bar Refaeli" )
                    , ( "start", num 2005 )
                    , ( "end", num 2010 )
                    ]
                << dataRow
                    [ ( "name", str "Blake Lively" )
                    , ( "start", num 2011 )
                    , ( "end", num 2011 )
                    ]
                << dataRow
                    [ ( "name", str "Erin Heatherton" )
                    , ( "start", num 2012 )
                    , ( "end", num 2012 )
                    ]
                << dataRow
                    [ ( "name", str "Toni Garrn" )
                    , ( "start", num 2013 )
                    , ( "end", num 2014 )
                    ]
                << dataRow
                    [ ( "name", str "Kelly Rohrbach" )
                    , ( "start", num 2015 )
                    , ( "end", num 2015 )
                    ]
                << dataRow
                    [ ( "name", str "Nina Agdal" )
                    , ( "start", num 2016 )
                    , ( "end", num 2017 )
                    ]
                << dataRow
                    [ ( "name", str "Camilla Morrone" )
                    , ( "start", num 2018 )
                    , ( "end", num 2019 )
                    ]

        dcAnnotationData =
            dataFromRows []
                << dataRow
                    [ ( "dcX", num 2019 )
                    , ( "dcY", num 44 )
                    , ( "dcAnnotation", str "Leo's age" )
                    ]
                << dataRow
                    [ ( "dcX", num 2013 )
                    , ( "dcY", num 32 )
                    , ( "dcAnnotation", str "xkcd non-creepiness range" )
                    ]

        -- XKCD Creepiness range
        encBand =
            encoding
                << position X [ pName "year", pMType Ordinal, pAxis [ axTitle "" ] ]
                << position Y
                    [ pName "minAge"
                    , pMType Quantitative
                    , pScale [ scZero False, scDomain (doNums [ 16, 50 ]) ]
                    , pAxis [ axTitle "" ]
                    ]
                << position Y2 [ pName "maxAge", pMType Quantitative ]

        specBand =
            asSpec [ encBand [], area [ maColor dcColour, maClip True, maOpacity 0.4 ] ]

        -- Leo's age
        encDiCaprio =
            encoding
                << position X [ pName "year", pMType Ordinal ]
                << position Y [ pName "dcAge", pMType Quantitative ]

        specDiCaprio =
            asSpec
                [ encDiCaprio []
                , line [ maColor dcColour, maPoint (pmMarker [ maStroke dcColour ]) ]
                ]

        encDiCaprioText =
            encoding
                << position X [ pName "year", pMType Ordinal ]
                << position Y [ pName "dcAge", pMType Quantitative ]
                << text [ tName "dcAge", tMType Quantitative ]

        specDiCaprioText =
            asSpec [ encDiCaprioText [], textMark [ maColor dcColour, maDx -2, maDy -7 ] ]

        encDiCaprioLabel =
            encoding
                << position X [ pName "dcX", pMType Ordinal ]
                << position Y [ pName "dcY", pMType Quantitative ]
                << text [ tName "dcAnnotation", tMType Nominal ]

        specDiCaprioLabel =
            asSpec
                [ dcAnnotationData []
                , encDiCaprioLabel []
                , textMark [ maColor dcColour, maAlign haLeft, maFont "FjallaOne", maDx 10, maDy 5, maSize 14 ]
                ]

        -- Partners' ages
        encPartners =
            encoding
                << position X [ pName "year", pMType Ordinal ]
                << position Y [ pName "partnerAge", pMType Quantitative ]

        specPartners =
            asSpec [ encPartners [], bar [ maColor partnerColour ] ]

        -- Partners' names
        encPartnerText =
            encoding
                << position X [ pName "year", pMType Ordinal ]
                << position Y [ pName "partnerAge", pMType Quantitative ]
                << text [ tName "partnerAge", tMType Quantitative ]

        specPartnerText =
            asSpec [ encPartnerText [], textMark [ maColor partnerColour, maDy -7 ] ]

        encPartnerRange =
            encoding
                << position X [ pName "start", pMType Ordinal ]
                << position X2 [ pName "end", pMType Ordinal ]
                << position Y [ pNum 420 ]

        specPartnerRange =
            asSpec [ annotationData [], encPartnerRange [], rule [ maColor partnerColour ] ]

        encPartnerNames =
            encoding
                << position X [ pName "start", pMType Ordinal ]
                << position Y [ pNum 435 ]
                << text [ tName "name", tMType Nominal ]

        specPartnerNames =
            asSpec
                [ annotationData []
                , encPartnerNames []
                , textMark [ maColor partnerColour, maAlign haLeft, maAngle 30, maFont "FjallaOne" ]
                ]

        cfg =
            configure
                << configuration (coScale [ sacoBandPaddingInner 0.5 ])
                << configuration
                    (coAxis
                        [ axcoGridOpacity 0.1
                        , axcoTicks False
                        , axcoDomain False
                        , axcoLabelColor textColour
                        , axcoLabelAngle 0
                        ]
                    )
                << configuration (coView [ vicoStroke Nothing ])
                << configuration (coPadding (paSize 60))
                << configuration (coBackground "black")
                << configuration (coText [ maColor textColour ])
                << configuration (coTitle [ ticoColor textColour, ticoFont "FjallaOne", ticoFontSize 22, ticoAnchor anStart ])
    in
    toVegaLite
        [ title "Leonardo DiCaprio has never dated a woman over 25"
        , width 650
        , height 400
        , cfg []
        , dcData []
        , layer
            [ specBand
            , specDiCaprio
            , specDiCaprioText
            , specDiCaprioLabel
            , specPartners
            , specPartnerText
            , specPartnerRange
            , specPartnerNames
            ]
        ]
```

{(task|}Without looking at the source code in Atom, how may layers can you detect being used here to create this composite visualization? Once you have made your estimate, check against the source code to see if you are right.{|task)}

For other examples of superposition such as adding summary graphics, annotations and multi-layered visualizations, see 'Labelling and annotation' and 'Other layered charts' in the examples gallery (available for download via Moodle).

## 3. Juxtaposition

As we saw in the first attempt to provide a superposed cycle-hire chart, sometimes overlaying data with different scales can result in charts that are hard to interpret. This becomes even more of a problem as chart complexity increases. The alternative of laying out charts next to each other provides a less cluttered view, and if designed well, one that can be easier to interpret.

_Faceting_ - separating out each data set by their fact (like IA), into its own thing.

- Cons:
- - Can take up much more space.
- - Scales don't work for all facets, can't see numbers of robberies (too zoomed out)
- - Difficult to compare and see clear relationship/differences
- Pros:
- - Gives independence of scaling for each facet

### Faceting

To illustrate the process of juxtaposition, let's consider the crime data we looked at the previous session and firstly, this single stacked bar chart view of the data:

```elm {l=hidden}
data =
    dataFromUrl "https://gicentre.github.io/data/westMidlands/westMidsCrimesAggregated.tsv"


crimeColours =
    categoricalDomainMap
        [ ( "Anti-social behaviour", "rgb(59,118,175)" )
        , ( "Burglary", "rgb(81,157,62)" )
        , ( "Criminal damage and arson", "rgb(141,106,184)" )
        , ( "Drugs", "rgb(239,133,55)" )
        , ( "Robbery", "rgb(132,88,78)" )
        , ( "Vehicle crime", "rgb(213,126,190)" )
        ]
```

```elm {v l}
stackedBars : Spec
stackedBars =
    let
        enc =
            encoding
                << position X [ pName "month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y [ pName "reportedCrimes", pMType Quantitative, pAggregate opSum ]
                << color [ mName "crimeType", mMType Nominal, mScale crimeColours ]
    in
    toVegaLite [ width 500, data [], bar [], enc [] ]
```

While this is a compact representation of the data and allows us to see the gradual decrease in reported crime over time (total height of each column of stacked bars), comparison of the trends in each crime type is harder because only 'vehicle crime' has a fixed baseline. The jump in September 2011, which we might infer is produced by a reclassification of some anti-social behaviour to 'criminal damage and arson', also adds to the difficulty in comparison.

As an alternative we can _facet_ the data by crime type, juxtaposing separate charts for each value in the `crimeType` field. This is achieved easily by encoding `crimeType` with the [column](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#column) channel:

```elm {v l highlight=9}
facetBars : Spec
facetBars =
    let
        enc =
            encoding
                << position X [ pName "month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y [ pName "reportedCrimes", pMType Quantitative, pAggregate opSum ]
                << color [ mName "crimeType", mMType Nominal, mScale crimeColours, mLegend [] ]
                << column [ fName "crimeType", fMType Nominal ]
    in
    toVegaLite [ height 100, width 120, data [], bar [], enc [] ]
```

Placing bars on a common baseline makes comparison of magnitude easier _within_ each sub-chart. And equally, arranging the sub-charts in a row with a common baseline affords bar height comparison _between_ charts. However, comparison of dates is now harder (e.g. do the seasonal fluctuations in burglary align with those of anti-social behaviour?). Perhaps it would be better to arrange the faceted charts in rows instead?

```elm {v l highlight=14}
facetBarsInRows : Spec
facetBarsInRows =
    let
        enc =
            encoding
                << position X [ pName "month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y
                    [ pName "reportedCrimes"
                    , pMType Quantitative
                    , pAggregate opSum
                    , pAxis [ axTitle "" ]
                    ]
                << color [ mName "crimeType", mMType Nominal, mScale crimeColours, mLegend [] ]
                << row [ fName "crimeType", fMType Nominal ]
    in
    toVegaLite [ height 100, width 455, data [], bar [], enc [] ]
```

By default, faceted views like this will share a common scaling, allowing us to assess relative magnitudes shown in each sub-view, but once arranged in rows, that benefit is diminished and space is not used very efficiently. If we want to make better use of the space, but at the cost of losing absolute magnitude comparison, we can make the vertical scales independent, just as we did for the layered bike hire data above:

```elm {v l highlight=16-18}
facetBarsIndScales : Spec
facetBarsIndScales =
    let
        enc =
            encoding
                << position X [ pName "month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y
                    [ pName "reportedCrimes"
                    , pMType Quantitative
                    , pAggregate opSum
                    , pAxis [ axTitle "" ]
                    ]
                << color [ mName "crimeType", mMType Nominal, mScale crimeColours, mLegend [] ]
                << row [ fName "crimeType", fMType Nominal ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite [ height 100, width 455, data [], bar [], enc [], res [] ]
```

Can we have the best of both designs? – accurate comparison of both bar height and bar position across views?
One possible approach might be to use interaction to highlight 'details on demand' (try dragging an interval selection in any sub-chart):

```elm {v l interactive highlight=[9-12,15-17]}
facetBarsInteractive : Spec
facetBarsInteractive =
    let
        enc =
            encoding
                << position X [ pName "month", pMType Temporal, pAxis [ axTitle "" ] ]
                << position Y [ pName "reportedCrimes", pMType Quantitative, pAggregate opSum ]
                << color
                    [ mSelectionCondition (selectionName "barSelection")
                        [ mName "crimeType", mMType Nominal, mScale crimeColours, mLegend [] ]
                        [ mStr "rgb(206,194,186)" ]
                    ]
                << column [ fName "crimeType", fMType Nominal ]

        sel =
            selection
                << select "barSelection" seInterval [ seEncodings [ chX ] ]
    in
    toVegaLite [ height 100, width 120, data [], sel [], bar [], enc [] ]
```

So when faceting a dataset to create a series of juxtaposed small multiples, some design choices need to be made that determine what forms of comparison are easiest to make.

- Should sub-views be arranged in rows or in columns?
- Should sub-views share a common scale or have independent scales?
- Would interaction help with comparison? If so, in what form?

### Juxtaposing different data fields

Sometimes we may wish to juxtapose not different values in a field, but different fields entirely. Consider, for example, visualization of [Anderson's Iris morphology dataset](https://en.wikipedia.org/wiki/Iris_flower_data_set). Like the Titanic data considered in Session 5, this is another of the 'classic' benchmarking datasets. It comprises 50 samples each from thee species of Iris - _Iris virginica_, _Iris versicolor_ and _Iris setosa_. Anderson measured the petal and sepal dimensions as an aid to species identification.

![Three irises](https://staff.city.ac.uk/~jwo/datavis2019/session07/images/irisPhotos.jpg)
Left: _Iris virginica_; centre: _Iris versicolor_; right: _Iris setosa_

The data table recording these samples has the following structure:

| species    | sepalLength | sepalWidth | petalLength | petalWidth |
| :--------- | :---------- | :--------- | :---------- | :--------- |
| setosa     | 5.1         | 3.5        | 1.4         | 0.2        |
| setosa     | 4.9         | 3.0        | 1.4         | 0.2        |
| setosa     | 4.7         | 3.2        | 1.3         | 0.2        |
| :          | :           | :          | :           | :          |
| versicolor | 7.0         | 3.2        | 4.7         | 1.4        |
| versicolor | 6.4         | 3.2        | 4.5         | 1.5        |
| versicolor | 6.9         | 3.1        | 4.9         | 1.5        |
| :          | :           | :          | :           | :          |

Let's first review how we can compare sepal and petal lengths in a scatterplot:

```elm {l}
irisData =
    dataFromUrl "https://vega.github.io/vega-lite/data/iris.json"


speciesColours =
    -- Brewer colourblind-safe categorical colours
    categoricalDomainMap
        [ ( "setosa", "#66c2a5" )
        , ( "versicolor", "#8da0cb" )
        , ( "virginica", "#fc8d62" )
        ]
```

```elm {l v}
singleScatter : Spec
singleScatter =
    let
        enc =
            encoding
                << position X [ pName "sepalLength", pMType Quantitative ]
                << position Y [ pName "petalLength", pMType Quantitative ]
                << color [ mName "species", mMType Nominal, mScale speciesColours ]
    in
    toVegaLite [ irisData [], enc [], circle [] ]
```

**NOTE:** The green setosa information just shows shorter petals, arguably not smaller or narrower.

The clusters of points on the scatterplot represent different morphological relationships of the different species (e.g. _Iris setosa_ has a sepal length only slightly shorter than _Iris versicolor_ but its petals are significantly shorter). The data-driven layout of points in such a scatterplot is essential to our ability to infer such distinctions. The design of a visualization such that items that are close together in the graphic also share some similarity in character is an example of what [Skupin and Fabrikant (2003)](#references) refer to as _cognitive plausibility_. It is an example of using visual variables (location and hue in this case) to emphasise _associative_ relations - i.e. that the graphical clustering is used to indicate that the items in that cluster have some common characteristic.

This is an important characteristic you should build into your designs - are the rules that map graphic character to data character obvious and intuitive ones? If not, consider whether a refinement of your design can increase the cognitive plausibility and therefore decrease the effort required by the reader of your graphic in order to understand the data.

But what of the other relationships, such as between sepal length and sepal width, or petal width and sepal length? Here is where we can use juxtaposition to arrange scatterplots of all pair-wise combinations, by creating a _scatterplot matrix_ (or 'SploM'). Variables are arranged systematically in rows columns, using location not just within plots, but between them, to carry meaning.

```elm {v l highlight=[6,7,10-12,16-21]}
splom : Spec
splom =
    let
        enc =
            encoding
                << position X [ pRepeat arColumn, pMType Quantitative, pAxis [ axGrid False ] ]
                << position Y [ pRepeat arRow, pMType Quantitative, pAxis [ axGrid False ] ]
                << color [ mName "species", mMType Nominal, mScale speciesColours ]

        res =
            resolve
                << resolution (reScale [ ( chX, reShared ), ( chY, reShared ) ])
    in
    toVegaLite
        [ irisData []
        , repeat
            [ rowFields [ "sepalLength", "sepalWidth", "petalLength", "petalWidth" ]
            , columnFields [ "petalWidth", "petalLength", "sepalWidth", "sepalLength" ]
            ]
        , specification (asSpec [ width 150, height 150, circle [], enc [] ])
        , res []
        ]
```

Here, juxtaposition is specified via the [repeat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#repeat) function that allows us to name the fields to appear in each new plot with [rowFields](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#rowFields) and [columnFields](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#columnFields). Those fields are referenced in the encoding not by their name (with [pName](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#pName)) as we would normally expect, but by [pRepeat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#pRepeat), following which is either [arRow](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#arRow) or [arColumn](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#arColumn) depending on whether we wish repeated views to be laid out in rows or columns.

`repeat` allows us to apply the same encoding specification to a list of different data fields. As with superposition of layers, that specification is provided to [asSpec](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#asSpec) but additionally this is stored by passing it to the [specification](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#specification) function. When we overlaid two datasets with very different domain ranges we ensured they were scaled separately by setting the scale of the Y channels to be [reIndependent](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#reIndependent). Here though, we want to do the opposite – we would like the repeated plots to share the same scaling along both axes by setting the scales to be [reShared](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#reShared).

{(task|}What is the effect of changing `reShared` in the example above to `reIndependent`? Which is preferable for supporting comparison and why?{|task)}

One of the advantages of composing juxtaposed views is that we can apply interaction across the entire set of views. For example, as hinted at in the previous session, we can project a selection made in any one view across all linked views:

```elm {v l interactive highlight=[9-11,14-16,19-21]}
splomInteractive : Spec
splomInteractive =
    let
        enc =
            encoding
                << position X [ pRepeat arColumn, pMType Quantitative, pAxis [ axGrid False ] ]
                << position Y [ pRepeat arRow, pMType Quantitative, pAxis [ axGrid False ] ]
                << color
                    [ mSelectionCondition (selectionName "mySelection")
                        [ mName "species", mMType Nominal, mScale speciesColours ]
                        [ mStr "black" ]
                    ]
                << opacity
                    [ mSelectionCondition (selectionName "mySelection")
                        [ mNum 0.9 ]
                        [ mNum 0.1 ]
                    ]

        sel =
            selection
                << select "mySelection" seInterval []

        res = -- makes sure the scale is the same for all graphs
            resolve
                << resolution (reScale [ ( chX, reShared ), ( chY, reShared ) ])
    in
    toVegaLite
        [ irisData []
        , repeat
            [ rowFields [ "sepalLength", "sepalWidth", "petalLength", "petalWidth" ]
            , columnFields [ "petalWidth", "petalLength", "sepalWidth", "sepalLength" ]
            ]
        , specification (asSpec [ width 150, height 150, sel [], circle [], enc [] ])
        , res []
        ]
```

{(task|}Why might you want to link highlighting across views like this? Are there any other forms of cross-view interaction that would be useful?{|task)}

- more than 4 variables means that it's complex to compare and the scaling would be too small to see anything useful
- this solution is not scalable

Notice that in the scatterplot matrix, there is redundancy on either side of the leading diagonal - we have plots of X against Y as well as Y against X, with little advantage in viewing both. A 4x4 matrix occupies a significant amount of space, so this redundancy comes at a cost, one that may prohibit using the approach for many more variables (imagine a 50 by 50 matrix of scatterplots).

An alternative, and more compact representation is the _parallel coordinates plot_. The concept is a simple one - instead of showing the two axes of a scatterplot at right angles to each other, orientate them so they are all parallel. Data values are then represented by joining the positions on two adjacent axes with a line. The scaling applied to each variable (petal length, petal width etc.) are different to ensure the full vertical height of each axis is used. You can think of the plot normalising the range of each variable to sit between 0 (bottom of the plot) and 1 (top).

**NOTE:** each line represents a flower, with its 4 vars. It's clear that there are clear trends per species, but for individual flowers, it's difficult to see, especially sepalLength and sepalWidth.
Arguably less variation in petalLength for setosa than other two species.
Patterns that we assume or see depends on the order of the vars in the graph.
It's easier to compare vars which are next to each other in the graph.

```elm {v l highlight=[9-20,31,34,39,42,50]}
pcp : Spec
pcp =
    let
        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])

        trans =
            transform
                -- Give each sample a unique index
                << window [ ( [ wiAggregateOp opCount ], "index" ) ] []
                -- Gather the four morphology types into a tidy table
                << foldAs [ "petalLength", "petalWidth", "sepalLength", "sepalWidth" ]
                    "morphology"
                    "magnitude"
                -- Find the min and max values for each morphology
                << joinAggregate [ opAs opMin "magnitude" "min", opAs opMax "magnitude" "max" ]
                    [ wiGroupBy [ "morphology" ] ]
                -- Calculate a normalised version of each measurement
                << calculateAs "(datum.magnitude - datum.min) / (datum.max-datum.min)" "normVal"

        encLine =
            encoding
                << position X
                    [ pName "morphology"
                    , pMType Nominal
                    , pAxis [ axLabelAngle 0, axDomain False ]
                    ]
                << position Y [ pName "normVal", pMType Quantitative, pAxis [] ]
                << color [ mName "species", mMType Nominal, mScale speciesColours ]
                << detail [ dName "index", dMType Nominal ]

        specLine =
            asSpec [ encLine [], line [ maOpacity 0.3 ] ]

        encAxis =
            encoding
                << position X [ pName "morphology", pMType Nominal, pAxis [ axTitle "" ] ]
                << detail [ dAggregate opCount, dMType Quantitative ]

        specAxis =
            asSpec [ encAxis [], rule [ maColor "#ccc" ] ]
    in
    toVegaLite
        [ cfg []
        , width 600
        , height 300
        , irisData []
        , trans []
        , layer [ specLine, specAxis ]
        ]
```

Superposed layering is used here rather than juxtaposition with one layer used for the coloured lines representing each Iris sample and one layer for drawing the vertical axis lines. We also see extensive use of transformation functions to shape the data, including some tidy table gathering with [foldAs](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#foldAs). Note also the use of the [detail](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#detail) channel for showing multiple lines (both sample lines and axes) without encoding them with differently.

{(task|}

Using the Iris data parallel coordinate plot and scatterplot matrix for reference,

- what would positively and negatively correlated pairs variables look like on a parallel coordinate plot?
- do crossing lines have any meaning?
- what are the relative advantages and disadvantages of the parallel coordinate plot compared with the scatterplot matrix?

{|task)}

### View composition to build dashboards

So far we have considered juxtaposition as faceting by the values of a single data field, and by substituting different data fields into the same encoding. In both cases all sub-views share the same encoding rules. The most flexible form of view composition comes when we are able to juxtapose views with entirely different encodings. This approach allows us to build 'dashboards' presenting multiple views around a related theme.

Defining what precisely what makes a 'dashboard' is tricky, but we might characterise dashboards as comprising an arrangement of several different aspects of data or views of the same dataset, all linked with a common theme or purpose in mind.

![Dashboard example](https://staff.city.ac.uk/~jwo/datavis2019/session07/images/dashboard.jpg)
_Dashboard example, from Sarikaya et al (2019)_

As Sarikaya et al (2019) point out, some dashboard are used primarily for real-time monitoring purposes, with live updates of content. Others may be for more strategic purposes and incorporate a greater analytical purpose.

What they have in common is that they tend to juxtapose (and often superpose) multiple views within a fixed space. Often those views are _hierarchical_ in nature, where each top-level space in the dashboard is itself made up of collections of juxtaposed views.

In Vega-Lite / Litvis, we can create such hierarchies with [concat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#concat) (for flow layouts), [vConcat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#vVoncat) (for vertical layouts) and [hConcat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#hConcat) (for horizontal layouts) and nest within them further assemblages of juxtaposed or superposed views.

### Composing a Seattle weather dashboard

To illustrate the process of arranging views in a dashboard, we will follow the example described by [the Vega-Lite team at OpenVis 2017](https://www.youtube.com/watch?v=9uaHRWj04D4&feature=youtu.be&t=9m40s), based on visualising weather data for Seattle. It brings together all of the superposition and juxtaposition techniques we have considered, along with some new functions for concatenating views in a single dashboard.

```elm {l}
seattleData =
    dataFromUrl "https://vega.github.io/vega-lite/data/seattle-weather.csv"
```

Let's start with a single bar chart showing monthly precipitation:

```elm {v l siding}
barChart : Spec
barChart =
    let
        enc =
            encoding
                << position X [ pName "date", pMType Ordinal, pTimeUnit month, pAxis [ axTitle "" ] ]
                << position Y [ pName "precipitation", pMType Quantitative, pAggregate opMean ]
    in
    toVegaLite [ seattleData [], bar [], enc [] ]
```

We can generalise this a little within Elm by allowing the data field to be counted (here `precipitation`) to be specified as a parameter along with a customisable width `w` to a function that returns the temporal bar chart specification:

```elm {l}
temporalBarSpec : PositionChannel -> Float -> Spec
temporalBarSpec pField w =
    let
        enc =
            encoding
                << position X [ pName "date", pMType Ordinal, pTimeUnit month, pAxis [ axTitle "" ] ]
                << position Y [ pField, pMType Quantitative, pAggregate opMean ]
    in
    asSpec [ width w, height w, bar [], enc [] ]
```

This can then be passed to `toVegaLite` as its own _layer_:

```elm { v l s}
barChart : Spec
barChart =
    toVegaLite [ seattleData [], layer [ temporalBarSpec (pName "precipitation") 180 ] ]
```

As we saw at the start of this session, we can annotate the chart by placing its specification in a layer and adding another layer with the annotation. In this example we will add a layer showing the average precipitation for the entire period:

```elm {l v siding}
barChart : Spec
barChart =
    let
        dataField =
            pName "precipitation"

        enc =
            encoding
                << position Y [ dataField, pMType Quantitative, pAggregate opMean ]
    in
    toVegaLite
        [ seattleData []
        , layer [ temporalBarSpec dataField 180, asSpec [ enc [], rule [] ] ]
        ]
```

The temporal bar encoding is as it was previously. We add a similar average line specification but only need to encode the y-position as we wish to span the entire chart space with the `rule` mark.
The two specifications are combined as layers with the `layer` function which we add to the list of specifications passed to `toVegaLite` in place of the original bar specification.

Again, it becomes a simple job to refactor this bar chart and average marker so that it will work with any named data field and width:

```elm {l}
temporalAvBarSpec : PositionChannel -> Float -> Spec
temporalAvBarSpec dataField w =
    let
        enc =
            encoding
                << position Y [ dataField, pMType Quantitative, pAggregate opMean ]
    in
    asSpec
        [ layer [ temporalBarSpec dataField w, asSpec [ enc [], rule [] ] ] ]
```

We now have the ability to juxtapose different data fields in, for example, a column using [repeat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#repeat):

```elm {l v siding}
barCharts : Spec
barCharts =
    toVegaLite
        [ seattleData []
        , repeat [ rowFields [ "precipitation", "temp_max", "wind" ] ]
        , specification (temporalAvBarSpec (pRepeat arRow) 150)
        ]
```

We could also choose to create a scatterplot matrix comparing the three weather variables:

```elm {l v  siding}
weatherSplom : Spec
weatherSplom =
    let
        enc =
            encoding
                << position X [ pRepeat arColumn, pMType Quantitative ]
                << position Y [ pRepeat arRow, pMType Quantitative ]
    in
    toVegaLite
        [ seattleData []
        , repeat
            [ rowFields [ "temp_max", "precipitation", "wind" ]
            , columnFields [ "wind", "precipitation", "temp_max" ]
            ]
        , specification (asSpec [ width 120, height 120, point [ maStrokeWidth 0.4 ], enc [] ])
        ]
```

And to use faceting to show distributions of weather types:

```elm {l}
weatherColors : List ScaleProperty
weatherColors =
    categoricalDomainMap
        [ ( "sun", "#e7ba52" )
        , ( "fog", "#c7c7c7" )
        , ( "drizzle", "#aec7ea" )
        , ( "rain", "#1f77b4" )
        , ( "snow", "#9467bd" )
        ]
```

```elm {l v siding}
facetedMultiples : Spec
facetedMultiples =
    let
        enc =
            encoding
                << position X [ pName "temp_max", pMType Quantitative, pBin [], pAxis [ axTitle "" ] ]
                << position Y [ pAggregate opCount, pMType Quantitative ]
                << color [ mName "weather", mMType Nominal, mLegend [], mScale weatherColors ]
                << column [ fName "weather", fMType Nominal ]
    in
    toVegaLite [ width 110, height 110, seattleData [], bar [], enc [] ]
```

To assemble these elements together in a dashboard it is useful to take a step back and think about how the various view composition functions relate to one another in our dashboard design:

![Composition tree](https://staff.city.ac.uk/~jwo/datavis2019/session07/images/compositionTree.png)

The various views are assembled in a single function that we parameterise with the name of the data source containing the weather data. Note the use of [hConcat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#hConcat) and [vConcat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#vConcat) to assemble histograms, the bar charts and the scatterplot matrix:

```elm {l}
dashboard : Data -> Spec
dashboard weatherData =
    let
        scatterEnc =
            encoding
                << position X [ pRepeat arColumn, pMType Quantitative ]
                << position Y [ pRepeat arRow, pMType Quantitative ]

        scatterSpec =
            asSpec [ point [ maStrokeWidth 0.4 ], scatterEnc [] ]

        splomSpec =
            asSpec
                [ repeat
                    [ rowFields [ "temp_max", "precipitation", "wind" ]
                    , columnFields [ "wind", "precipitation", "temp_max" ]
                    ]
                , specification scatterSpec
                ]

        barsSpec =
            asSpec
                [ repeat [ rowFields [ "precipitation", "temp_max", "wind" ] ]
                , specification (temporalAvBarSpec (pRepeat arRow) 150)
                ]

        histoEnc =
            encoding
                << position X
                    [ pName "temp_max"
                    , pMType Quantitative
                    , pBin []
                    , pAxis [ axTitle "Max temp" ]
                    ]
                << position Y [ pAggregate opCount, pMType Quantitative ]
                << color [ mName "weather", mMType Nominal, mLegend [], mScale weatherColors ]
                << column [ fName "weather", fMType Nominal ]

        histoSpec =
            asSpec [ width 120, height 120, bar [], histoEnc [] ]
    in
    toVegaLite
        [ weatherData
        , vConcat [ asSpec [ hConcat [ splomSpec, barsSpec ] ], histoSpec ]
        ]
```

Which allows us to generate the dashboard with a single call to the `dashboard` function providing the data source:

```elm {l v}
seattleDashboard : Spec
seattleDashboard =
    dashboard (seattleData [])
```

This design allows us to create dashboard of other regions quite simply by providing an alternative dataset:

```elm {l v}
nyDashboard : Spec
nyDashboard =
    let
        newYorkData =
            dataFromUrl "https://gicentre.github.io/data/newYork-weather.csv"
    in
    dashboard (newYorkData [])
```

## 4. Conclusions

Of all the visual variables (channels) you have control over as a design of a data visualization, _position_ is probably the most powerful and important. Implicit in most chart designs is the information carrying capacity of position, but as we have seen in this session, the arrangement of charts themselves can carry information. And they do so with relatively little cognitive load when designed well. Cognitively plausible positioning allows us to make quick associative, orderable and quantitative comparisons.

View composition in Vega-Lite and Litvis attempts to approach the layout of multiple charts systematically and provides a range of possible functions to support this. They are summarised below along with their primary characteristics.

| Function                                                                                                                                                                                                                                                                                       | Comparison      | Use                                                                                                                                                                                                                                                                                                                                                            |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [layer](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#layer)                                                                                                                                                                                                     | _superposition_ | Layering two or more single-view specifications on top of each other                                                                                                                                                                                                                                                                                           |
| [row](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#row) / [column](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#column)                                                                                                          | _juxtaposition_ | Create a set of separate views in rows or columns by faceting a single categorical data field.                                                                                                                                                                                                                                                                 |
| [facetFlow](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#facetFlow) / [facet](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#facet)                                                                                                | _juxtaposition_ | Create a set of separate view in rows or columns by faceting categorical data field(s). Has more flexible layout options than faceting with the more compact [row](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#row) or [column](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#column) functions. |
| [repeatFlow](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#repeatFlow) / [repeat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#repeat)                                                                                            | _juxtaposition_ | Arranging plots in rows and/or columns using the same encoding but for different data fields                                                                                                                                                                                                                                                                   |
| [concat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#concat) / [hConcat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#hConcat) / [vConcat](https://package.elm-lang.org/packages/gicentre/elm-vegalite/latest/VegaLite#vConcat) | _juxtaposition_ | Positioning nested views in rows and/or columns, that may include different encoding and different data fields                                                                                                                                                                                                                                                 |

## 5. Recommended Reading

**Gleicher, M., Albers, D., Walker, R., Jusufi, I., Hansen, C. and Roberts, J.** (2011) [Visual comparison for information visualization](http://tinyurl.com/yyhvhdao). _Information Visualization_ 10(4), pp.289-309.

**Kirk, A.** (2015) Chapter 10, _Composition_, pp.293-311 in [Data Visualisation: A Handbook for Data Driven Design](http://tinyurl.com/ybevhqo6), Sage.

**Munzner, T.** (2015) Chapter 7: _Arrange Tables_, pp.144-176 in [Visualization Analysis and Design](http://tinyurl.com/ycqp5cf2), CRC Press

**Sarikaya, A., Correll, M., Bartram, L., Tory, M. and Fisher, D.** (2019) [What do we talk about when we talk about dashboards?](https://research.tableau.com/sites/default/files/DashboardsConspiracy_final.pdf) _IEEE Transactions on Visualization and Computer Graphics_, 25(1) pp. 682-692.

**Satyanarayan, A., Moritz, D., Wongsuphasawat, K. and Heer, J.** (2017) [Vega-Lite: A Grammar of Interactive Graphics](https://files.osf.io/v1/resources/mqzyx/providers/osfstorage/5be5e643d354e900197998bd?action=download&version=1&direct&format=pdf). _IEEE Transactions on Visualization and Computer Graphics,_ 23(1) pp. 341-350.

### References

**Skupin, A. and Fabrikant, S.** (2003) [Spatialization methods: a cartographic research agenda for non-geographic information visualization](http://geog.sdsu.edu/People/Pages/skupin/research/pubs/CaGIS2003.pdf). _Cartography and Geographic Information Science_, 30(2), pp.99-119.

---

## 6. Practical Exercises

{(task|}

From now on, you should be spending your time in the lab classes working on your own coursework data visualization designs. But you may wish to focus this week on thinking about:

- What kinds of comparison task should your visualization be able to support in order to answer your research questions?
- How will you use position within any given chart design to support comparison?
- How might you arrange multiple views via superposition, juxtaposition and direct encoding to support comparison?

{|task)}

---

_Check your progress._

- [ ] I can recognise where others have used _juxtaposition_, _superposition_ and _direct encoding_ to enable comparison in their data visualization.
- [ ] I can control the arrangement of multiple views in a visualization in order to support comparison.
- [ ] I can create annotations to a chart through _layering_.
- [ ] I can choose between _repeating_, _faceting_ and _concatenating_ views depending on whether views share data fields, data values and data encodings.
- [ ] I can create dashboards with multiple views of my data.
