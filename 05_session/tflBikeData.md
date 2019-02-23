---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

## Damaged Public Hire Bicycles from a Transport for London Data Feed

TfL provides open access to a range of data sources related to transport in London. These are accessible via an [API](https://tfl.gov.uk/info-for/open-data-users/unified-api) that can respond to data requests by generating JSON that wrap the returned data. While the data are [free to use and distribute](https://tfl.gov.uk/corporate/terms-and-conditions/transport-data-service), access requires you to register to receive an _app_id_ and _app_key_ in order to make requests. In the example I have already retrieved the JSON data using my API key, but you should be able to replace with your own data if you register with TfL

### Bike hire data

Suppose we wished to show the numbers and locations of broken (Santander) public hire bicycles across London. The TfL [bikePoint feed](https://api.tfl.gov.uk/swagger/ui/index.html?url=/swagger/docs/v1#!/BikePoint/BikePoint_GetAll) provides live data on the numbers of bikes docked at each more than 780 bike stations around London. The feed also contains data on the name and location of each station as well as its capacity and number of free spaces currently available. We can calculate the number of broken bikes as `numDocks - (numEmptyDocks + numBikes)` (the data on the number of bikes at a station records only those available for hire, so any bikes not included in this figure are broken ones).

Here is an example JSON feed for a single station:

```javascript
{
    "$type": "Tfl.Api.Presentation.Entities.Place, Tfl.Api.Presentation.Entities",
    "id": "BikePoints_1",
    "url": "/Place/BikePoints_1",
    "commonName": "River Street , Clerkenwell",
    "placeType": "BikePoint",
    "additionalProperties": [
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "TerminalName",
        "sourceSystemKey": "BikePoints",
        "value": "001023",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "Installed",
        "sourceSystemKey": "BikePoints",
        "value": "true",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "Locked",
        "sourceSystemKey": "BikePoints",
        "value": "false",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "InstallDate",
        "sourceSystemKey": "BikePoints",
        "value": "1278947280000",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "RemovalDate",
        "sourceSystemKey": "BikePoints",
        "value": "",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "Temporary",
        "sourceSystemKey": "BikePoints",
        "value": "false",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "NbBikes",
        "sourceSystemKey": "BikePoints",
        "value": "7",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "NbEmptyDocks",
        "sourceSystemKey": "BikePoints",
        "value": "11",
        "modified": "2019-02-15T15:01:29.337Z"
      },
      {
        "$type": "Tfl.Api.Presentation.Entities.AdditionalProperties, Tfl.Api.Presentation.Entities",
        "category": "Description",
        "key": "NbDocks",
        "sourceSystemKey": "BikePoints",
        "value": "19",
        "modified": "2019-02-15T15:01:29.337Z"
      }
    ],
    "children": [],
    "childrenUrls": [],
    "lat": 51.529163,
    "lon": -0.10997
  }
```

#### Step 1: Retrieve docking station information

There's plenty of information in the JSON feed we do not need, and the [Tidy]() package will allow us just to extract the data items we need. As a first step we can create a tidy table of just the location and name of each station, the sources of which are all in the 'root' of each station record.

```elm {l}
stationTable : Table
stationTable =
    empty
        |> insertColumnFromJson "commonName" [] sampleJson
        |> insertColumnFromJson "lon" [] sampleJson
        |> insertColumnFromJson "lat" [] sampleJson
```

```elm {m}
stationsDisplay : List String
stationsDisplay =
    tableSummary 4 stationTable
```

#### Step 2: Retrieve the number of bikes, spaces and capacity of each station.

Data on the status of each station including the number of bikes and spaces are stored inside the `additionalProperties` object in the JSON file. Matters are complicated a little because the three values are stored in separate objects each of which have a `key` and `value` field. We can retrieve these by extracting all key-value pairs for each station and filtering just the values for a given key type. Here we create three separate tables for those three data values:

```elm {l}
bikesTable : Table
bikesTable =
    empty
        |> insertColumnFromJson "key" [ "additionalProperties" ] sampleJson
        |> insertColumnFromJson "value" [ "additionalProperties" ] sampleJson
        |> filterRows "key" (\k -> k == "NbBikes")


spacesTable : Table
spacesTable =
    empty
        |> insertColumnFromJson "key" [ "additionalProperties" ] sampleJson
        |> insertColumnFromJson "value" [ "additionalProperties" ] sampleJson
        |> filterRows "key" (\k -> k == "NbEmptyDocks")


capacityTable : Table
capacityTable =
    empty
        |> insertColumnFromJson "key" [ "additionalProperties" ] sampleJson
        |> insertColumnFromJson "value" [ "additionalProperties" ] sampleJson
        |> filterRows "key" (\k -> k == "NbDocks")
```

```elm {m}
capacityDisplay : List String
capacityDisplay =
    tableSummary 4 capacityTable


spacesDisplay : List String
spacesDisplay =
    tableSummary 4 spacesTable


bikesDisplay : List String
bikesDisplay =
    tableSummary 4 bikesTable
```

#### Step 3: Calculating number of damaged bikes

We can create an additional column of data containing the number of damaged bikes by calculating the difference between the capacity and the total of number of bikes and spaces. This can be achieved by extracting the numerical values and applying a [List.map3](https://package.elm-lang.org/packages/elm/core/latest/List#map3) function to the three lists:

```elm {l}
damagedColumn : List String
damagedColumn =
    List.map3 (\capacity spaces bikes -> capacity - (spaces + bikes))
        (numColumn "value" capacityTable)
        (numColumn "value" spacesTable)
        (numColumn "value" bikesTable)
        |> List.map String.fromFloat
```

#### Step 4: Combining tables

Finally, we can combine the data from the separate tables and columns into a single table:

```elm {l}
combinedTable : Table
combinedTable =
    stationTable
        |> insertColumn "capacity" (strColumn "value" capacityTable)
        |> insertColumn "numSpaces" (strColumn "value" spacesTable)
        |> insertColumn "numBikes" (strColumn "value" bikesTable)
        |> insertColumn "damaged" damagedColumn
```

```elm {m}
showTable : List String
showTable =
    tableSummary 4 combinedTable
```

```elm {l=hidden}
sampleJson : String
sampleJson =
```