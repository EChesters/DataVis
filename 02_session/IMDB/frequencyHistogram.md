---
follows: imdbRoot
---

## A Frequency histogram

This Frequency histogram has changed from the original, by removing a lot of "chart junk" (Tufte, 1983). All axis information, the stroke around the histogram have been removed, to make the chart easier to read. The colours are more distingiushable, by using more colours than blue.
Whilst this design is "intriguing and curiosity-provoking" (ibid), this is arguably because the lack of information leaves the viewer wondering what on earth this data is showing them. I think because the colours are more distinguished than the shades of blue - similar to the gradient/colour contrast example last lecture - it’s easier to see where similar data is on each bar

So, if I’m interested in the purple information, I can easily see that across all the bars, rather than trying to see if the shades of blue match when they’re only slightly distinguished from the other shades of blue around them

```elm {l=hidden v}
histogram : Spec
histogram =
  let
    cfg =
      configure
        << configuration (coView [vicoStroke Nothing ]) -- removes chart outline

    enc =
      encoding
        << position X [ pName "IMDB_Rating",
                        pMType Nominal, -- uses more distinguishable colours
                        pBin [],
                        pAxis [] -- removes all X Axis info
                      ]
        << position Y [ pMType Quantitative,
                        pAggregate opCount,
                        pAxis [] -- removes all Y Axis info
                      ]
        << color [ mName "IMDB_Rating",
                   mMType Nominal, -- uses more distingiushable colours
                   mLegend [] -- removes legend
                 ]
  in
  toVegaLite [ cfg [], data [], bar [], enc [] ]
```
