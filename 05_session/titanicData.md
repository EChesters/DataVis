---
id: litvis

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

# Titanic Data

The dataset contains 14 fields for each passenger recorded on the Titanic

| column    | description                                                    |
| --------- | -------------------------------------------------------------- |
| pclass    | Ticket class (1st, 2nd, 3rd)                                   |
| survived  | 1=yes, 0=no                                                    |
| name      | Recorded name of passenger                                     |
| sex       | male or female                                                 |
| age       | age of passenger                                               |
| sibsp     | Number of siblings/spouses aboard                              |
| parch     | Number of parents/children aboard                              |
| ticket    | Ticket number                                                  |
| fare      | Passenger fare in £ sterling                                   |
| cabin     | Cabin location                                                 |
| embarked  | Embarkation location: S=Southampton; C=Cherbourg; Q=Queenstown |
| boat      | Lifeboat                                                       |
| body      | Body identification number                                     |
| home.dest | Home or destination location                                   |

The first few rows of the data table:

```elm {m}
showFullTable : List String
showFullTable =
    tableSummary 6 fullTable
```

## Data Shaping

Just select the gender and age of those who drowned, for records where we have the passenger age recorded.

```elm {l}
deathsTable : Table
deathsTable =
    fullTable
        |> filterRows "survived" (\v -> v == "0")
        |> filterRows "age" (\v -> v /= "")
        |> filterColumns (\c -> c == "pclass" || c == "sex" || c == "age")
```

```elm {m}
showDeathsTable : List String
showDeathsTable =
    tableSummary 10 deathsTable
```

<!-- The raw data, imported with Tidy's fromCSV function: -->

```elm {l=hidden}
fullTable : Table
fullTable =
    """pclass,survived,name,sex,age,sibsp,parch,ticket,fare,cabin,embarked,boat,body,home.dest
1,1,"Allen, Miss. Elisabeth Walton",female,29,0,0,24160,211.3375,B5,S,2,,"St Louis, MO"
1,1,"Allison, Master. Hudson Trevor",male,0.9167,1,2,113781,151.55,C22 C26,S,11,,"Montreal, PQ / Chesterville, ON"
1,0,"Allison, Miss. Helen Loraine",female,2,1,2,113781,151.55,C22 C26,S,,,"Montreal, PQ / Chesterville, ON"
1,0,"Allison, Mr. Hudson Joshua Creighton",male,30,1,2,113781,151.55,C22 C26,S,,135,"Montreal, PQ / Chesterville, ON"
1,0,"Allison, Mrs. Hudson J C (Bessie Waldo Daniels)",female,25,1,2,113781,151.55,C22 C26,S,,,"Montreal, PQ / Chesterville, ON"
1,1,"Anderson, Mr. Harry",male,48,0,0,19952,26.55,E12,S,3,,"New York, NY"
1,1,"Andrews, Miss. Kornelia Theodosia",female,63,1,0,13502,77.9583,D7,S,10,,"Hudson, NY"
1,0,"Andrews, Mr. Thomas Jr",male,39,0,0,112050,0,A36,S,,,"Belfast, NI"
1,1,"Appleton, Mrs. Edward Dale (Charlotte Lamson)",female,53,2,0,11769,51.4792,C101,S,D,,"Bayside, Queens, NY"
1,0,"Artagaveytia, Mr. Ramon",male,71,0,0,PC 17609,49.5042,,C,,22,"Montevideo, Uruguay"
1,0,"Astor, Col. John Jacob",male,47,1,0,PC 17757,227.525,C62 C64,C,,124,"New York, NY"
1,1,"Astor, Mrs. John Jacob (Madeleine Talmadge Force)",female,18,1,0,PC 17757,227.525,C62 C64,C,4,,"New York, NY"
1,1,"Aubart, Mme. Leontine Pauline",female,24,0,0,PC 17477,69.3,B35,C,9,,"Paris, France"
1,1,"Barber, Miss. Ellen "'Nellie'",female,26,0,0,19877,78.85,,S,6,,
1,1,"Barkworth, Mr. Algernon Henry Wilson",male,80,0,0,27042,30,A23,S,B,,"Hessle, Yorks"
1,0,"Baumann, Mr. John D",male,,0,0,PC 17318,25.925,,S,,,"New York, NY"
1,0,"Baxter, Mr. Quigg Edmond",male,24,0,1,PC 17558,247.5208,B58 B60,C,,,"Montreal, PQ"
1,1,"Baxter, Mrs. James (Helene DeLaudeniere Chaput)",female,50,0,1,PC 17558,247.5208,B58 B60,C,6,,"Montreal, PQ"
1,1,"Bazzani, Miss. Albina",female,32,0,0,11813,76.2917,D15,C,8,,
1,0,"Beattie, Mr. Thomson",male,36,0,0,13050,75.2417,C6,C,A,,"Winnipeg, MN"
1,1,"Beckwith, Mr. Richard Leonard",male,37,1,1,11751,52.5542,D35,S,5,,"New York, NY"
1,1,"Beckwith, Mrs. Richard Leonard (Sallie Monypeny)",female,47,1,1,11751,52.5542,D35,S,5,,"New York, NY"
1,1,"Behr, Mr. Karl Howell",male,26,0,0,111369,30,C148,C,5,,"New York, NY"
1,1,"Bidois, Miss. Rosalie",female,42,0,0,PC 17757,227.525,,C,4,,
1,1,"Bird, Miss. Ellen",female,29,0,0,PC 17483,221.7792,C97,S,8,,
1,0,"Birnbaum, Mr. Jakob",male,25,0,0,13905,26,,C,,148,"San Francisco, CA"
1,1,"Bishop, Mr. Dickinson H",male,25,1,0,11967,91.0792,B49,C,7,,"Dowagiac, MI"
1,1,"Bishop, Mrs. Dickinson H (Helen Walton)",female,19,1,0,11967,91.0792,B49,C,7,,"Dowagiac, MI"
1,1,"Bissette, Miss. Amelia",female,35,0,0,PC 17760,135.6333,C99,S,8,,
1,1,"Bjornstrom-Steffansson, Mr. Mauritz Hakan",male,28,0,0,110564,26.55,C52,S,D,,"Stockholm, Sweden / Washington, DC"
1,0,"Blackwell, Mr. Stephen Weart",male,45,0,0,113784,35.5,T,S,,,"Trenton, NJ"
1,1,"Blank, Mr. Henry",male,40,0,0,112277,31,A31,C,7,,"Glen Ridge, NJ"
1,1,"Bonnell, Miss. Caroline",female,30,0,0,36928,164.8667,C7,S,8,,"Youngstown, OH"
1,1,"Bonnell, Miss. Elizabeth",female,58,0,0,113783,26.55,C103,S,8,,"Birkdale, England Cleveland, Ohio"
1,0,"Borebank, Mr. John James",male,42,0,0,110489,26.55,D22,S,,,"London / Winnipeg, MB"
1,1,"Bowen, Miss. Grace Scott",female,45,0,0,PC 17608,262.375,,C,4,,"Cooperstown, NY"
1,1,"Bowerman, Miss. Elsie Edith",female,22,0,1,113505,55,E33,S,6,,"St Leonards-on-Sea, England Ohio"
1,1,"Bradley, Mr. George ('George Arthur Brayton')",male,,0,0,111427,26.55,,S,9,,"Los Angeles, CA"
1,0,"Brady, Mr. John Bertram",male,41,0,0,113054,30.5,A21,S,,,"Pomeroy, WA"
1,0,"Brandeis, Mr. Emil",male,48,0,0,PC 17591,50.4958,B10,C,,208,"Omaha, NE"
1,0,"Brewe, Dr. Arthur Jackson",male,,0,0,112379,39.6,,C,,,"Philadelphia, PA"
1,1,"Brown, Mrs. James Joseph (Margaret Tobin)",female,44,0,0,PC 17610,27.7208,B4,C,6,,"Denver, CO"
1,1,"Brown, Mrs. John Murray (Caroline Lane Lamson)",female,59,2,0,11769,51.4792,C101,S,D,,"Belmont, MA"
1,1,"Bucknell, Mrs. William Robert (Emma Eliza Ward)",female,60,0,0,11813,76.2917,D15,C,8,,"Philadelphia, PA"
1,1,"Burns, Miss. Elizabeth Margaret",female,41,0,0,16966,134.5,E40,C,3,,
1,0,"Butt, Major. Archibald Willingham",male,45,0,0,113050,26.55,B38,S,,,"Washington, DC"
1,0,"Cairns, Mr. Alexander",male,,0,0,113798,31,,S,,,
1,1,"Calderhead, Mr. Edward Pennington",male,42,0,0,PC 17476,26.2875,E24,S,5,,"New York, NY"
1,1,"Candee, Mrs. Edward (Helen Churchill Hungerford)",female,53,0,0,PC 17606,27.4458,,C,6,,"Washington, DC"
1,1,"Cardeza, Mr. Thomas Drake Martinez",male,36,0,1,PC 17755,512.3292,B51 B53 B55,C,3,,"Austria-Hungary / Germantown, Philadelphia, PA"
1,1,"Cardeza, Mrs. James Warburton Martinez (Charlotte Wardle Drake)",female,58,0,1,PC 17755,512.3292,B51 B53 B55,C,3,,"Germantown, Philadelphia, PA"
1,0,"Carlsson, Mr. Frans Olof",male,33,0,0,695,5,B51 B53 B55,S,,,"New York, NY"
1,0,"Carrau, Mr. Francisco M",male,28,0,0,113059,47.1,,S,,,"Montevideo, Uruguay"
1,0,"Carrau, Mr. Jose Pedro",male,17,0,0,113059,47.1,,S,,,"Montevideo, Uruguay"
1,1,"Carter, Master. William Thornton II",male,11,1,2,113760,120,B96 B98,S,4,,"Bryn Mawr, PA"
1,1,"Carter, Miss. Lucile Polk",female,14,1,2,113760,120,B96 B98,S,4,,"Bryn Mawr, PA"
1,1,"Carter, Mr. William Ernest",male,36,1,2,113760,120,B96 B98,S,C,,"Bryn Mawr, PA"
1,1,"Carter, Mrs. William Ernest (Lucile Polk)",female,36,1,2,113760,120,B96 B98,S,4,,"Bryn Mawr, PA"
1,0,"Case, Mr. Howard Brown",male,49,0,0,19924,26,,S,,,"Ascot, Berkshire / Rochester, NY"
1,1,"Cassebeer, Mrs. Henry Arthur Jr (Eleanor Genevieve Fosdick)",female,,0,0,17770,27.7208,,C,5,,"New York, NY"
1,0,"Cavendish, Mr. Tyrell William",male,36,1,0,19877,78.85,C46,S,,172,"Little Onn Hall, Staffs"
1,1,"Cavendish, Mrs. Tyrell William (Julia Florence Siegel)",female,76,1,0,19877,78.85,C46,S,6,,"Little Onn Hall, Staffs"
1,0,"Chaffee, Mr. Herbert Fuller",male,46,1,0,W.E.P. 5734,61.175,E31,S,,,"Amenia, ND"
1,1,"Chaffee, Mrs. Herbert Fuller (Carrie Constance Toogood)",female,47,1,0,W.E.P. 5734,61.175,E31,S,4,,"Amenia, ND"
1,1,"Chambers, Mr. Norman Campbell",male,27,1,0,113806,53.1,E8,S,5,,"New York, NY / Ithaca, NY"
1,1,"Chambers, Mrs. Norman Campbell (Bertha Griggs)",female,33,1,0,113806,53.1,E8,S,5,,"New York, NY / Ithaca, NY"
1,1,"Chaudanson, Miss. Victorine",female,36,0,0,PC 17608,262.375,B61,C,4,,
1,1,"Cherry, Miss. Gladys",female,30,0,0,110152,86.5,B77,S,8,,"London, England"
1,1,"Chevre, Mr. Paul Romaine",male,45,0,0,PC 17594,29.7,A9,C,7,,"Paris, France"
1,1,"Chibnall, Mrs. (Edith Martha Bowerman)",female,,0,1,113505,55,E33,S,6,,"St Leonards-on-Sea, England Ohio"
1,0,"Chisholm, Mr. Roderick Robert Crispin",male,,0,0,112051,0,,S,,,"Liverpool, England / Belfast"
1,0,"Clark, Mr. Walter Miller",male,27,1,0,13508,136.7792,C89,C,,,"Los Angeles, CA"
1,1,"Clark, Mrs. Walter Miller (Virginia McDowell)",female,26,1,0,13508,136.7792,C89,C,4,,"Los Angeles, CA"
1,1,"Cleaver, Miss. Alice",female,22,0,0,113781,151.55,,S,11,,
1,0,"Clifford, Mr. George Quincy",male,,0,0,110465,52,A14,S,,,"Stoughton, MA"
1,0,"Colley, Mr. Edward Pomeroy",male,47,0,0,5727,25.5875,E58,S,,,"Victoria, BC"
1,1,"Compton, Miss. Sara Rebecca",female,39,1,1,PC 17756,83.1583,E49,C,14,,"Lakewood, NJ"
1,0,"Compton, Mr. Alexander Taylor Jr",male,37,1,1,PC 17756,83.1583,E52,C,,,"Lakewood, NJ"
1,1,"Compton, Mrs. Alexander Taylor (Mary Eliza Ingersoll)",female,64,0,2,PC 17756,83.1583,E45,C,14,,"Lakewood, NJ"
1,1,"Cornell, Mrs. Robert Clifford (Malvina Helen Lamson)",female,55,2,0,11770,25.7,C101,S,2,,"New York, NY"
1,0,"Crafton, Mr. John Bertram",male,,0,0,113791,26.55,,S,,,"Roachdale, IN"
1,0,"Crosby, Capt. Edward Gifford",male,70,1,1,WE/P 5735,71,B22,S,,269,"Milwaukee, WI"
1,1,"Crosby, Miss. Harriet R",female,36,0,2,WE/P 5735,71,B22,S,7,,"Milwaukee, WI"
1,1,"Crosby, Mrs. Edward Gifford (Catherine Elizabeth Halstead)",female,64,1,1,112901,26.55,B26,S,7,,"Milwaukee, WI"
1,0,"Cumings, Mr. John Bradley",male,39,1,0,PC 17599,71.2833,C85,C,,,"New York, NY"
1,1,"Cumings, Mrs. John Bradley (Florence Briggs Thayer)",female,38,1,0,PC 17599,71.2833,C85,C,4,,"New York, NY"
1,1,"Daly, Mr. Peter Denis ",male,51,0,0,113055,26.55,E17,S,5 9,,"Lima, Peru"
1,1,"Daniel, Mr. Robert Williams",male,27,0,0,113804,30.5,,S,3,,"Philadelphia, PA"
1,1,"Daniels, Miss. Sarah",female,33,0,0,113781,151.55,,S,8,,
1,0,"Davidson, Mr. Thornton",male,31,1,0,F.C. 12750,52,B71,S,,,"Montreal, PQ"
1,1,"Davidson, Mrs. Thornton (Orian Hays)",female,27,1,2,F.C. 12750,52,B71,S,3,,"Montreal, PQ"
1,1,"Dick, Mr. Albert Adrian",male,31,1,0,17474,57,B20,S,3,,"Calgary, AB"
1,1,"Dick, Mrs. Albert Adrian (Vera Gillespie)",female,17,1,0,17474,57,B20,S,3,,"Calgary, AB"
1,1,"Dodge, Dr. Washington",male,53,1,1,33638,81.8583,A34,S,13,,"San Francisco, CA"
1,1,"Dodge, Master. Washington",male,4,0,2,33638,81.8583,A34,S,5,,"San Francisco, CA"
1,1,"Dodge, Mrs. Washington (Ruth Vidaver)",female,54,1,1,33638,81.8583,A34,S,5,,"San Francisco, CA"
1,0,"Douglas, Mr. Walter Donald",male,50,1,0,PC 17761,106.425,C86,C,,62,"Deephaven, MN / Cedar Rapids, IA"
1,1,"Douglas, Mrs. Frederick Charles (Mary Helene Baxter)",female,27,1,1,PC 17558,247.5208,B58 B60,C,6,,"Montreal, PQ"
1,1,"Douglas, Mrs. Walter Donald (Mahala Dutton)",female,48,1,0,PC 17761,106.425,C86,C,2,,"Deephaven, MN / Cedar Rapids, IA"
1,1,"Duff Gordon, Lady. (Lucille Christiana Sutherland) ('Mrs Morgan')",female,48,1,0,11755,39.6,A16,C,1,,London / Paris
1,1,"Duff Gordon, Sir. Cosmo Edmund ('Mr Morgan')",male,49,1,0,PC 17485,56.9292,A20,C,1,,London / Paris
1,0,"Dulles, Mr. William Crothers",male,39,0,0,PC 17580,29.7,A18,C,,133,"Philadelphia, PA"
1,1,"Earnshaw, Mrs. Boulton (Olive Potter)",female,23,0,1,11767,83.1583,C54,C,7,,"Mt Airy, Philadelphia, PA"
1,1,"Endres, Miss. Caroline Louise",female,38,0,0,PC 17757,227.525,C45,C,4,,"New York, NY"
1,1,"Eustis, Miss. Elizabeth Mussey",female,54,1,0,36947,78.2667,D20,C,4,,"Brookline, MA"
1,0,"Evans, Miss. Edith Corse",female,36,0,0,PC 17531,31.6792,A29,C,,,"New York, NY"
1,0,"Farthing, Mr. John",male,,0,0,PC 17483,221.7792,C95,S,,,
1,1,"Flegenheim, Mrs. Alfred (Antoinette)",female,,0,0,PC 17598,31.6833,,S,7,,"New York, NY"
1,1,"Fleming, Miss. Margaret",female,,0,0,17421,110.8833,,C,4,,
1,1,"Flynn, Mr. John Irwin ('Irving')",male,36,0,0,PC 17474,26.3875,E25,S,5,,"Brooklyn, NY"
1,0,"Foreman, Mr. Benjamin Laventall",male,30,0,0,113051,27.75,C111,C,,,"New York, NY"
1,1,"Fortune, Miss. Alice Elizabeth",female,24,3,2,19950,263,C23 C25 C27,S,10,,"Winnipeg, MB"
1,1,"Fortune, Miss. Ethel Flora",female,28,3,2,19950,263,C23 C25 C27,S,10,,"Winnipeg, MB"
1,1,"Fortune, Miss. Mabel Helen",female,23,3,2,19950,263,C23 C25 C27,S,10,,"Winnipeg, MB"
1,0,"Fortune, Mr. Charles Alexander",male,19,3,2,19950,263,C23 C25 C27,S,,,"Winnipeg, MB"
1,0,"Fortune, Mr. Mark",male,64,1,4,19950,263,C23 C25 C27,S,,,"Winnipeg, MB"
1,1,"Fortune, Mrs. Mark (Mary McDougald)",female,60,1,4,19950,263,C23 C25 C27,S,10,,"Winnipeg, MB"
1,1,"Francatelli, Miss. Laura Mabel",female,30,0,0,PC 17485,56.9292,E36,C,1,,
1,0,"Franklin, Mr. Thomas Parham",male,,0,0,113778,26.55,D34,S,,,"Westcliff-on-Sea, Essex"
1,1,"Frauenthal, Dr. Henry William",male,50,2,0,PC 17611,133.65,,S,5,,"New York, NY"
1,1,"Frauenthal, Mr. Isaac Gerald",male,43,1,0,17765,27.7208,D40,C,5,,"New York, NY"
1,1,"Frauenthal, Mrs. Henry William (Clara Heinsheimer)",female,,1,0,PC 17611,133.65,,S,5,,"New York, NY"
1,1,"Frolicher, Miss. Hedwig Margaritha",female,22,0,2,13568,49.5,B39,C,5,,"Zurich, Switzerland"
1,1,"Frolicher-Stehli, Mr. Maxmillian",male,60,1,1,13567,79.2,B41,C,5,,"Zurich, Switzerland"
1,1,"Frolicher-Stehli, Mrs. Maxmillian (Margaretha Emerentia Stehli)",female,48,1,1,13567,79.2,B41,C,5,,"Zurich, Switzerland"
1,0,"Fry, Mr. Richard",male,,0,0,112058,0,B102,S,,,
1,0,"Futrelle, Mr. Jacques Heath",male,37,1,0,113803,53.1,C123,S,,,"Scituate, MA"
1,1,"Futrelle, Mrs. Jacques Heath (Lily May Peel)",female,35,1,0,113803,53.1,C123,S,D,,"Scituate, MA"
1,0,"Gee, Mr. Arthur H",male,47,0,0,111320,38.5,E63,S,,275,"St Anne's-on-Sea, Lancashire"
1,1,"Geiger, Miss. Amalie",female,35,0,0,113503,211.5,C130,C,4,,
1,1,"Gibson, Miss. Dorothy Winifred",female,22,0,1,112378,59.4,,C,7,,"New York, NY"
1,1,"Gibson, Mrs. Leonard (Pauline C Boeson)",female,45,0,1,112378,59.4,,C,7,,"New York, NY"
1,0,"Giglio, Mr. Victor",male,24,0,0,PC 17593,79.2,B86,C,,,
1,1,"Goldenberg, Mr. Samuel L",male,49,1,0,17453,89.1042,C92,C,5,,"Paris, France / New York, NY"
1,1,"Goldenberg, Mrs. Samuel L (Edwiga Grabowska)",female,,1,0,17453,89.1042,C92,C,5,,"Paris, France / New York, NY"
1,0,"Goldschmidt, Mr. George B",male,71,0,0,PC 17754,34.6542,A5,C,,,"New York, NY"
1,1,"Gracie, Col. Archibald IV",male,53,0,0,113780,28.5,C51,C,B,,"Washington, DC"
1,1,"Graham, Miss. Margaret Edith",female,19,0,0,112053,30,B42,S,3,,"Greenwich, CT"
1,0,"Graham, Mr. George Edward",male,38,0,1,PC 17582,153.4625,C91,S,,147,"Winnipeg, MB"
1,1,"Graham, Mrs. William Thompson (Edith Junkins)",female,58,0,1,PC 17582,153.4625,C125,S,3,,"Greenwich, CT"
1,1,"Greenfield, Mr. William Bertram",male,23,0,1,PC 17759,63.3583,D10 D12,C,7,,"New York, NY"
1,1,"Greenfield, Mrs. Leo David (Blanche Strouse)",female,45,0,1,PC 17759,63.3583,D10 D12,C,7,,"New York, NY"
1,0,"Guggenheim, Mr. Benjamin",male,46,0,0,PC 17593,79.2,B82 B84,C,,,"New York, NY"
1,1,"Harder, Mr. George Achilles",male,25,1,0,11765,55.4417,E50,C,5,,"Brooklyn, NY"
1,1,"Harder, Mrs. George Achilles (Dorothy Annan)",female,25,1,0,11765,55.4417,E50,C,5,,"Brooklyn, NY"
1,1,"Harper, Mr. Henry Sleeper",male,48,1,0,PC 17572,76.7292,D33,C,3,,"New York, NY"
1,1,"Harper, Mrs. Henry Sleeper (Myna Haxtun)",female,49,1,0,PC 17572,76.7292,D33,C,3,,"New York, NY"
1,0,"Harrington, Mr. Charles H",male,,0,0,113796,42.4,,S,,,
1,0,"Harris, Mr. Henry Birkhardt",male,45,1,0,36973,83.475,C83,S,,,"New York, NY"
1,1,"Harris, Mrs. Henry Birkhardt (Irene Wallach)",female,35,1,0,36973,83.475,C83,S,D,,"New York, NY"
1,0,"Harrison, Mr. William",male,40,0,0,112059,0,B94,S,,110,
1,1,"Hassab, Mr. Hammad",male,27,0,0,PC 17572,76.7292,D49,C,3,,
1,1,"Hawksford, Mr. Walter James",male,,0,0,16988,30,D45,S,3,,"Kingston, Surrey"
1,1,"Hays, Miss. Margaret Bechstein",female,24,0,0,11767,83.1583,C54,C,7,,"New York, NY"
1,0,"Hays, Mr. Charles Melville",male,55,1,1,12749,93.5,B69,S,,307,"Montreal, PQ"
1,1,"Hays, Mrs. Charles Melville (Clara Jennings Gregg)",female,52,1,1,12749,93.5,B69,S,3,,"Montreal, PQ"
1,0,"Head, Mr. Christopher",male,42,0,0,113038,42.5,B11,S,,,London / Middlesex
1,0,"Hilliard, Mr. Herbert Henry",male,,0,0,17463,51.8625,E46,S,,,"Brighton, MA"
1,0,"Hipkins, Mr. William Edward",male,55,0,0,680,50,C39,S,,,London / Birmingham
1,1,"Hippach, Miss. Jean Gertrude",female,16,0,1,111361,57.9792,B18,C,4,,"Chicago, IL"
1,1,"Hippach, Mrs. Louis Albert (Ida Sophia Fischer)",female,44,0,1,111361,57.9792,B18,C,4,,"Chicago, IL"
1,1,"Hogeboom, Mrs. John C (Anna Andrews)",female,51,1,0,13502,77.9583,D11,S,10,,"Hudson, NY"
1,0,"Holverson, Mr. Alexander Oskar",male,42,1,0,113789,52,,S,,38,"New York, NY"
1,1,"Holverson, Mrs. Alexander Oskar (Mary Aline Towner)",female,35,1,0,113789,52,,S,8,,"New York, NY"
1,1,"Homer, Mr. Harry ('Mr E Haven')",male,35,0,0,111426,26.55,,C,15,,"Indianapolis, IN"
1,1,"Hoyt, Mr. Frederick Maxfield",male,38,1,0,19943,90,C93,S,D,,"New York, NY /  Stamford CT"
1,0,"Hoyt, Mr. William Fisher",male,,0,0,PC 17600,30.6958,,C,14,,"New York, NY"
1,1,"Hoyt, Mrs. Frederick Maxfield (Jane Anne Forby)",female,35,1,0,19943,90,C93,S,D,,"New York, NY /  Stamford CT"
1,1,"Icard, Miss. Amelie",female,38,0,0,113572,80,B28,,6,,
1,0,"Isham, Miss. Ann Elizabeth",female,50,0,0,PC 17595,28.7125,C49,C,,,"Paris, France New York, NY"
1,1,"Ismay, Mr. Joseph Bruce",male,49,0,0,112058,0,B52 B54 B56,S,C,,Liverpool
1,0,"Jones, Mr. Charles Cresson",male,46,0,0,694,26,,S,,80,"Bennington, VT"
1,0,"Julian, Mr. Henry Forbes",male,50,0,0,113044,26,E60,S,,,London
1,0,"Keeping, Mr. Edwin",male,32.5,0,0,113503,211.5,C132,C,,45,
1,0,"Kent, Mr. Edward Austin",male,58,0,0,11771,29.7,B37,C,,258,"Buffalo, NY"
1,0,"Kenyon, Mr. Frederick R",male,41,1,0,17464,51.8625,D21,S,,,"Southington / Noank, CT"
1,1,"Kenyon, Mrs. Frederick R (Marion)",female,,1,0,17464,51.8625,D21,S,8,,"Southington / Noank, CT"
1,1,"Kimball, Mr. Edwin Nelson Jr",male,42,1,0,11753,52.5542,D19,S,5,,"Boston, MA"
1,1,"Kimball, Mrs. Edwin Nelson Jr (Gertrude Parsons)",female,45,1,0,11753,52.5542,D19,S,5,,"Boston, MA"
1,0,"Klaber, Mr. Herman",male,,0,0,113028,26.55,C124,S,,,"Portland, OR"
1,1,"Kreuchen, Miss. Emilie",female,39,0,0,24160,211.3375,,S,2,,
1,1,"Leader, Dr. Alice (Farnham)",female,49,0,0,17465,25.9292,D17,S,8,,"New York, NY"
1,1,"LeRoy, Miss. Bertha",female,30,0,0,PC 17761,106.425,,C,2,,
1,1,"Lesurer, Mr. Gustave J",male,35,0,0,PC 17755,512.3292,B101,C,3,,
1,0,"Lewy, Mr. Ervin G",male,,0,0,PC 17612,27.7208,,C,,,"Chicago, IL"
1,0,"Lindeberg-Lind, Mr. Erik Gustaf ('Mr Edward Lingrey')",male,42,0,0,17475,26.55,,S,,,"Stockholm, Sweden"
1,1,"Lindstrom, Mrs. Carl Johan (Sigrid Posse)",female,55,0,0,112377,27.7208,,C,6,,"Stockholm, Sweden"
1,1,"Lines, Miss. Mary Conover",female,16,0,1,PC 17592,39.4,D28,S,9,,"Paris, France"
1,1,"Lines, Mrs. Ernest H (Elizabeth Lindsey James)",female,51,0,1,PC 17592,39.4,D28,S,9,,"Paris, France"
1,0,"Long, Mr. Milton Clyde",male,29,0,0,113501,30,D6,S,,126,"Springfield, MA"
1,1,"Longley, Miss. Gretchen Fiske",female,21,0,0,13502,77.9583,D9,S,10,,"Hudson, NY"
1,0,"Loring, Mr. Joseph Holland",male,30,0,0,113801,45.5,,S,,,"London / New York, NY"
1,1,"Lurette, Miss. Elise",female,58,0,0,PC 17569,146.5208,B80,C,,,
1,1,"Madill, Miss. Georgette Alexandra",female,15,0,1,24160,211.3375,B5,S,2,,"St Louis, MO"
1,0,"Maguire, Mr. John Edward",male,30,0,0,110469,26,C106,S,,,"Brockton, MA"
1,1,"Maioni, Miss. Roberta",female,16,0,0,110152,86.5,B79,S,8,,
1,1,"Marechal, Mr. Pierre",male,,0,0,11774,29.7,C47,C,7,,"Paris, France"
1,0,"Marvin, Mr. Daniel Warner",male,19,1,0,113773,53.1,D30,S,,,"New York, NY"
1,1,"Marvin, Mrs. Daniel Warner (Mary Graham Carmichael Farquarson)",female,18,1,0,113773,53.1,D30,S,10,,"New York, NY"
1,1,"Mayne, Mlle. Berthe Antonine ('Mrs de Villiers')",female,24,0,0,PC 17482,49.5042,C90,C,6,,"Belgium  Montreal, PQ"
1,0,"McCaffry, Mr. Thomas Francis",male,46,0,0,13050,75.2417,C6,C,,292,"Vancouver, BC"
1,0,"McCarthy, Mr. Timothy J",male,54,0,0,17463,51.8625,E46,S,,175,"Dorchester, MA"
1,1,"McGough, Mr. James Robert",male,36,0,0,PC 17473,26.2875,E25,S,7,,"Philadelphia, PA"
1,0,"Meyer, Mr. Edgar Joseph",male,28,1,0,PC 17604,82.1708,,C,,,"New York, NY"
1,1,"Meyer, Mrs. Edgar Joseph (Leila Saks)",female,,1,0,PC 17604,82.1708,,C,6,,"New York, NY"
1,0,"Millet, Mr. Francis Davis",male,65,0,0,13509,26.55,E38,S,,249,"East Bridgewater, MA"
1,0,"Minahan, Dr. William Edward",male,44,2,0,19928,90,C78,Q,,230,"Fond du Lac, WI"
1,1,"Minahan, Miss. Daisy E",female,33,1,0,19928,90,C78,Q,14,,"Green Bay, WI"
1,1,"Minahan, Mrs. William Edward (Lillian E Thorpe)",female,37,1,0,19928,90,C78,Q,14,,"Fond du Lac, WI"
1,1,"Mock, Mr. Philipp Edmund",male,30,1,0,13236,57.75,C78,C,11,,"New York, NY"
1,0,"Molson, Mr. Harry Markland",male,55,0,0,113787,30.5,C30,S,,,"Montreal, PQ"
1,0,"Moore, Mr. Clarence Bloomfield",male,47,0,0,113796,42.4,,S,,,"Washington, DC"
1,0,"Natsch, Mr. Charles H",male,37,0,1,PC 17596,29.7,C118,C,,,"Brooklyn, NY"
1,1,"Newell, Miss. Madeleine",female,31,1,0,35273,113.275,D36,C,6,,"Lexington, MA"
1,1,"Newell, Miss. Marjorie",female,23,1,0,35273,113.275,D36,C,6,,"Lexington, MA"
1,0,"Newell, Mr. Arthur Webster",male,58,0,2,35273,113.275,D48,C,,122,"Lexington, MA"
1,1,"Newsom, Miss. Helen Monypeny",female,19,0,2,11752,26.2833,D47,S,5,,"New York, NY"
1,0,"Nicholson, Mr. Arthur Ernest",male,64,0,0,693,26,,S,,263,"Isle of Wight, England"
1,1,"Oliva y Ocana, Dona. Fermina",female,39,0,0,PC 17758,108.9,C105,C,8,,
1,1,"Omont, Mr. Alfred Fernand",male,,0,0,F.C. 12998,25.7417,,C,7,,"Paris, France"
1,1,"Ostby, Miss. Helene Ragnhild",female,22,0,1,113509,61.9792,B36,C,5,,"Providence, RI"
1,0,"Ostby, Mr. Engelhart Cornelius",male,65,0,1,113509,61.9792,B30,C,,234,"Providence, RI"
1,0,"Ovies y Rodriguez, Mr. Servando",male,28.5,0,0,PC 17562,27.7208,D43,C,,189,"?Havana, Cuba"
1,0,"Parr, Mr. William Henry Marsh",male,,0,0,112052,0,,S,,,Belfast
1,0,"Partner, Mr. Austen",male,45.5,0,0,113043,28.5,C124,S,,166,"Surbiton Hill, Surrey"
1,0,"Payne, Mr. Vivian Ponsonby",male,23,0,0,12749,93.5,B24,S,,,"Montreal, PQ"
1,0,"Pears, Mr. Thomas Clinton",male,29,1,0,113776,66.6,C2,S,,,"Isleworth, England"
1,1,"Pears, Mrs. Thomas (Edith Wearne)",female,22,1,0,113776,66.6,C2,S,8,,"Isleworth, England"
1,0,"Penasco y Castellana, Mr. Victor de Satode",male,18,1,0,PC 17758,108.9,C65,C,,,"Madrid, Spain"
1,1,"Penasco y Castellana, Mrs. Victor de Satode (Maria Josefa Perez de Soto y Vallejo)",female,17,1,0,PC 17758,108.9,C65,C,8,,"Madrid, Spain"
1,1,"Perreault, Miss. Anne",female,30,0,0,12749,93.5,B73,S,3,,
1,1,"Peuchen, Major. Arthur Godfrey",male,52,0,0,113786,30.5,C104,S,6,,"Toronto, ON"
1,0,"Porter, Mr. Walter Chamberlain",male,47,0,0,110465,52,C110,S,,207,"Worcester, MA"
1,1,"Potter, Mrs. Thomas Jr (Lily Alexenia Wilson)",female,56,0,1,11767,83.1583,C50,C,7,,"Mt Airy, Philadelphia, PA"
1,0,"Reuchlin, Jonkheer. John George",male,38,0,0,19972,0,,S,,,"Rotterdam, Netherlands"
1,1,"Rheims, Mr. George Alexander Lucien",male,,0,0,PC 17607,39.6,,S,A,,"Paris /  New York, NY"
1,0,"Ringhini, Mr. Sante",male,22,0,0,PC 17760,135.6333,,C,,232,
1,0,"Robbins, Mr. Victor",male,,0,0,PC 17757,227.525,,C,,,
1,1,"Robert, Mrs. Edward Scott (Elisabeth Walton McMillan)",female,43,0,1,24160,211.3375,B3,S,2,,"St Louis, MO"
1,0,"Roebling, Mr. Washington Augustus II",male,31,0,0,PC 17590,50.4958,A24,S,,,"Trenton, NJ"
1,1,"Romaine, Mr. Charles Hallace ('Mr C Rolmane')",male,45,0,0,111428,26.55,,S,9,,"New York, NY"
1,0,"Rood, Mr. Hugh Roscoe",male,,0,0,113767,50,A32,S,,,"Seattle, WA"
1,1,"Rosenbaum, Miss. Edith Louise",female,33,0,0,PC 17613,27.7208,A11,C,11,,"Paris, France"
1,0,"Rosenshine, Mr. George ('Mr George Thorne')",male,46,0,0,PC 17585,79.2,,C,,16,"New York, NY"
1,0,"Ross, Mr. John Hugo",male,36,0,0,13049,40.125,A10,C,,,"Winnipeg, MB"
1,1,"Rothes, the Countess. of (Lucy Noel Martha Dyer-Edwards)",female,33,0,0,110152,86.5,B77,S,8,,"London  Vancouver, BC"
1,0,"Rothschild, Mr. Martin",male,55,1,0,PC 17603,59.4,,C,,,"New York, NY"
1,1,"Rothschild, Mrs. Martin (Elizabeth L. Barrett)",female,54,1,0,PC 17603,59.4,,C,6,,"New York, NY"
1,0,"Rowe, Mr. Alfred G",male,33,0,0,113790,26.55,,S,,109,London
1,1,"Ryerson, Master. John Borie",male,13,2,2,PC 17608,262.375,B57 B59 B63 B66,C,4,,"Haverford, PA / Cooperstown, NY"
1,1,"Ryerson, Miss. Emily Borie",female,18,2,2,PC 17608,262.375,B57 B59 B63 B66,C,4,,"Haverford, PA / Cooperstown, NY"
1,1,"Ryerson, Miss. Susan Parker "'Suzette'",female,21,2,2,PC 17608,262.375,B57 B59 B63 B66,C,4,,"Haverford, PA / Cooperstown, NY"
1,0,"Ryerson, Mr. Arthur Larned",male,61,1,3,PC 17608,262.375,B57 B59 B63 B66,C,,,"Haverford, PA / Cooperstown, NY"
1,1,"Ryerson, Mrs. Arthur Larned (Emily Maria Borie)",female,48,1,3,PC 17608,262.375,B57 B59 B63 B66,C,4,,"Haverford, PA / Cooperstown, NY"
1,1,"Saalfeld, Mr. Adolphe",male,,0,0,19988,30.5,C106,S,3,,"Manchester, England"
1,1,"Sagesser, Mlle. Emma",female,24,0,0,PC 17477,69.3,B35,C,9,,
1,1,"Salomon, Mr. Abraham L",male,,0,0,111163,26,,S,1,,"New York, NY"
1,1,"Schabert, Mrs. Paul (Emma Mock)",female,35,1,0,13236,57.75,C28,C,11,,"New York, NY"
1,1,"Serepeca, Miss. Augusta",female,30,0,0,113798,31,,C,4,,
1,1,"Seward, Mr. Frederic Kimber",male,34,0,0,113794,26.55,,S,7,,"New York, NY"
1,1,"Shutes, Miss. Elizabeth W",female,40,0,0,PC 17582,153.4625,C125,S,3,,"New York, NY / Greenwich CT"
1,1,"Silverthorne, Mr. Spencer Victor",male,35,0,0,PC 17475,26.2875,E24,S,5,,"St Louis, MO"
1,0,"Silvey, Mr. William Baird",male,50,1,0,13507,55.9,E44,S,,,"Duluth, MN"
1,1,"Silvey, Mrs. William Baird (Alice Munger)",female,39,1,0,13507,55.9,E44,S,11,,"Duluth, MN"
1,1,"Simonius-Blumer, Col. Oberst Alfons",male,56,0,0,13213,35.5,A26,C,3,,"Basel, Switzerland"
1,1,"Sloper, Mr. William Thompson",male,28,0,0,113788,35.5,A6,S,7,,"New Britain, CT"
1,0,"Smart, Mr. John Montgomery",male,56,0,0,113792,26.55,,S,,,"New York, NY"
1,0,"Smith, Mr. James Clinch",male,56,0,0,17764,30.6958,A7,C,,,"St James, Long Island, NY"
1,0,"Smith, Mr. Lucien Philip",male,24,1,0,13695,60,C31,S,,,"Huntington, WV"
1,0,"Smith, Mr. Richard William",male,,0,0,113056,26,A19,S,,,"Streatham, Surrey"
1,1,"Smith, Mrs. Lucien Philip (Mary Eloise Hughes)",female,18,1,0,13695,60,C31,S,6,,"Huntington, WV"
1,1,"Snyder, Mr. John Pillsbury",male,24,1,0,21228,82.2667,B45,S,7,,"Minneapolis, MN"
1,1,"Snyder, Mrs. John Pillsbury (Nelle Stevenson)",female,23,1,0,21228,82.2667,B45,S,7,,"Minneapolis, MN"
1,1,"Spedden, Master. Robert Douglas",male,6,0,2,16966,134.5,E34,C,3,,"Tuxedo Park, NY"
1,1,"Spedden, Mr. Frederic Oakley",male,45,1,1,16966,134.5,E34,C,3,,"Tuxedo Park, NY"
1,1,"Spedden, Mrs. Frederic Oakley (Margaretta Corning Stone)",female,40,1,1,16966,134.5,E34,C,3,,"Tuxedo Park, NY"
1,0,"Spencer, Mr. William Augustus",male,57,1,0,PC 17569,146.5208,B78,C,,,"Paris, France"
1,1,"Spencer, Mrs. William Augustus (Marie Eugenie)",female,,1,0,PC 17569,146.5208,B78,C,6,,"Paris, France"
1,1,"Stahelin-Maeglin, Dr. Max",male,32,0,0,13214,30.5,B50,C,3,,"Basel, Switzerland"
1,0,"Stead, Mr. William Thomas",male,62,0,0,113514,26.55,C87,S,,,"Wimbledon Park, London / Hayling Island, Hants"
1,1,"Stengel, Mr. Charles Emil Henry",male,54,1,0,11778,55.4417,C116,C,1,,"Newark, NJ"
1,1,"Stengel, Mrs. Charles Emil Henry (Annie May Morris)",female,43,1,0,11778,55.4417,C116,C,5,,"Newark, NJ"
1,1,"Stephenson, Mrs. Walter Bertram (Martha Eustis)",female,52,1,0,36947,78.2667,D20,C,4,,"Haverford, PA"
1,0,"Stewart, Mr. Albert A",male,,0,0,PC 17605,27.7208,,C,,,"Gallipolis, Ohio / ? Paris / New York"
1,1,"Stone, Mrs. George Nelson (Martha Evelyn)",female,62,0,0,113572,80,B28,,6,,"Cincinatti, OH"
1,0,"Straus, Mr. Isidor",male,67,1,0,PC 17483,221.7792,C55 C57,S,,96,"New York, NY"
1,0,"Straus, Mrs. Isidor (Rosalie Ida Blun)",female,63,1,0,PC 17483,221.7792,C55 C57,S,,,"New York, NY"
1,0,"Sutton, Mr. Frederick",male,61,0,0,36963,32.3208,D50,S,,46,"Haddenfield, NJ"
1,1,"Swift, Mrs. Frederick Joel (Margaret Welles Barron)",female,48,0,0,17466,25.9292,D17,S,8,,"Brooklyn, NY"
1,1,"Taussig, Miss. Ruth",female,18,0,2,110413,79.65,E68,S,8,,"New York, NY"
1,0,"Taussig, Mr. Emil",male,52,1,1,110413,79.65,E67,S,,,"New York, NY"
1,1,"Taussig, Mrs. Emil (Tillie Mandelbaum)",female,39,1,1,110413,79.65,E67,S,8,,"New York, NY"
1,1,"Taylor, Mr. Elmer Zebley",male,48,1,0,19996,52,C126,S,5 7,,"London /  East Orange, NJ"
1,1,"Taylor, Mrs. Elmer Zebley (Juliet Cummins Wright)",female,,1,0,19996,52,C126,S,5 7,,"London /  East Orange, NJ"
1,0,"Thayer, Mr. John Borland",male,49,1,1,17421,110.8833,C68,C,,,"Haverford, PA"
1,1,"Thayer, Mr. John Borland Jr",male,17,0,2,17421,110.8833,C70,C,B,,"Haverford, PA"
1,1,"Thayer, Mrs. John Borland (Marian Longstreth Morris)",female,39,1,1,17421,110.8833,C68,C,4,,"Haverford, PA"
1,1,"Thorne, Mrs. Gertrude Maybelle",female,,0,0,PC 17585,79.2,,C,D,,"New York, NY"
1,1,"Tucker, Mr. Gilbert Milligan Jr",male,31,0,0,2543,28.5375,C53,C,7,,"Albany, NY"
1,0,"Uruchurtu, Don. Manuel E",male,40,0,0,PC 17601,27.7208,,C,,,"Mexico City, Mexico"
1,0,"Van der hoef, Mr. Wyckoff",male,61,0,0,111240,33.5,B19,S,,245,"Brooklyn, NY"
1,0,"Walker, Mr. William Anderson",male,47,0,0,36967,34.0208,D46,S,,,"East Orange, NJ"
1,1,"Ward, Miss. Anna",female,35,0,0,PC 17755,512.3292,,C,3,,
1,0,"Warren, Mr. Frank Manley",male,64,1,0,110813,75.25,D37,C,,,"Portland, OR"
1,1,"Warren, Mrs. Frank Manley (Anna Sophia Atkinson)",female,60,1,0,110813,75.25,D37,C,5,,"Portland, OR"
1,0,"Weir, Col. John",male,60,0,0,113800,26.55,,S,,,"England Salt Lake City, Utah"
1,0,"White, Mr. Percival Wayland",male,54,0,1,35281,77.2875,D26,S,,,"Brunswick, ME"
1,0,"White, Mr. Richard Frasar",male,21,0,1,35281,77.2875,D26,S,,169,"Brunswick, ME"
1,1,"White, Mrs. John Stuart (Ella Holmes)",female,55,0,0,PC 17760,135.6333,C32,C,8,,"New York, NY / Briarcliff Manor NY"
1,1,"Wick, Miss. Mary Natalie",female,31,0,2,36928,164.8667,C7,S,8,,"Youngstown, OH"
1,0,"Wick, Mr. George Dennick",male,57,1,1,36928,164.8667,,S,,,"Youngstown, OH"
1,1,"Wick, Mrs. George Dennick (Mary Hitchcock)",female,45,1,1,36928,164.8667,,S,8,,"Youngstown, OH"
1,0,"Widener, Mr. George Dunton",male,50,1,1,113503,211.5,C80,C,,,"Elkins Park, PA"
1,0,"Widener, Mr. Harry Elkins",male,27,0,2,113503,211.5,C82,C,,,"Elkins Park, PA"
1,1,"Widener, Mrs. George Dunton (Eleanor Elkins)",female,50,1,1,113503,211.5,C80,C,4,,"Elkins Park, PA"
1,1,"Willard, Miss. Constance",female,21,0,0,113795,26.55,,S,8 10,,"Duluth, MN"
1,0,"Williams, Mr. Charles Duane",male,51,0,1,PC 17597,61.3792,,C,,,"Geneva, Switzerland / Radnor, PA"
1,1,"Williams, Mr. Richard Norris II",male,21,0,1,PC 17597,61.3792,,C,A,,"Geneva, Switzerland / Radnor, PA"
1,0,"Williams-Lambert, Mr. Fletcher Fellows",male,,0,0,113510,35,C128,S,,,"London, England"
1,1,"Wilson, Miss. Helen Alice",female,31,0,0,16966,134.5,E39 E41,C,3,,
1,1,"Woolner, Mr. Hugh",male,,0,0,19947,35.5,C52,S,D,,"London, England"
1,0,"Wright, Mr. George",male,62,0,0,113807,26.55,,S,,,"Halifax, NS"
1,1,"Young, Miss. Marie Grice",female,36,0,0,PC 17760,135.6333,C32,C,8,,"New York, NY / Washington, DC"
2,0,"Abelson, Mr. Samuel",male,30,1,0,P/PP 3381,24,,C,,,"Russia New York, NY"
2,1,"Abelson, Mrs. Samuel (Hannah Wizosky)",female,28,1,0,P/PP 3381,24,,C,10,,"Russia New York, NY"
2,0,"Aldworth, Mr. Charles Augustus",male,30,0,0,248744,13,,S,,,"Bryn Mawr, PA, USA"
2,0,"Andrew, Mr. Edgardo Samuel",male,18,0,0,231945,11.5,,S,,,"Buenos Aires, Argentina / New Jersey, NJ"
2,0,"Andrew, Mr. Frank Thomas",male,25,0,0,C.A. 34050,10.5,,S,,,"Cornwall, England Houghton, MI"
2,0,"Angle, Mr. William A",male,34,1,0,226875,26,,S,,,"Warwick, England"
2,1,"Angle, Mrs. William A (Florence 'Mary' Agnes Hughes)",female,36,1,0,226875,26,,S,11,,"Warwick, England"
2,0,"Ashby, Mr. John",male,57,0,0,244346,13,,S,,,"West Hoboken, NJ"
2,0,"Bailey, Mr. Percy Andrew",male,18,0,0,29108,11.5,,S,,,"Penzance, Cornwall / Akron, OH"
2,0,"Baimbrigge, Mr. Charles Robert",male,23,0,0,C.A. 31030,10.5,,S,,,Guernsey
2,1,"Ball, Mrs. (Ada E Hall)",female,36,0,0,28551,13,D,S,10,,"Bristol, Avon / Jacksonville, FL"
2,0,"Banfield, Mr. Frederick James",male,28,0,0,C.A./SOTON 34068,10.5,,S,,,"Plymouth, Dorset / Houghton, MI"
2,0,"Bateman, Rev. Robert James",male,51,0,0,S.O.P. 1166,12.525,,S,,174,"Jacksonville, FL"
2,1,"Beane, Mr. Edward",male,32,1,0,2908,26,,S,13,,"Norwich / New York, NY"
2,1,"Beane, Mrs. Edward (Ethel Clarke)",female,19,1,0,2908,26,,S,13,,"Norwich / New York, NY"
2,0,"Beauchamp, Mr. Henry James",male,28,0,0,244358,26,,S,,,England
2,1,"Becker, Master. Richard F",male,1,2,1,230136,39,F4,S,11,,"Guntur, India / Benton Harbour, MI"
2,1,"Becker, Miss. Marion Louise",female,4,2,1,230136,39,F4,S,11,,"Guntur, India / Benton Harbour, MI"
2,1,"Becker, Miss. Ruth Elizabeth",female,12,2,1,230136,39,F4,S,13,,"Guntur, India / Benton Harbour, MI"
2,1,"Becker, Mrs. Allen Oliver (Nellie E Baumgardner)",female,36,0,3,230136,39,F4,S,11,,"Guntur, India / Benton Harbour, MI"
2,1,"Beesley, Mr. Lawrence",male,34,0,0,248698,13,D56,S,13,,London
2,1,"Bentham, Miss. Lilian W",female,19,0,0,28404,13,,S,12,,"Rochester, NY"
2,0,"Berriman, Mr. William John",male,23,0,0,28425,13,,S,,,"St Ives, Cornwall / Calumet, MI"
2,0,"Botsford, Mr. William Hull",male,26,0,0,237670,13,,S,,,"Elmira, NY / Orange, NJ"
2,0,"Bowenur, Mr. Solomon",male,42,0,0,211535,13,,S,,,London
2,0,"Bracken, Mr. James H",male,27,0,0,220367,13,,S,,,"Lake Arthur, Chavez County, NM"
2,1,"Brown, Miss. Amelia "'Mildred'",female,24,0,0,248733,13,F33,S,11,,"London / Montreal, PQ"
2,1,"Brown, Miss. Edith Eileen",female,15,0,2,29750,39,,S,14,,"Cape Town, South Africa / Seattle, WA"
2,0,"Brown, Mr. Thomas William Solomon",male,60,1,1,29750,39,,S,,,"Cape Town, South Africa / Seattle, WA"
2,1,"Brown, Mrs. Thomas William Solomon (Elizabeth Catherine Ford)",female,40,1,1,29750,39,,S,14,,"Cape Town, South Africa / Seattle, WA"
2,1,"Bryhl, Miss. Dagmar Jenny Ingeborg ",female,20,1,0,236853,26,,S,12,,"Skara, Sweden / Rockford, IL"
2,0,"Bryhl, Mr. Kurt Arnold Gottfrid",male,25,1,0,236853,26,,S,,,"Skara, Sweden / Rockford, IL"
2,1,"Buss, Miss. Kate",female,36,0,0,27849,13,,S,9,,"Sittingbourne, England / San Diego, CA"
2,0,"Butler, Mr. Reginald Fenton",male,25,0,0,234686,13,,S,,97,"Southsea, Hants"
2,0,"Byles, Rev. Thomas Roussel Davids",male,42,0,0,244310,13,,S,,,London
2,1,"Bystrom, Mrs. (Karolina)",female,42,0,0,236852,13,,S,,,"New York, NY"
2,1,"Caldwell, Master. Alden Gates",male,0.8333,0,2,248738,29,,S,13,,"Bangkok, Thailand / Roseville, IL"
2,1,"Caldwell, Mr. Albert Francis",male,26,1,1,248738,29,,S,13,,"Bangkok, Thailand / Roseville, IL"
2,1,"Caldwell, Mrs. Albert Francis (Sylvia Mae Harbaugh)",female,22,1,1,248738,29,,S,13,,"Bangkok, Thailand / Roseville, IL"
2,1,"Cameron, Miss. Clear Annie",female,35,0,0,F.C.C. 13528,21,,S,14,,"Mamaroneck, NY"
2,0,"Campbell, Mr. William",male,,0,0,239853,0,,S,,,Belfast
2,0,"Carbines, Mr. William",male,19,0,0,28424,13,,S,,18,"St Ives, Cornwall / Calumet, MI"
2,0,"Carter, Mrs. Ernest Courtenay (Lilian Hughes)",female,44,1,0,244252,26,,S,,,London
2,0,"Carter, Rev. Ernest Courtenay",male,54,1,0,244252,26,,S,,,London
2,0,"Chapman, Mr. Charles Henry",male,52,0,0,248731,13.5,,S,,130,"Bronx, NY"
2,0,"Chapman, Mr. John Henry",male,37,1,0,SC/AH 29037,26,,S,,17,"Cornwall / Spokane, WA"
2,0,"Chapman, Mrs. John Henry (Sara Elizabeth Lawry)",female,29,1,0,SC/AH 29037,26,,S,,,"Cornwall / Spokane, WA"
2,1,"Christy, Miss. Julie Rachel",female,25,1,1,237789,30,,S,12,,London
2,1,"Christy, Mrs. (Alice Frances)",female,45,0,2,237789,30,,S,12,,London
2,0,"Clarke, Mr. Charles Valentine",male,29,1,0,2003,26,,S,,,"England / San Francisco, CA"
2,1,"Clarke, Mrs. Charles V (Ada Maria Winfield)",female,28,1,0,2003,26,,S,14,,"England / San Francisco, CA"
2,0,"Coleridge, Mr. Reginald Charles",male,29,0,0,W./C. 14263,10.5,,S,,,"Hartford, Huntingdonshire"
2,0,"Collander, Mr. Erik Gustaf",male,28,0,0,248740,13,,S,,,"Helsinki, Finland Ashtabula, Ohio"
2,1,"Collett, Mr. Sidney C Stuart",male,24,0,0,28034,10.5,,S,9,,"London / Fort Byron, NY"
2,1,"Collyer, Miss. Marjorie "'Lottie'",female,8,0,2,C.A. 31921,26.25,,S,14,,"Bishopstoke, Hants / Fayette Valley, ID"
2,0,"Collyer, Mr. Harvey",male,31,1,1,C.A. 31921,26.25,,S,,,"Bishopstoke, Hants / Fayette Valley, ID"
2,1,"Collyer, Mrs. Harvey (Charlotte Annie Tate)",female,31,1,1,C.A. 31921,26.25,,S,14,,"Bishopstoke, Hants / Fayette Valley, ID"
2,1,"Cook, Mrs. (Selena Rogers)",female,22,0,0,W./C. 14266,10.5,F33,S,14,,Pennsylvania
2,0,"Corbett, Mrs. Walter H (Irene Colvin)",female,30,0,0,237249,13,,S,,,"Provo, UT"
2,0,"Corey, Mrs. Percy C (Mary Phyllis Elizabeth Miller)",female,,0,0,F.C.C. 13534,21,,S,,,"Upper Burma, India Pittsburgh, PA"
2,0,"Cotterill, Mr. Henry "'Harry'",male,21,0,0,29107,11.5,,S,,,"Penzance, Cornwall / Akron, OH"
2,0,"Cunningham, Mr. Alfred Fleming",male,,0,0,239853,0,,S,,,Belfast
2,1,"Davies, Master. John Morgan Jr",male,8,1,1,C.A. 33112,36.75,,S,14,,"St Ives, Cornwall / Hancock, MI"
2,0,"Davies, Mr. Charles Henry",male,18,0,0,S.O.C. 14879,73.5,,S,,,"Lyndhurst, England"
2,1,"Davies, Mrs. John Morgan (Elizabeth Agnes Mary White) ",female,48,0,2,C.A. 33112,36.75,,S,14,,"St Ives, Cornwall / Hancock, MI"
2,1,"Davis, Miss. Mary",female,28,0,0,237668,13,,S,13,,"London / Staten Island, NY"
2,0,"de Brito, Mr. Jose Joaquim",male,32,0,0,244360,13,,S,,,"Portugal / Sau Paulo, Brazil"
2,0,"Deacon, Mr. Percy William",male,17,0,0,S.O.C. 14879,73.5,,S,,,
2,0,"del Carlo, Mr. Sebastiano",male,29,1,0,SC/PARIS 2167,27.7208,,C,,295,"Lucca, Italy / California"
2,1,"del Carlo, Mrs. Sebastiano (Argenia Genovesi)",female,24,1,0,SC/PARIS 2167,27.7208,,C,12,,"Lucca, Italy / California"
2,0,"Denbury, Mr. Herbert",male,25,0,0,C.A. 31029,31.5,,S,,,"Guernsey / Elizabeth, NJ"
2,0,"Dibden, Mr. William",male,18,0,0,S.O.C. 14879,73.5,,S,,,"New Forest, England"
2,1,"Doling, Miss. Elsie",female,18,0,1,231919,23,,S,,,Southampton
2,1,"Doling, Mrs. John T (Ada Julia Bone)",female,34,0,1,231919,23,,S,,,Southampton
2,0,"Downton, Mr. William James",male,54,0,0,28403,26,,S,,,"Holley, NY"
2,1,"Drew, Master. Marshall Brines",male,8,0,2,28220,32.5,,S,10,,"Greenport, NY"
2,0,"Drew, Mr. James Vivian",male,42,1,1,28220,32.5,,S,,,"Greenport, NY"
2,1,"Drew, Mrs. James Vivian (Lulu Thorne Christian)",female,34,1,1,28220,32.5,,S,10,,"Greenport, NY"
2,1,"Duran y More, Miss. Asuncion",female,27,1,0,SC/PARIS 2149,13.8583,,C,12,,"Barcelona, Spain / Havana, Cuba"
2,1,"Duran y More, Miss. Florentina",female,30,1,0,SC/PARIS 2148,13.8583,,C,12,,"Barcelona, Spain / Havana, Cuba"
2,0,"Eitemiller, Mr. George Floyd",male,23,0,0,29751,13,,S,,,"England / Detroit, MI"
2,0,"Enander, Mr. Ingvar",male,21,0,0,236854,13,,S,,,"Goteborg, Sweden / Rockford, IL"
2,0,"Fahlstrom, Mr. Arne Jonas",male,18,0,0,236171,13,,S,,,"Oslo, Norway Bayonne, NJ"
2,0,"Faunthorpe, Mr. Harry",male,40,1,0,2926,26,,S,,286,"England / Philadelphia, PA"
2,1,"Faunthorpe, Mrs. Lizzie (Elizabeth Anne Wilkinson)",female,29,1,0,2926,26,,S,16,,
2,0,"Fillbrook, Mr. Joseph Charles",male,18,0,0,C.A. 15185,10.5,,S,,,"Cornwall / Houghton, MI"
2,0,"Fox, Mr. Stanley Hubert",male,36,0,0,229236,13,,S,,236,"Rochester, NY"
2,0,"Frost, Mr. Anthony Wood "'Archie'",male,,0,0,239854,0,,S,,,Belfast
2,0,"Funk, Miss. Annie Clemmer",female,38,0,0,237671,13,,S,,,"Janjgir, India / Pennsylvania"
2,0,"Fynney, Mr. Joseph J",male,35,0,0,239865,26,,S,,322,"Liverpool / Montreal, PQ"
2,0,"Gale, Mr. Harry",male,38,1,0,28664,21,,S,,,"Cornwall / Clear Creek, CO"
2,0,"Gale, Mr. Shadrach",male,34,1,0,28664,21,,S,,,"Cornwall / Clear Creek, CO"
2,1,"Garside, Miss. Ethel",female,34,0,0,243880,13,,S,12,,"Brooklyn, NY"
2,0,"Gaskell, Mr. Alfred",male,16,0,0,239865,26,,S,,,"Liverpool / Montreal, PQ"
2,0,"Gavey, Mr. Lawrence",male,26,0,0,31028,10.5,,S,,,"Guernsey / Elizabeth, NJ"
2,0,"Gilbert, Mr. William",male,47,0,0,C.A. 30769,10.5,,S,,,Cornwall
2,0,"Giles, Mr. Edgar",male,21,1,0,28133,11.5,,S,,,"Cornwall / Camden, NJ"
2,0,"Giles, Mr. Frederick Edward",male,21,1,0,28134,11.5,,S,,,"Cornwall / Camden, NJ"
2,0,"Giles, Mr. Ralph",male,24,0,0,248726,13.5,,S,,297,"West Kensington, London"
2,0,"Gill, Mr. John William",male,24,0,0,233866,13,,S,,155,"Clevedon, England"
2,0,"Gillespie, Mr. William Henry",male,34,0,0,12233,13,,S,,,"Vancouver, BC"
2,0,"Givard, Mr. Hans Kristensen",male,30,0,0,250646,13,,S,,305,
2,0,"Greenberg, Mr. Samuel",male,52,0,0,250647,13,,S,,19,"Bronx, NY"
2,0,"Hale, Mr. Reginald",male,30,0,0,250653,13,,S,,75,"Auburn, NY"
2,1,"Hamalainen, Master. Viljo",male,0.6667,1,1,250649,14.5,,S,4,,"Detroit, MI"
2,1,"Hamalainen, Mrs. William (Anna)",female,24,0,2,250649,14.5,,S,4,,"Detroit, MI"
2,0,"Harbeck, Mr. William H",male,44,0,0,248746,13,,S,,35,"Seattle, WA / Toledo, OH"
2,1,"Harper, Miss. Annie Jessie "'Nina'",female,6,0,1,248727,33,,S,11,,"Denmark Hill, Surrey / Chicago"
2,0,"Harper, Rev. John",male,28,0,1,248727,33,,S,,,"Denmark Hill, Surrey / Chicago"
2,1,"Harris, Mr. George",male,62,0,0,S.W./PP 752,10.5,,S,15,,London
2,0,"Harris, Mr. Walter",male,30,0,0,W/C 14208,10.5,,S,,,"Walthamstow, England"
2,1,"Hart, Miss. Eva Miriam",female,7,0,2,F.C.C. 13529,26.25,,S,14,,"Ilford, Essex / Winnipeg, MB"
2,0,"Hart, Mr. Benjamin",male,43,1,1,F.C.C. 13529,26.25,,S,,,"Ilford, Essex / Winnipeg, MB"
2,1,"Hart, Mrs. Benjamin (Esther Ada Bloomfield)",female,45,1,1,F.C.C. 13529,26.25,,S,14,,"Ilford, Essex / Winnipeg, MB"
2,1,"Herman, Miss. Alice",female,24,1,2,220845,65,,S,9,,"Somerset / Bernardsville, NJ"
2,1,"Herman, Miss. Kate",female,24,1,2,220845,65,,S,9,,"Somerset / Bernardsville, NJ"
2,0,"Herman, Mr. Samuel",male,49,1,2,220845,65,,S,,,"Somerset / Bernardsville, NJ"
2,1,"Herman, Mrs. Samuel (Jane Laver)",female,48,1,2,220845,65,,S,9,,"Somerset / Bernardsville, NJ"
2,1,"Hewlett, Mrs. (Mary D Kingcome) ",female,55,0,0,248706,16,,S,13,,"India / Rapid City, SD"
2,0,"Hickman, Mr. Leonard Mark",male,24,2,0,S.O.C. 14879,73.5,,S,,,"West Hampstead, London / Neepawa, MB"
2,0,"Hickman, Mr. Lewis",male,32,2,0,S.O.C. 14879,73.5,,S,,256,"West Hampstead, London / Neepawa, MB"
2,0,"Hickman, Mr. Stanley George",male,21,2,0,S.O.C. 14879,73.5,,S,,,"West Hampstead, London / Neepawa, MB"
2,0,"Hiltunen, Miss. Marta",female,18,1,1,250650,13,,S,,,"Kontiolahti, Finland / Detroit, MI"
2,1,"Hocking, Miss. Ellen "'Nellie'",female,20,2,1,29105,23,,S,4,,"Cornwall / Akron, OH"
2,0,"Hocking, Mr. Richard George",male,23,2,1,29104,11.5,,S,,,"Cornwall / Akron, OH"
2,0,"Hocking, Mr. Samuel James Metcalfe",male,36,0,0,242963,13,,S,,,"Devonport, England"
2,1,"Hocking, Mrs. Elizabeth (Eliza Needs)",female,54,1,3,29105,23,,S,4,,"Cornwall / Akron, OH"
2,0,"Hodges, Mr. Henry Price",male,50,0,0,250643,13,,S,,149,Southampton
2,0,"Hold, Mr. Stephen",male,44,1,0,26707,26,,S,,,"England / Sacramento, CA"
2,1,"Hold, Mrs. Stephen (Annie Margaret Hill)",female,29,1,0,26707,26,,S,10,,"England / Sacramento, CA"
2,0,"Hood, Mr. Ambrose Jr",male,21,0,0,S.O.C. 14879,73.5,,S,,,"New Forest, England"
2,1,"Hosono, Mr. Masabumi",male,42,0,0,237798,13,,S,10,,"Tokyo, Japan"
2,0,"Howard, Mr. Benjamin",male,63,1,0,24065,26,,S,,,"Swindon, England"
2,0,"Howard, Mrs. Benjamin (Ellen Truelove Arman)",female,60,1,0,24065,26,,S,,,"Swindon, England"
2,0,"Hunt, Mr. George Henry",male,33,0,0,SCO/W 1585,12.275,,S,,,"Philadelphia, PA"
2,1,"Ilett, Miss. Bertha",female,17,0,0,SO/C 14885,10.5,,S,,,Guernsey
2,0,"Jacobsohn, Mr. Sidney Samuel",male,42,1,0,243847,27,,S,,,London
2,1,"Jacobsohn, Mrs. Sidney Samuel (Amy Frances Christy)",female,24,2,1,243847,27,,S,12,,London
2,0,"Jarvis, Mr. John Denzil",male,47,0,0,237565,15,,S,,,"North Evington, England"
2,0,"Jefferys, Mr. Clifford Thomas",male,24,2,0,C.A. 31029,31.5,,S,,,"Guernsey / Elizabeth, NJ"
2,0,"Jefferys, Mr. Ernest Wilfred",male,22,2,0,C.A. 31029,31.5,,S,,,"Guernsey / Elizabeth, NJ"
2,0,"Jenkin, Mr. Stephen Curnow",male,32,0,0,C.A. 33111,10.5,,S,,,"St Ives, Cornwall / Houghton, MI"
2,1,"Jerwan, Mrs. Amin S (Marie Marthe Thuillard)",female,23,0,0,SC/AH Basle 541,13.7917,D,C,11,,"New York, NY"
2,0,"Kantor, Mr. Sinai",male,34,1,0,244367,26,,S,,283,"Moscow / Bronx, NY"
2,1,"Kantor, Mrs. Sinai (Miriam Sternin)",female,24,1,0,244367,26,,S,12,,"Moscow / Bronx, NY"
2,0,"Karnes, Mrs. J Frank (Claire Bennett)",female,22,0,0,F.C.C. 13534,21,,S,,,"India / Pittsburgh, PA"
2,1,"Keane, Miss. Nora A",female,,0,0,226593,12.35,E101,Q,10,,"Harrisburg, PA"
2,0,"Keane, Mr. Daniel",male,35,0,0,233734,12.35,,Q,,,
2,1,"Kelly, Mrs. Florence "'Fannie'",female,45,0,0,223596,13.5,,S,9,,"London / New York, NY"
2,0,"Kirkland, Rev. Charles Leonard",male,57,0,0,219533,12.35,,Q,,,"Glasgow / Bangor, ME"
2,0,"Knight, Mr. Robert J",male,,0,0,239855,0,,S,,,Belfast
2,0,"Kvillner, Mr. Johan Henrik Johannesson",male,31,0,0,C.A. 18723,10.5,,S,,165,"Sweden / Arlington, NJ"
2,0,"Lahtinen, Mrs. William (Anna Sylfven)",female,26,1,1,250651,26,,S,,,"Minneapolis, MN"
2,0,"Lahtinen, Rev. William",male,30,1,1,250651,26,,S,,,"Minneapolis, MN"
2,0,"Lamb, Mr. John Joseph",male,,0,0,240261,10.7083,,Q,,,
2,1,"Laroche, Miss. Louise",female,1,1,2,SC/Paris 2123,41.5792,,C,14,,Paris / Haiti
2,1,"Laroche, Miss. Simonne Marie Anne Andree",female,3,1,2,SC/Paris 2123,41.5792,,C,14,,Paris / Haiti
2,0,"Laroche, Mr. Joseph Philippe Lemercier",male,25,1,2,SC/Paris 2123,41.5792,,C,,,Paris / Haiti
2,1,"Laroche, Mrs. Joseph (Juliette Marie Louise Lafargue)",female,22,1,2,SC/Paris 2123,41.5792,,C,14,,Paris / Haiti
2,1,"Lehmann, Miss. Bertha",female,17,0,0,SC 1748,12,,C,12,,"Berne, Switzerland / Central City, IA"
2,1,"Leitch, Miss. Jessie Wills",female,,0,0,248727,33,,S,11,,"London / Chicago, IL"
2,1,"Lemore, Mrs. (Amelia Milley)",female,34,0,0,C.A. 34260,10.5,F33,S,14,,"Chicago, IL"
2,0,"Levy, Mr. Rene Jacques",male,36,0,0,SC/Paris 2163,12.875,D,C,,,"Montreal, PQ"
2,0,"Leyson, Mr. Robert William Norman",male,24,0,0,C.A. 29566,10.5,,S,,108,
2,0,"Lingane, Mr. John",male,61,0,0,235509,12.35,,Q,,,
2,0,"Louch, Mr. Charles Alexander",male,50,1,0,SC/AH 3085,26,,S,,121,"Weston-Super-Mare, Somerset"
2,1,"Louch, Mrs. Charles Alexander (Alice Adelaide Slow)",female,42,1,0,SC/AH 3085,26,,S,,,"Weston-Super-Mare, Somerset"
2,0,"Mack, Mrs. (Mary)",female,57,0,0,S.O./P.P. 3,10.5,E77,S,,52,"Southampton / New York, NY"
2,0,"Malachard, Mr. Noel",male,,0,0,237735,15.0458,D,C,,,Paris
2,1,"Mallet, Master. Andre",male,1,0,2,S.C./PARIS 2079,37.0042,,C,10,,"Paris / Montreal, PQ"
2,0,"Mallet, Mr. Albert",male,31,1,1,S.C./PARIS 2079,37.0042,,C,,,"Paris / Montreal, PQ"
2,1,"Mallet, Mrs. Albert (Antoinette Magnin)",female,24,1,1,S.C./PARIS 2079,37.0042,,C,10,,"Paris / Montreal, PQ"
2,0,"Mangiavacchi, Mr. Serafino Emilio",male,,0,0,SC/A.3 2861,15.5792,,C,,,"New York, NY"
2,0,"Matthews, Mr. William John",male,30,0,0,28228,13,,S,,,"St Austall, Cornwall"
2,0,"Maybery, Mr. Frank Hubert",male,40,0,0,239059,16,,S,,,"Weston-Super-Mare / Moose Jaw, SK"
2,0,"McCrae, Mr. Arthur Gordon",male,32,0,0,237216,13.5,,S,,209,"Sydney, Australia"
2,0,"McCrie, Mr. James Matthew",male,30,0,0,233478,13,,S,,,"Sarnia, ON"
2,0,"McKane, Mr. Peter David",male,46,0,0,28403,26,,S,,,"Rochester, NY"
2,1,"Mellinger, Miss. Madeleine Violet",female,13,0,1,250644,19.5,,S,14,,"England / Bennington, VT"
2,1,"Mellinger, Mrs. (Elizabeth Anne Maidment)",female,41,0,1,250644,19.5,,S,14,,"England / Bennington, VT"
2,1,"Mellors, Mr. William John",male,19,0,0,SW/PP 751,10.5,,S,B,,"Chelsea, London"
2,0,"Meyer, Mr. August",male,39,0,0,248723,13,,S,,,"Harrow-on-the-Hill, Middlesex"
2,0,"Milling, Mr. Jacob Christian",male,48,0,0,234360,13,,S,,271,"Copenhagen, Denmark"
2,0,"Mitchell, Mr. Henry Michael",male,70,0,0,C.A. 24580,10.5,,S,,,"Guernsey / Montclair, NJ and/or Toledo, Ohio"
2,0,"Montvila, Rev. Juozas",male,27,0,0,211536,13,,S,,,"Worcester, MA"
2,0,"Moraweck, Dr. Ernest",male,54,0,0,29011,14,,S,,,"Frankfort, KY"
2,0,"Morley, Mr. Henry Samuel ('Mr Henry Marshall')",male,39,0,0,250655,26,,S,,,
2,0,"Mudd, Mr. Thomas Charles",male,16,0,0,S.O./P.P. 3,10.5,,S,,,"Halesworth, England"
2,0,"Myles, Mr. Thomas Francis",male,62,0,0,240276,9.6875,,Q,,,"Cambridge, MA"
2,0,"Nasser, Mr. Nicholas",male,32.5,1,0,237736,30.0708,,C,,43,"New York, NY"
2,1,"Nasser, Mrs. Nicholas (Adele Achem)",female,14,1,0,237736,30.0708,,C,,,"New York, NY"
2,1,"Navratil, Master. Edmond Roger",male,2,1,1,230080,26,F2,S,D,,"Nice, France"
2,1,"Navratil, Master. Michel M",male,3,1,1,230080,26,F2,S,D,,"Nice, France"
2,0,"Navratil, Mr. Michel ('Louis M Hoffman')",male,36.5,0,2,230080,26,F2,S,,15,"Nice, France"
2,0,"Nesson, Mr. Israel",male,26,0,0,244368,13,F2,S,,,"Boston, MA"
2,0,"Nicholls, Mr. Joseph Charles",male,19,1,1,C.A. 33112,36.75,,S,,101,"Cornwall / Hancock, MI"
2,0,"Norman, Mr. Robert Douglas",male,28,0,0,218629,13.5,,S,,287,Glasgow
2,1,"Nourney, Mr. Alfred ('Baron von Drachstedt')",male,20,0,0,SC/PARIS 2166,13.8625,D38,C,7,,"Cologne, Germany"
2,1,"Nye, Mrs. (Elizabeth Ramell)",female,29,0,0,C.A. 29395,10.5,F33,S,11,,"Folkstone, Kent / New York, NY"
2,0,"Otter, Mr. Richard",male,39,0,0,28213,13,,S,,,"Middleburg Heights, OH"
2,1,"Oxenham, Mr. Percy Thomas",male,22,0,0,W./C. 14260,10.5,,S,13,,"Pondersend, England / New Durham, NJ"
2,1,"Padro y Manent, Mr. Julian",male,,0,0,SC/PARIS 2146,13.8625,,C,9,,"Spain / Havana, Cuba"
2,0,"Pain, Dr. Alfred",male,23,0,0,244278,10.5,,S,,,"Hamilton, ON"
2,1,"Pallas y Castello, Mr. Emilio",male,29,0,0,SC/PARIS 2147,13.8583,,C,9,,"Spain / Havana, Cuba"
2,0,"Parker, Mr. Clifford Richard",male,28,0,0,SC 14888,10.5,,S,,,"St Andrews, Guernsey"
2,0,"Parkes, Mr. Francis "'Frank'",male,,0,0,239853,0,,S,,,Belfast
2,1,"Parrish, Mrs. (Lutie Davis)",female,50,0,1,230433,26,,S,12,,"Woodford County, KY"
2,0,"Pengelly, Mr. Frederick William",male,19,0,0,28665,10.5,,S,,,"Gunnislake, England / Butte, MT"
2,0,"Pernot, Mr. Rene",male,,0,0,SC/PARIS 2131,15.05,,C,,,
2,0,"Peruschitz, Rev. Joseph Maria",male,41,0,0,237393,13,,S,,,
2,1,"Phillips, Miss. Alice Frances Louisa",female,21,0,1,S.O./P.P. 2,21,,S,12,,"Ilfracombe, Devon"
2,1,"Phillips, Miss. Kate Florence ('Mrs Kate Louise Phillips Marshall')",female,19,0,0,250655,26,,S,11,,"Worcester, England"
2,0,"Phillips, Mr. Escott Robert",male,43,0,1,S.O./P.P. 2,21,,S,,,"Ilfracombe, Devon"
2,1,"Pinsky, Mrs. (Rosa)",female,32,0,0,234604,13,,S,9,,Russia
2,0,"Ponesell, Mr. Martin",male,34,0,0,250647,13,,S,,,"Denmark / New York, NY"
2,1,"Portaluppi, Mr. Emilio Ilario Giuseppe",male,30,0,0,C.A. 34644,12.7375,,C,14,,"Milford, NH"
2,0,"Pulbaum, Mr. Franz",male,27,0,0,SC/PARIS 2168,15.0333,,C,,,Paris
2,1,"Quick, Miss. Phyllis May",female,2,1,1,26360,26,,S,11,,"Plymouth, Devon / Detroit, MI"
2,1,"Quick, Miss. Winifred Vera",female,8,1,1,26360,26,,S,11,,"Plymouth, Devon / Detroit, MI"
2,1,"Quick, Mrs. Frederick Charles (Jane Richards)",female,33,0,2,26360,26,,S,11,,"Plymouth, Devon / Detroit, MI"
2,0,"Reeves, Mr. David",male,36,0,0,C.A. 17248,10.5,,S,,,"Brighton, Sussex"
2,0,"Renouf, Mr. Peter Henry",male,34,1,0,31027,21,,S,12,,"Elizabeth, NJ"
2,1,"Renouf, Mrs. Peter Henry (Lillian Jefferys)",female,30,3,0,31027,21,,S,,,"Elizabeth, NJ"
2,1,"Reynaldo, Ms. Encarnacion",female,28,0,0,230434,13,,S,9,,Spain
2,0,"Richard, Mr. Emile",male,23,0,0,SC/PARIS 2133,15.0458,,C,,,"Paris / Montreal, PQ"
2,1,"Richards, Master. George Sibley",male,0.8333,1,1,29106,18.75,,S,4,,"Cornwall / Akron, OH"
2,1,"Richards, Master. William Rowe",male,3,1,1,29106,18.75,,S,4,,"Cornwall / Akron, OH"
2,1,"Richards, Mrs. Sidney (Emily Hocking)",female,24,2,3,29106,18.75,,S,4,,"Cornwall / Akron, OH"
2,1,"Ridsdale, Miss. Lucy",female,50,0,0,W./C. 14258,10.5,,S,13,,"London, England / Marietta, Ohio and Milwaukee, WI"
2,0,"Rogers, Mr. Reginald Harry",male,19,0,0,28004,10.5,,S,,,
2,1,"Rugg, Miss. Emily",female,21,0,0,C.A. 31026,10.5,,S,12,,"Guernsey / Wilmington, DE"
2,0,"Schmidt, Mr. August",male,26,0,0,248659,13,,S,,,"Newark, NJ"
2,0,"Sedgwick, Mr. Charles Frederick Waddington",male,25,0,0,244361,13,,S,,,Liverpool
2,0,"Sharp, Mr. Percival James R",male,27,0,0,244358,26,,S,,,"Hornsey, England"
2,1,"Shelley, Mrs. William (Imanita Parrish Hall)",female,25,0,1,230433,26,,S,12,,"Deer Lodge, MT"
2,1,"Silven, Miss. Lyyli Karoliina",female,18,0,2,250652,13,,S,16,,"Finland / Minneapolis, MN"
2,1,"Sincock, Miss. Maude",female,20,0,0,C.A. 33112,36.75,,S,11,,"Cornwall / Hancock, MI"
2,1,"Sinkkonen, Miss. Anna",female,30,0,0,250648,13,,S,10,,"Finland / Washington, DC"
2,0,"Sjostedt, Mr. Ernst Adolf",male,59,0,0,237442,13.5,,S,,,"Sault St Marie, ON"
2,1,"Slayter, Miss. Hilda Mary",female,30,0,0,234818,12.35,,Q,13,,"Halifax, NS"
2,0,"Slemen, Mr. Richard James",male,35,0,0,28206,10.5,,S,,,Cornwall
2,1,"Smith, Miss. Marion Elsie",female,40,0,0,31418,13,,S,9,,
2,0,"Sobey, Mr. Samuel James Hayden",male,25,0,0,C.A. 29178,13,,S,,,"Cornwall / Houghton, MI"
2,0,"Stanton, Mr. Samuel Ward",male,41,0,0,237734,15.0458,,C,,,"New York, NY"
2,0,"Stokes, Mr. Philip Joseph",male,25,0,0,F.C.C. 13540,10.5,,S,,81,"Catford, Kent / Detroit, MI"
2,0,"Swane, Mr. George",male,18.5,0,0,248734,13,F,S,,294,
2,0,"Sweet, Mr. George Frederick",male,14,0,0,220845,65,,S,,,"Somerset / Bernardsville, NJ"
2,1,"Toomey, Miss. Ellen",female,50,0,0,F.C.C. 13531,10.5,,S,9,,"Indianapolis, IN"
2,0,"Troupiansky, Mr. Moses Aaron",male,23,0,0,233639,13,,S,,,
2,1,"Trout, Mrs. William H (Jessie L)",female,28,0,0,240929,12.65,,S,,,"Columbus, OH"
2,1,"Troutt, Miss. Edwina Celia "'Winnie'",female,27,0,0,34218,10.5,E101,S,16,,"Bath, England / Massachusetts"
2,0,"Turpin, Mr. William John Robert",male,29,1,0,11668,21,,S,,,"Plymouth, England"
2,0,"Turpin, Mrs. William John Robert (Dorothy Ann Wonnacott)",female,27,1,0,11668,21,,S,,,"Plymouth, England"
2,0,"Veal, Mr. James",male,40,0,0,28221,13,,S,,,"Barre, Co Washington, VT"
2,1,"Walcroft, Miss. Nellie",female,31,0,0,F.C.C. 13528,21,,S,14,,"Mamaroneck, NY"
2,0,"Ware, Mr. John James",male,30,1,0,CA 31352,21,,S,,,"Bristol, England / New Britain, CT"
2,0,"Ware, Mr. William Jeffery",male,23,1,0,28666,10.5,,S,,,
2,1,"Ware, Mrs. John James (Florence Louise Long)",female,31,0,0,CA 31352,21,,S,10,,"Bristol, England / New Britain, CT"
2,0,"Watson, Mr. Ennis Hastings",male,,0,0,239856,0,,S,,,Belfast
2,1,"Watt, Miss. Bertha J",female,12,0,0,C.A. 33595,15.75,,S,9,,"Aberdeen / Portland, OR"
2,1,"Watt, Mrs. James (Elizabeth 'Bessie' Inglis Milne)",female,40,0,0,C.A. 33595,15.75,,S,9,,"Aberdeen / Portland, OR"
2,1,"Webber, Miss. Susan",female,32.5,0,0,27267,13,E101,S,12,,"England / Hartford, CT"
2,0,"Weisz, Mr. Leopold",male,27,1,0,228414,26,,S,,293,"Bromsgrove, England / Montreal, PQ"
2,1,"Weisz, Mrs. Leopold (Mathilde Francoise Pede)",female,29,1,0,228414,26,,S,10,,"Bromsgrove, England / Montreal, PQ"
2,1,"Wells, Master. Ralph Lester",male,2,1,1,29103,23,,S,14,,"Cornwall / Akron, OH"
2,1,"Wells, Miss. Joan",female,4,1,1,29103,23,,S,14,,"Cornwall / Akron, OH"
2,1,"Wells, Mrs. Arthur Henry ('Addie' Dart Trevaskis)",female,29,0,2,29103,23,,S,14,,"Cornwall / Akron, OH"
2,1,"West, Miss. Barbara J",female,0.9167,1,2,C.A. 34651,27.75,,S,10,,"Bournmouth, England"
2,1,"West, Miss. Constance Mirium",female,5,1,2,C.A. 34651,27.75,,S,10,,"Bournmouth, England"
2,0,"West, Mr. Edwy Arthur",male,36,1,2,C.A. 34651,27.75,,S,,,"Bournmouth, England"
2,1,"West, Mrs. Edwy Arthur (Ada Mary Worth)",female,33,1,2,C.A. 34651,27.75,,S,10,,"Bournmouth, England"
2,0,"Wheadon, Mr. Edward H",male,66,0,0,C.A. 24579,10.5,,S,,,"Guernsey, England / Edgewood, RI"
2,0,"Wheeler, Mr. Edwin "'Frederick'",male,,0,0,SC/PARIS 2159,12.875,,S,,,
2,1,"Wilhelms, Mr. Charles",male,31,0,0,244270,13,,S,9,,"London, England"
2,1,"Williams, Mr. Charles Eugene",male,,0,0,244373,13,,S,14,,"Harrow, England"
2,1,"Wright, Miss. Marion",female,26,0,0,220844,13.5,,S,9,,"Yoevil, England / Cottage Grove, OR"
2,0,"Yrois, Miss. Henriette ('Mrs Harbeck')",female,24,0,0,248747,13,,S,,,Paris
3,0,"Abbing, Mr. Anthony",male,42,0,0,C.A. 5547,7.55,,S,,,
3,0,"Abbott, Master. Eugene Joseph",male,13,0,2,C.A. 2673,20.25,,S,,,"East Providence, RI"
3,0,"Abbott, Mr. Rossmore Edward",male,16,1,1,C.A. 2673,20.25,,S,,190,"East Providence, RI"
3,1,"Abbott, Mrs. Stanton (Rosa Hunt)",female,35,1,1,C.A. 2673,20.25,,S,A,,"East Providence, RI"
3,1,"Abelseth, Miss. Karen Marie",female,16,0,0,348125,7.65,,S,16,,"Norway Los Angeles, CA"
3,1,"Abelseth, Mr. Olaus Jorgensen",male,25,0,0,348122,7.65,F G63,S,A,,"Perkins County, SD"
3,1,"Abrahamsson, Mr. Abraham August Johannes",male,20,0,0,SOTON/O2 3101284,7.925,,S,15,,"Taalintehdas, Finland Hoboken, NJ"
3,1,"Abrahim, Mrs. Joseph (Sophie Halaut Easu)",female,18,0,0,2657,7.2292,,C,C,,"Greensburg, PA"
3,0,"Adahl, Mr. Mauritz Nils Martin",male,30,0,0,C 7076,7.25,,S,,72,"Asarum, Sweden Brooklyn, NY"
3,0,"Adams, Mr. John",male,26,0,0,341826,8.05,,S,,103,"Bournemouth, England"
3,0,"Ahlin, Mrs. Johan (Johanna Persdotter Larsson)",female,40,1,0,7546,9.475,,S,,,"Sweden Akeley, MN"
3,1,"Aks, Master. Philip Frank",male,0.8333,0,1,392091,9.35,,S,11,,"London, England Norfolk, VA"
3,1,"Aks, Mrs. Sam (Leah Rosen)",female,18,0,1,392091,9.35,,S,13,,"London, England Norfolk, VA"
3,1,"Albimona, Mr. Nassef Cassem",male,26,0,0,2699,18.7875,,C,15,,"Syria Fredericksburg, VA"
3,0,"Alexander, Mr. William",male,26,0,0,3474,7.8875,,S,,,"England Albion, NY"
3,0,"Alhomaki, Mr. Ilmari Rudolf",male,20,0,0,SOTON/O2 3101287,7.925,,S,,,"Salo, Finland Astoria, OR"
3,0,"Ali, Mr. Ahmed",male,24,0,0,SOTON/O.Q. 3101311,7.05,,S,,,
3,0,"Ali, Mr. William",male,25,0,0,SOTON/O.Q. 3101312,7.05,,S,,79,Argentina
3,0,"Allen, Mr. William Henry",male,35,0,0,373450,8.05,,S,,,"Lower Clapton, Middlesex or Erdington, Birmingham"
3,0,"Allum, Mr. Owen George",male,18,0,0,2223,8.3,,S,,259,"Windsor, England New York, NY"
3,0,"Andersen, Mr. Albert Karvin",male,32,0,0,C 4001,22.525,,S,,260,"Bergen, Norway"
3,1,"Andersen-Jensen, Miss. Carla Christine Nielsine",female,19,1,0,350046,7.8542,,S,16,,
3,0,"Andersson, Master. Sigvard Harald Elias",male,4,4,2,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,0,"Andersson, Miss. Ebba Iris Alfrida",female,6,4,2,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,0,"Andersson, Miss. Ellis Anna Maria",female,2,4,2,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,1,"Andersson, Miss. Erna Alexandra",female,17,4,2,3101281,7.925,,S,D,,"Ruotsinphyhtaa, Finland New York, NY"
3,0,"Andersson, Miss. Ida Augusta Margareta",female,38,4,2,347091,7.775,,S,,,"Vadsbro, Sweden Ministee, MI"
3,0,"Andersson, Miss. Ingeborg Constanzia",female,9,4,2,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,0,"Andersson, Miss. Sigrid Elisabeth",female,11,4,2,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,0,"Andersson, Mr. Anders Johan",male,39,1,5,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,1,"Andersson, Mr. August Edvard ('Wennerstrom')",male,27,0,0,350043,7.7958,,S,A,,
3,0,"Andersson, Mr. Johan Samuel",male,26,0,0,347075,7.775,,S,,,"Hartford, CT"
3,0,"Andersson, Mrs. Anders Johan (Alfrida Konstantia Brogren)",female,39,1,5,347082,31.275,,S,,,"Sweden Winnipeg, MN"
3,0,"Andreasson, Mr. Paul Edvin",male,20,0,0,347466,7.8542,,S,,,"Sweden Chicago, IL"
3,0,"Angheloff, Mr. Minko",male,26,0,0,349202,7.8958,,S,,,"Bulgaria Chicago, IL"
3,0,"Arnold-Franchi, Mr. Josef",male,25,1,0,349237,17.8,,S,,,"Altdorf, Switzerland"
3,0,"Arnold-Franchi, Mrs. Josef (Josefine Franchi)",female,18,1,0,349237,17.8,,S,,,"Altdorf, Switzerland"
3,0,"Aronsson, Mr. Ernst Axel Algot",male,24,0,0,349911,7.775,,S,,,"Sweden Joliet, IL"
3,0,"Asim, Mr. Adola",male,35,0,0,SOTON/O.Q. 3101310,7.05,,S,,,
3,0,"Asplund, Master. Carl Edgar",male,5,4,2,347077,31.3875,,S,,,"Sweden  Worcester, MA"
3,0,"Asplund, Master. Clarence Gustaf Hugo",male,9,4,2,347077,31.3875,,S,,,"Sweden Worcester, MA"
3,1,"Asplund, Master. Edvin Rojj Felix",male,3,4,2,347077,31.3875,,S,15,,"Sweden Worcester, MA"
3,0,"Asplund, Master. Filip Oscar",male,13,4,2,347077,31.3875,,S,,,"Sweden Worcester, MA"
3,1,"Asplund, Miss. Lillian Gertrud",female,5,4,2,347077,31.3875,,S,15,,"Sweden Worcester, MA"
3,0,"Asplund, Mr. Carl Oscar Vilhelm Gustafsson",male,40,1,5,347077,31.3875,,S,,142,"Sweden  Worcester, MA"
3,1,"Asplund, Mr. Johan Charles",male,23,0,0,350054,7.7958,,S,13,,"Oskarshamn, Sweden Minneapolis, MN"
3,1,"Asplund, Mrs. Carl Oscar (Selma Augusta Emilia Johansson)",female,38,1,5,347077,31.3875,,S,15,,"Sweden  Worcester, MA"
3,1,"Assaf Khalil, Mrs. Mariana ('Miriam')",female,45,0,0,2696,7.225,,C,C,,"Ottawa, ON"
3,0,"Assaf, Mr. Gerios",male,21,0,0,2692,7.225,,C,,,"Ottawa, ON"
3,0,"Assam, Mr. Ali",male,23,0,0,SOTON/O.Q. 3101309,7.05,,S,,,
3,0,"Attalah, Miss. Malake",female,17,0,0,2627,14.4583,,C,,,
3,0,"Attalah, Mr. Sleiman",male,30,0,0,2694,7.225,,C,,,"Ottawa, ON"
3,0,"Augustsson, Mr. Albert",male,23,0,0,347468,7.8542,,S,,,"Krakoryd, Sweden Bloomington, IL"
3,1,"Ayoub, Miss. Banoura",female,13,0,0,2687,7.2292,,C,C,,"Syria Youngstown, OH"
3,0,"Baccos, Mr. Raffull",male,20,0,0,2679,7.225,,C,,,
3,0,"Backstrom, Mr. Karl Alfred",male,32,1,0,3101278,15.85,,S,D,,"Ruotsinphytaa, Finland New York, NY"
3,1,"Backstrom, Mrs. Karl Alfred (Maria Mathilda Gustafsson)",female,33,3,0,3101278,15.85,,S,,,"Ruotsinphytaa, Finland New York, NY"
3,1,"Baclini, Miss. Eugenie",female,0.75,2,1,2666,19.2583,,C,C,,"Syria New York, NY"
3,1,"Baclini, Miss. Helene Barbara",female,0.75,2,1,2666,19.2583,,C,C,,"Syria New York, NY"
3,1,"Baclini, Miss. Marie Catherine",female,5,2,1,2666,19.2583,,C,C,,"Syria New York, NY"
3,1,"Baclini, Mrs. Solomon (Latifa Qurban)",female,24,0,3,2666,19.2583,,C,C,,"Syria New York, NY"
3,1,"Badman, Miss. Emily Louisa",female,18,0,0,A/4 31416,8.05,,S,C,,"London Skanteales, NY"
3,0,"Badt, Mr. Mohamed",male,40,0,0,2623,7.225,,C,,,
3,0,"Balkic, Mr. Cerin",male,26,0,0,349248,7.8958,,S,,,
3,1,"Barah, Mr. Hanna Assi",male,20,0,0,2663,7.2292,,C,15,,
3,0,"Barbara, Miss. Saiide",female,18,0,1,2691,14.4542,,C,,,"Syria Ottawa, ON"
3,0,"Barbara, Mrs. (Catherine David)",female,45,0,1,2691,14.4542,,C,,,"Syria Ottawa, ON"
3,0,"Barry, Miss. Julia",female,27,0,0,330844,7.8792,,Q,,,"New York, NY"
3,0,"Barton, Mr. David John",male,22,0,0,324669,8.05,,S,,,"England New York, NY"
3,0,"Beavan, Mr. William Thomas",male,19,0,0,323951,8.05,,S,,,England
3,0,"Bengtsson, Mr. John Viktor",male,26,0,0,347068,7.775,,S,,,"Krakudden, Sweden Moune, IL"
3,0,"Berglund, Mr. Karl Ivar Sven",male,22,0,0,PP 4348,9.35,,S,,,"Tranvik, Finland New York"
3,0,"Betros, Master. Seman",male,,0,0,2622,7.2292,,C,,,
3,0,"Betros, Mr. Tannous",male,20,0,0,2648,4.0125,,C,,,Syria
3,1,"Bing, Mr. Lee",male,32,0,0,1601,56.4958,,S,C,,"Hong Kong New York, NY"
3,0,"Birkeland, Mr. Hans Martin Monsen",male,21,0,0,312992,7.775,,S,,,"Brennes, Norway New York"
3,0,"Bjorklund, Mr. Ernst Herbert",male,18,0,0,347090,7.75,,S,,,"Stockholm, Sweden New York"
3,0,"Bostandyeff, Mr. Guentcho",male,26,0,0,349224,7.8958,,S,,,"Bulgaria Chicago, IL"
3,0,"Boulos, Master. Akar",male,6,1,1,2678,15.2458,,C,,,"Syria Kent, ON"
3,0,"Boulos, Miss. Nourelain",female,9,1,1,2678,15.2458,,C,,,"Syria Kent, ON"
3,0,"Boulos, Mr. Hanna",male,,0,0,2664,7.225,,C,,,Syria
3,0,"Boulos, Mrs. Joseph (Sultana)",female,,0,2,2678,15.2458,,C,,,"Syria Kent, ON"
3,0,"Bourke, Miss. Mary",female,,0,2,364848,7.75,,Q,,,"Ireland Chicago, IL"
3,0,"Bourke, Mr. John",male,40,1,1,364849,15.5,,Q,,,"Ireland Chicago, IL"
3,0,"Bourke, Mrs. John (Catherine)",female,32,1,1,364849,15.5,,Q,,,"Ireland Chicago, IL"
3,0,"Bowen, Mr. David John "'Dai'",male,21,0,0,54636,16.1,,S,,,"Treherbert, Cardiff, Wales"
3,1,"Bradley, Miss. Bridget Delia",female,22,0,0,334914,7.725,,Q,13,,"Kingwilliamstown, Co Cork, Ireland Glens Falls, NY"
3,0,"Braf, Miss. Elin Ester Maria",female,20,0,0,347471,7.8542,,S,,,"Medeltorp, Sweden Chicago, IL"
3,0,"Braund, Mr. Lewis Richard",male,29,1,0,3460,7.0458,,S,,,"Bridgerule, Devon"
3,0,"Braund, Mr. Owen Harris",male,22,1,0,A/5 21171,7.25,,S,,,"Bridgerule, Devon"
3,0,"Brobeck, Mr. Karl Rudolf",male,22,0,0,350045,7.7958,,S,,,"Sweden Worcester, MA"
3,0,"Brocklebank, Mr. William Alfred",male,35,0,0,364512,8.05,,S,,,"Broomfield, Chelmsford, England"
3,0,"Buckley, Miss. Katherine",female,18.5,0,0,329944,7.2833,,Q,,299,"Co Cork, Ireland Roxbury, MA"
3,1,"Buckley, Mr. Daniel",male,21,0,0,330920,7.8208,,Q,13,,"Kingwilliamstown, Co Cork, Ireland New York, NY"
3,0,"Burke, Mr. Jeremiah",male,19,0,0,365222,6.75,,Q,,,"Co Cork, Ireland Charlestown, MA"
3,0,"Burns, Miss. Mary Delia",female,18,0,0,330963,7.8792,,Q,,,"Co Sligo, Ireland New York, NY"
3,0,"Cacic, Miss. Manda",female,21,0,0,315087,8.6625,,S,,,
3,0,"Cacic, Miss. Marija",female,30,0,0,315084,8.6625,,S,,,
3,0,"Cacic, Mr. Jego Grga",male,18,0,0,315091,8.6625,,S,,,
3,0,"Cacic, Mr. Luka",male,38,0,0,315089,8.6625,,S,,,Croatia
3,0,"Calic, Mr. Jovo",male,17,0,0,315093,8.6625,,S,,,
3,0,"Calic, Mr. Petar",male,17,0,0,315086,8.6625,,S,,,
3,0,"Canavan, Miss. Mary",female,21,0,0,364846,7.75,,Q,,,
3,0,"Canavan, Mr. Patrick",male,21,0,0,364858,7.75,,Q,,,"Ireland Philadelphia, PA"
3,0,"Cann, Mr. Ernest Charles",male,21,0,0,A./5. 2152,8.05,,S,,,
3,0,"Caram, Mr. Joseph",male,,1,0,2689,14.4583,,C,,,"Ottawa, ON"
3,0,"Caram, Mrs. Joseph (Maria Elias)",female,,1,0,2689,14.4583,,C,,,"Ottawa, ON"
3,0,"Carlsson, Mr. August Sigfrid",male,28,0,0,350042,7.7958,,S,,,"Dagsas, Sweden Fower, MN"
3,0,"Carlsson, Mr. Carl Robert",male,24,0,0,350409,7.8542,,S,,,"Goteborg, Sweden Huntley, IL"
3,1,"Carr, Miss. Helen "'Ellen'",female,16,0,0,367231,7.75,,Q,16,,"Co Longford, Ireland New York, NY"
3,0,"Carr, Miss. Jeannie",female,37,0,0,368364,7.75,,Q,,,"Co Sligo, Ireland Hartford, CT"
3,0,"Carver, Mr. Alfred John",male,28,0,0,392095,7.25,,S,,,"St Denys, Southampton, Hants"
3,0,"Celotti, Mr. Francesco",male,24,0,0,343275,8.05,,S,,,London
3,0,"Charters, Mr. David",male,21,0,0,A/5. 13032,7.7333,,Q,,,"Ireland New York, NY"
3,1,"Chip, Mr. Chang",male,32,0,0,1601,56.4958,,S,C,,"Hong Kong New York, NY"
3,0,"Christmann, Mr. Emil",male,29,0,0,343276,8.05,,S,,,
3,0,"Chronopoulos, Mr. Apostolos",male,26,1,0,2680,14.4542,,C,,,Greece
3,0,"Chronopoulos, Mr. Demetrios",male,18,1,0,2680,14.4542,,C,,,Greece
3,0,"Coelho, Mr. Domingos Fernandeo",male,20,0,0,SOTON/O.Q. 3101307,7.05,,S,,,Portugal
3,1,"Cohen, Mr. Gurshon "'Gus'",male,18,0,0,A/5 3540,8.05,,S,12,,"London Brooklyn, NY"
3,0,"Colbert, Mr. Patrick",male,24,0,0,371109,7.25,,Q,,,"Co Limerick, Ireland Sherbrooke, PQ"
3,0,"Coleff, Mr. Peju",male,36,0,0,349210,7.4958,,S,,,"Bulgaria Chicago, IL"
3,0,"Coleff, Mr. Satio",male,24,0,0,349209,7.4958,,S,,,
3,0,"Conlon, Mr. Thomas Henry",male,31,0,0,21332,7.7333,,Q,,,"Philadelphia, PA"
3,0,"Connaghton, Mr. Michael",male,31,0,0,335097,7.75,,Q,,,"Ireland Brooklyn, NY"
3,1,"Connolly, Miss. Kate",female,22,0,0,370373,7.75,,Q,13,,Ireland
3,0,"Connolly, Miss. Kate",female,30,0,0,330972,7.6292,,Q,,,Ireland
3,0,"Connors, Mr. Patrick",male,70.5,0,0,370369,7.75,,Q,,171,
3,0,"Cook, Mr. Jacob",male,43,0,0,A/5 3536,8.05,,S,,,
3,0,"Cor, Mr. Bartol",male,35,0,0,349230,7.8958,,S,,,Austria
3,0,"Cor, Mr. Ivan",male,27,0,0,349229,7.8958,,S,,,Austria
3,0,"Cor, Mr. Liudevit",male,19,0,0,349231,7.8958,,S,,,Austria
3,0,"Corn, Mr. Harry",male,30,0,0,SOTON/OQ 392090,8.05,,S,,,London
3,1,"Coutts, Master. Eden Leslie "'Neville'",male,9,1,1,C.A. 37671,15.9,,S,2,,"England Brooklyn, NY"
3,1,"Coutts, Master. William Loch "'William'",male,3,1,1,C.A. 37671,15.9,,S,2,,"England Brooklyn, NY"
3,1,"Coutts, Mrs. William (Winnie 'Minnie' Treanor)",female,36,0,2,C.A. 37671,15.9,,S,2,,"England Brooklyn, NY"
3,0,"Coxon, Mr. Daniel",male,59,0,0,364500,7.25,,S,,,"Merrill, WI"
3,0,"Crease, Mr. Ernest James",male,19,0,0,S.P. 3464,8.1583,,S,,,"Bristol, England Cleveland, OH"
3,1,"Cribb, Miss. Laura Alice",female,17,0,1,371362,16.1,,S,12,,"Bournemouth, England Newark, NJ"
3,0,"Cribb, Mr. John Hatfield",male,44,0,1,371362,16.1,,S,,,"Bournemouth, England Newark, NJ"
3,0,"Culumovic, Mr. Jeso",male,17,0,0,315090,8.6625,,S,,,Austria-Hungary
3,0,"Daher, Mr. Shedid",male,22.5,0,0,2698,7.225,,C,,9,
3,1,"Dahl, Mr. Karl Edwart",male,45,0,0,7598,8.05,,S,15,,"Australia Fingal, ND"
3,0,"Dahlberg, Miss. Gerda Ulrika",female,22,0,0,7552,10.5167,,S,,,"Norrlot, Sweden Chicago, IL"
3,0,"Dakic, Mr. Branko",male,19,0,0,349228,10.1708,,S,,,Austria
3,1,"Daly, Miss. Margaret Marcella "'Maggie'",female,30,0,0,382650,6.95,,Q,15,,"Co Athlone, Ireland New York, NY"
3,1,"Daly, Mr. Eugene Patrick",male,29,0,0,382651,7.75,,Q,13 15 B,,"Co Athlone, Ireland New York, NY"
3,0,"Danbom, Master. Gilbert Sigvard Emanuel",male,0.3333,0,2,347080,14.4,,S,,,"Stanton, IA"
3,0,"Danbom, Mr. Ernst Gilbert",male,34,1,1,347080,14.4,,S,,197,"Stanton, IA"
3,0,"Danbom, Mrs. Ernst Gilbert (Anna Sigrid Maria Brogren)",female,28,1,1,347080,14.4,,S,,,"Stanton, IA"
3,0,"Danoff, Mr. Yoto",male,27,0,0,349219,7.8958,,S,,,"Bulgaria Chicago, IL"
3,0,"Dantcheff, Mr. Ristiu",male,25,0,0,349203,7.8958,,S,,,"Bulgaria Chicago, IL"
3,0,"Davies, Mr. Alfred J",male,24,2,0,A/4 48871,24.15,,S,,,"West Bromwich, England Pontiac, MI"
3,0,"Davies, Mr. Evan",male,22,0,0,SC/A4 23568,8.05,,S,,,
3,0,"Davies, Mr. John Samuel",male,21,2,0,A/4 48871,24.15,,S,,,"West Bromwich, England Pontiac, MI"
3,0,"Davies, Mr. Joseph",male,17,2,0,A/4 48873,8.05,,S,,,"West Bromwich, England Pontiac, MI"
3,0,"Davison, Mr. Thomas Henry",male,,1,0,386525,16.1,,S,,,"Liverpool, England Bedford, OH"
3,1,"Davison, Mrs. Thomas Henry (Mary E Finck)",female,,1,0,386525,16.1,,S,16,,"Liverpool, England Bedford, OH"
3,1,"de Messemaeker, Mr. Guillaume Joseph",male,36.5,1,0,345572,17.4,,S,15,,"Tampico, MT"
3,1,"de Messemaeker, Mrs. Guillaume Joseph (Emma)",female,36,1,0,345572,17.4,,S,13,,"Tampico, MT"
3,1,"de Mulder, Mr. Theodore",male,30,0,0,345774,9.5,,S,11,,"Belgium Detroit, MI"
3,0,"de Pelsmaeker, Mr. Alfons",male,16,0,0,345778,9.5,,S,,,
3,1,"Dean, Master. Bertram Vere",male,1,1,2,C.A. 2315,20.575,,S,10,,"Devon, England Wichita, KS"
3,1,"Dean, Miss. Elizabeth Gladys "'Millvina'",female,0.1667,1,2,C.A. 2315,20.575,,S,10,,"Devon, England Wichita, KS"
3,0,"Dean, Mr. Bertram Frank",male,26,1,2,C.A. 2315,20.575,,S,,,"Devon, England Wichita, KS"
3,1,"Dean, Mrs. Bertram (Eva Georgetta Light)",female,33,1,2,C.A. 2315,20.575,,S,10,,"Devon, England Wichita, KS"
3,0,"Delalic, Mr. Redjo",male,25,0,0,349250,7.8958,,S,,,
3,0,"Demetri, Mr. Marinko",male,,0,0,349238,7.8958,,S,,,
3,0,"Denkoff, Mr. Mitto",male,,0,0,349225,7.8958,,S,,,"Bulgaria Coon Rapids, IA"
3,0,"Dennis, Mr. Samuel",male,22,0,0,A/5 21172,7.25,,S,,,
3,0,"Dennis, Mr. William",male,36,0,0,A/5 21175,7.25,,S,,,
3,1,"Devaney, Miss. Margaret Delia",female,19,0,0,330958,7.8792,,Q,C,,"Kilmacowen, Co Sligo, Ireland New York, NY"
3,0,"Dika, Mr. Mirko",male,17,0,0,349232,7.8958,,S,,,
3,0,"Dimic, Mr. Jovan",male,42,0,0,315088,8.6625,,S,,,
3,0,"Dintcheff, Mr. Valtcho",male,43,0,0,349226,7.8958,,S,,,
3,0,"Doharr, Mr. Tannous",male,,0,0,2686,7.2292,,C,,,
3,0,"Dooley, Mr. Patrick",male,32,0,0,370376,7.75,,Q,,,"Ireland New York, NY"
3,1,"Dorking, Mr. Edward Arthur",male,19,0,0,A/5. 10482,8.05,,S,B,,"England Oglesby, IL"
3,1,"Dowdell, Miss. Elizabeth",female,30,0,0,364516,12.475,,S,13,,"Union Hill, NJ"
3,0,"Doyle, Miss. Elizabeth",female,24,0,0,368702,7.75,,Q,,,"Ireland New York, NY"
3,1,"Drapkin, Miss. Jennie",female,23,0,0,SOTON/OQ 392083,8.05,,S,,,"London New York, NY"
3,0,"Drazenoic, Mr. Jozef",male,33,0,0,349241,7.8958,,C,,51,"Austria Niagara Falls, NY"
3,0,"Duane, Mr. Frank",male,65,0,0,336439,7.75,,Q,,,
3,1,"Duquemin, Mr. Joseph",male,24,0,0,S.O./P.P. 752,7.55,,S,D,,"England Albion, NY"
3,0,"Dyker, Mr. Adolf Fredrik",male,23,1,0,347072,13.9,,S,,,"West Haven, CT"
3,1,"Dyker, Mrs. Adolf Fredrik (Anna Elisabeth Judith Andersson)",female,22,1,0,347072,13.9,,S,16,,"West Haven, CT"
3,0,"Edvardsson, Mr. Gustaf Hjalmar",male,18,0,0,349912,7.775,,S,,,"Tofta, Sweden Joliet, IL"
3,0,"Eklund, Mr. Hans Linus",male,16,0,0,347074,7.775,,S,,,"Karberg, Sweden Jerome Junction, AZ"
3,0,"Ekstrom, Mr. Johan",male,45,0,0,347061,6.975,,S,,,"Effington Rut, SD"
3,0,"Elias, Mr. Dibo",male,,0,0,2674,7.225,,C,,,
3,0,"Elias, Mr. Joseph",male,39,0,2,2675,7.2292,,C,,,"Syria Ottawa, ON"
3,0,"Elias, Mr. Joseph Jr",male,17,1,1,2690,7.2292,,C,,,
3,0,"Elias, Mr. Tannous",male,15,1,1,2695,7.2292,,C,,,Syria
3,0,"Elsbury, Mr. William James",male,47,0,0,A/5 3902,7.25,,S,,,"Illinois, USA"
3,1,"Emanuel, Miss. Virginia Ethel",female,5,0,0,364516,12.475,,S,13,,"New York, NY"
3,0,"Emir, Mr. Farred Chehab",male,,0,0,2631,7.225,,C,,,
3,0,"Everett, Mr. Thomas James",male,40.5,0,0,C.A. 6212,15.1,,S,,187,
3,0,"Farrell, Mr. James",male,40.5,0,0,367232,7.75,,Q,,68,"Aughnacliff, Co Longford, Ireland New York, NY"
3,1,"Finoli, Mr. Luigi",male,,0,0,SOTON/O.Q. 3101308,7.05,,S,15,,"Italy Philadelphia, PA"
3,0,"Fischer, Mr. Eberhard Thelander",male,18,0,0,350036,7.7958,,S,,,
3,0,"Fleming, Miss. Honora",female,,0,0,364859,7.75,,Q,,,
3,0,"Flynn, Mr. James",male,,0,0,364851,7.75,,Q,,,
3,0,"Flynn, Mr. John",male,,0,0,368323,6.95,,Q,,,
3,0,"Foley, Mr. Joseph",male,26,0,0,330910,7.8792,,Q,,,"Ireland Chicago, IL"
3,0,"Foley, Mr. William",male,,0,0,365235,7.75,,Q,,,Ireland
3,1,"Foo, Mr. Choong",male,,0,0,1601,56.4958,,S,13,,"Hong Kong New York, NY"
3,0,"Ford, Miss. Doolina Margaret "'Daisy'",female,21,2,2,W./C. 6608,34.375,,S,,,"Rotherfield, Sussex, England Essex Co, MA"
3,0,"Ford, Miss. Robina Maggie "'Ruby'",female,9,2,2,W./C. 6608,34.375,,S,,,"Rotherfield, Sussex, England Essex Co, MA"
3,0,"Ford, Mr. Arthur",male,,0,0,A/5 1478,8.05,,S,,,"Bridgwater, Somerset, England"
3,0,"Ford, Mr. Edward Watson",male,18,2,2,W./C. 6608,34.375,,S,,,"Rotherfield, Sussex, England Essex Co, MA"
3,0,"Ford, Mr. William Neal",male,16,1,3,W./C. 6608,34.375,,S,,,"Rotherfield, Sussex, England Essex Co, MA"
3,0,"Ford, Mrs. Edward (Margaret Ann Watson)",female,48,1,3,W./C. 6608,34.375,,S,,,"Rotherfield, Sussex, England Essex Co, MA"
3,0,"Fox, Mr. Patrick",male,,0,0,368573,7.75,,Q,,,"Ireland New York, NY"
3,0,"Franklin, Mr. Charles (Charles Fardon)",male,,0,0,SOTON/O.Q. 3101314,7.25,,S,,,
3,0,"Gallagher, Mr. Martin",male,25,0,0,36864,7.7417,,Q,,,"New York, NY"
3,0,"Garfirth, Mr. John",male,,0,0,358585,14.5,,S,,,
3,0,"Gheorgheff, Mr. Stanio",male,,0,0,349254,7.8958,,C,,,
3,0,"Gilinski, Mr. Eliezer",male,22,0,0,14973,8.05,,S,,47,
3,1,"Gilnagh, Miss. Katherine "'Katie'",female,16,0,0,35851,7.7333,,Q,16,,"Co Longford, Ireland New York, NY"
3,1,"Glynn, Miss. Mary Agatha",female,,0,0,335677,7.75,,Q,13,,"Co Clare, Ireland Washington, DC"
3,1,"Goldsmith, Master. Frank John William "'Frankie'",male,9,0,2,363291,20.525,,S,C D,,"Strood, Kent, England Detroit, MI"
3,0,"Goldsmith, Mr. Frank John",male,33,1,1,363291,20.525,,S,,,"Strood, Kent, England Detroit, MI"
3,0,"Goldsmith, Mr. Nathan",male,41,0,0,SOTON/O.Q. 3101263,7.85,,S,,,"Philadelphia, PA"
3,1,"Goldsmith, Mrs. Frank John (Emily Alice Brown)",female,31,1,1,363291,20.525,,S,C D,,"Strood, Kent, England Detroit, MI"
3,0,"Goncalves, Mr. Manuel Estanslas",male,38,0,0,SOTON/O.Q. 3101306,7.05,,S,,,Portugal
3,0,"Goodwin, Master. Harold Victor",male,9,5,2,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Master. Sidney Leonard",male,1,5,2,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Master. William Frederick",male,11,5,2,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Miss. Jessie Allis",female,10,5,2,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Miss. Lillian Amy",female,16,5,2,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Mr. Charles Edward",male,14,5,2,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Mr. Charles Frederick",male,40,1,6,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Goodwin, Mrs. Frederick (Augusta Tyler)",female,43,1,6,CA 2144,46.9,,S,,,"Wiltshire, England Niagara Falls, NY"
3,0,"Green, Mr. George Henry",male,51,0,0,21440,8.05,,S,,,"Dorking, Surrey, England"
3,0,"Gronnestad, Mr. Daniel Danielsen",male,32,0,0,8471,8.3625,,S,,,"Foresvik, Norway Portland, ND"
3,0,"Guest, Mr. Robert",male,,0,0,376563,8.05,,S,,,
3,0,"Gustafsson, Mr. Alfred Ossian",male,20,0,0,7534,9.8458,,S,,,"Waukegan, Chicago, IL"
3,0,"Gustafsson, Mr. Anders Vilhelm",male,37,2,0,3101276,7.925,,S,,98,"Ruotsinphytaa, Finland New York, NY"
3,0,"Gustafsson, Mr. Johan Birger",male,28,2,0,3101277,7.925,,S,,,"Ruotsinphytaa, Finland New York, NY"
3,0,"Gustafsson, Mr. Karl Gideon",male,19,0,0,347069,7.775,,S,,,"Myren, Sweden New York, NY"
3,0,"Haas, Miss. Aloisia",female,24,0,0,349236,8.85,,S,,,
3,0,"Hagardon, Miss. Kate",female,17,0,0,AQ/3. 30631,7.7333,,Q,,,
3,0,"Hagland, Mr. Ingvald Olai Olsen",male,,1,0,65303,19.9667,,S,,,
3,0,"Hagland, Mr. Konrad Mathias Reiersen",male,,1,0,65304,19.9667,,S,,,
3,0,"Hakkarainen, Mr. Pekka Pietari",male,28,1,0,STON/O2. 3101279,15.85,,S,,,
3,1,"Hakkarainen, Mrs. Pekka Pietari (Elin Matilda Dolck)",female,24,1,0,STON/O2. 3101279,15.85,,S,15,,
3,0,"Hampe, Mr. Leon",male,20,0,0,345769,9.5,,S,,,
3,0,"Hanna, Mr. Mansour",male,23.5,0,0,2693,7.2292,,C,,188,
3,0,"Hansen, Mr. Claus Peter",male,41,2,0,350026,14.1083,,S,,,
3,0,"Hansen, Mr. Henrik Juul",male,26,1,0,350025,7.8542,,S,,,
3,0,"Hansen, Mr. Henry Damsgaard",male,21,0,0,350029,7.8542,,S,,69,
3,1,"Hansen, Mrs. Claus Peter (Jennie L Howard)",female,45,1,0,350026,14.1083,,S,11,,
3,0,"Harknett, Miss. Alice Phoebe",female,,0,0,W./C. 6609,7.55,,S,,,
3,0,"Harmer, Mr. Abraham (David Lishin)",male,25,0,0,374887,7.25,,S,B,,
3,0,"Hart, Mr. Henry",male,,0,0,394140,6.8583,,Q,,,
3,0,"Hassan, Mr. Houssein G N",male,11,0,0,2699,18.7875,,C,,,
3,1,"Healy, Miss. Hanora "'Nora'",female,,0,0,370375,7.75,,Q,16,,
3,1,"Hedman, Mr. Oskar Arvid",male,27,0,0,347089,6.975,,S,15,,
3,1,"Hee, Mr. Ling",male,,0,0,1601,56.4958,,S,C,,
3,0,"Hegarty, Miss. Hanora "'Nora'",female,18,0,0,365226,6.75,,Q,,,
3,1,"Heikkinen, Miss. Laina",female,26,0,0,STON/O2. 3101282,7.925,,S,,,
3,0,"Heininen, Miss. Wendla Maria",female,23,0,0,STON/O2. 3101290,7.925,,S,,,
3,1,"Hellstrom, Miss. Hilda Maria",female,22,0,0,7548,8.9625,,S,C,,
3,0,"Hendekovic, Mr. Ignjac",male,28,0,0,349243,7.8958,,S,,306,
3,0,"Henriksson, Miss. Jenny Lovisa",female,28,0,0,347086,7.775,,S,,,
3,0,"Henry, Miss. Delia",female,,0,0,382649,7.75,,Q,,,
3,1,"Hirvonen, Miss. Hildur E",female,2,0,1,3101298,12.2875,,S,15,,
3,1,"Hirvonen, Mrs. Alexander (Helga E Lindqvist)",female,22,1,1,3101298,12.2875,,S,15,,
3,0,"Holm, Mr. John Fredrik Alexander",male,43,0,0,C 7075,6.45,,S,,,
3,0,"Holthen, Mr. Johan Martin",male,28,0,0,C 4001,22.525,,S,,,
3,1,"Honkanen, Miss. Eliina",female,27,0,0,STON/O2. 3101283,7.925,,S,,,
3,0,"Horgan, Mr. John",male,,0,0,370377,7.75,,Q,,,
3,1,"Howard, Miss. May Elizabeth",female,,0,0,A. 2. 39186,8.05,,S,C,,
3,0,"Humblen, Mr. Adolf Mathias Nicolai Olsen",male,42,0,0,348121,7.65,F G63,S,,120,
3,1,"Hyman, Mr. Abraham",male,,0,0,3470,7.8875,,S,C,,
3,0,"Ibrahim Shawah, Mr. Yousseff",male,30,0,0,2685,7.2292,,C,,,
3,0,"Ilieff, Mr. Ylio",male,,0,0,349220,7.8958,,S,,,
3,0,"Ilmakangas, Miss. Ida Livija",female,27,1,0,STON/O2. 3101270,7.925,,S,,,
3,0,"Ilmakangas, Miss. Pieta Sofia",female,25,1,0,STON/O2. 3101271,7.925,,S,,,
3,0,"Ivanoff, Mr. Kanio",male,,0,0,349201,7.8958,,S,,,
3,1,"Jalsevac, Mr. Ivan",male,29,0,0,349240,7.8958,,C,15,,
3,1,"Jansson, Mr. Carl Olof",male,21,0,0,350034,7.7958,,S,A,,
3,0,"Jardin, Mr. Jose Neto",male,,0,0,SOTON/O.Q. 3101305,7.05,,S,,,
3,0,"Jensen, Mr. Hans Peder",male,20,0,0,350050,7.8542,,S,,,
3,0,"Jensen, Mr. Niels Peder",male,48,0,0,350047,7.8542,,S,,,
3,0,"Jensen, Mr. Svend Lauritz",male,17,1,0,350048,7.0542,,S,,,
3,1,"Jermyn, Miss. Annie",female,,0,0,14313,7.75,,Q,D,,
3,1,"Johannesen-Bratthammer, Mr. Bernt",male,,0,0,65306,8.1125,,S,13,,
3,0,"Johanson, Mr. Jakob Alfred",male,34,0,0,3101264,6.4958,,S,,143,
3,1,"Johansson Palmquist, Mr. Oskar Leander",male,26,0,0,347070,7.775,,S,15,,
3,0,"Johansson, Mr. Erik",male,22,0,0,350052,7.7958,,S,,156,
3,0,"Johansson, Mr. Gustaf Joel",male,33,0,0,7540,8.6542,,S,,285,
3,0,"Johansson, Mr. Karl Johan",male,31,0,0,347063,7.775,,S,,,
3,0,"Johansson, Mr. Nils",male,29,0,0,347467,7.8542,,S,,,
3,1,"Johnson, Master. Harold Theodor",male,4,1,1,347742,11.1333,,S,15,,
3,1,"Johnson, Miss. Eleanor Ileen",female,1,1,1,347742,11.1333,,S,15,,
3,0,"Johnson, Mr. Alfred",male,49,0,0,LINE,0,,S,,,
3,0,"Johnson, Mr. Malkolm Joackim",male,33,0,0,347062,7.775,,S,,37,
3,0,"Johnson, Mr. William Cahoone Jr",male,19,0,0,LINE,0,,S,,,
3,1,"Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)",female,27,0,2,347742,11.1333,,S,15,,
3,0,"Johnston, Master. William Arthur "'Willie'",male,,1,2,W./C. 6607,23.45,,S,,,
3,0,"Johnston, Miss. Catherine Helen "'Carrie'",female,,1,2,W./C. 6607,23.45,,S,,,
3,0,"Johnston, Mr. Andrew G",male,,1,2,W./C. 6607,23.45,,S,,,
3,0,"Johnston, Mrs. Andrew G (Elizabeth 'Lily' Watson)",female,,1,2,W./C. 6607,23.45,,S,,,
3,0,"Jonkoff, Mr. Lalio",male,23,0,0,349204,7.8958,,S,,,
3,1,"Jonsson, Mr. Carl",male,32,0,0,350417,7.8542,,S,15,,
3,0,"Jonsson, Mr. Nils Hilding",male,27,0,0,350408,7.8542,,S,,,
3,0,"Jussila, Miss. Katriina",female,20,1,0,4136,9.825,,S,,,
3,0,"Jussila, Miss. Mari Aina",female,21,1,0,4137,9.825,,S,,,
3,1,"Jussila, Mr. Eiriik",male,32,0,0,STON/O 2. 3101286,7.925,,S,15,,
3,0,"Kallio, Mr. Nikolai Erland",male,17,0,0,STON/O 2. 3101274,7.125,,S,,,
3,0,"Kalvik, Mr. Johannes Halvorsen",male,21,0,0,8475,8.4333,,S,,,
3,0,"Karaic, Mr. Milan",male,30,0,0,349246,7.8958,,S,,,
3,1,"Karlsson, Mr. Einar Gervasius",male,21,0,0,350053,7.7958,,S,13,,
3,0,"Karlsson, Mr. Julius Konrad Eugen",male,33,0,0,347465,7.8542,,S,,,
3,0,"Karlsson, Mr. Nils August",male,22,0,0,350060,7.5208,,S,,,
3,1,"Karun, Miss. Manca",female,4,0,1,349256,13.4167,,C,15,,
3,1,"Karun, Mr. Franz",male,39,0,1,349256,13.4167,,C,15,,
3,0,"Kassem, Mr. Fared",male,,0,0,2700,7.2292,,C,,,
3,0,"Katavelas, Mr. Vassilios ('Catavelas Vassilios')",male,18.5,0,0,2682,7.2292,,C,,58,
3,0,"Keane, Mr. Andrew "'Andy'",male,,0,0,12460,7.75,,Q,,,
3,0,"Keefe, Mr. Arthur",male,,0,0,323592,7.25,,S,A,,
3,1,"Kelly, Miss. Anna Katherine "'Annie Kate'",female,,0,0,9234,7.75,,Q,16,,
3,1,"Kelly, Miss. Mary",female,,0,0,14312,7.75,,Q,D,,
3,0,"Kelly, Mr. James",male,34.5,0,0,330911,7.8292,,Q,,70,
3,0,"Kelly, Mr. James",male,44,0,0,363592,8.05,,S,,,
3,1,"Kennedy, Mr. John",male,,0,0,368783,7.75,,Q,,,
3,0,"Khalil, Mr. Betros",male,,1,0,2660,14.4542,,C,,,
3,0,"Khalil, Mrs. Betros (Zahie 'Maria' Elias)",female,,1,0,2660,14.4542,,C,,,
3,0,"Kiernan, Mr. John",male,,1,0,367227,7.75,,Q,,,
3,0,"Kiernan, Mr. Philip",male,,1,0,367229,7.75,,Q,,,
3,0,"Kilgannon, Mr. Thomas J",male,,0,0,36865,7.7375,,Q,,,
3,0,"Kink, Miss. Maria",female,22,2,0,315152,8.6625,,S,,,
3,0,"Kink, Mr. Vincenz",male,26,2,0,315151,8.6625,,S,,,
3,1,"Kink-Heilmann, Miss. Luise Gretchen",female,4,0,2,315153,22.025,,S,2,,
3,1,"Kink-Heilmann, Mr. Anton",male,29,3,1,315153,22.025,,S,2,,
3,1,"Kink-Heilmann, Mrs. Anton (Luise Heilmann)",female,26,1,1,315153,22.025,,S,2,,
3,0,"Klasen, Miss. Gertrud Emilia",female,1,1,1,350405,12.1833,,S,,,
3,0,"Klasen, Mr. Klas Albin",male,18,1,1,350404,7.8542,,S,,,
3,0,"Klasen, Mrs. (Hulda Kristina Eugenia Lofqvist)",female,36,0,2,350405,12.1833,,S,,,
3,0,"Kraeff, Mr. Theodor",male,,0,0,349253,7.8958,,C,,,
3,1,"Krekorian, Mr. Neshan",male,25,0,0,2654,7.2292,F E57,C,10,,
3,0,"Lahoud, Mr. Sarkis",male,,0,0,2624,7.225,,C,,,
3,0,"Laitinen, Miss. Kristina Sofia",female,37,0,0,4135,9.5875,,S,,,
3,0,"Laleff, Mr. Kristo",male,,0,0,349217,7.8958,,S,,,
3,1,"Lam, Mr. Ali",male,,0,0,1601,56.4958,,S,C,,
3,0,"Lam, Mr. Len",male,,0,0,1601,56.4958,,S,,,
3,1,"Landergren, Miss. Aurora Adelia",female,22,0,0,C 7077,7.25,,S,13,,
3,0,"Lane, Mr. Patrick",male,,0,0,7935,7.75,,Q,,,
3,1,"Lang, Mr. Fang",male,26,0,0,1601,56.4958,,S,14,,
3,0,"Larsson, Mr. August Viktor",male,29,0,0,7545,9.4833,,S,,,
3,0,"Larsson, Mr. Bengt Edvin",male,29,0,0,347067,7.775,,S,,,
3,0,"Larsson-Rondberg, Mr. Edvard A",male,22,0,0,347065,7.775,,S,,,
3,1,"Leeni, Mr. Fahim ('Philip Zenni')",male,22,0,0,2620,7.225,,C,6,,
3,0,"Lefebre, Master. Henry Forbes",male,,3,1,4133,25.4667,,S,,,
3,0,"Lefebre, Miss. Ida",female,,3,1,4133,25.4667,,S,,,
3,0,"Lefebre, Miss. Jeannie",female,,3,1,4133,25.4667,,S,,,
3,0,"Lefebre, Miss. Mathilde",female,,3,1,4133,25.4667,,S,,,
3,0,"Lefebre, Mrs. Frank (Frances)",female,,0,4,4133,25.4667,,S,,,
3,0,"Leinonen, Mr. Antti Gustaf",male,32,0,0,STON/O 2. 3101292,7.925,,S,,,
3,0,"Lemberopolous, Mr. Peter L",male,34.5,0,0,2683,6.4375,,C,,196,
3,0,"Lennon, Miss. Mary",female,,1,0,370371,15.5,,Q,,,
3,0,"Lennon, Mr. Denis",male,,1,0,370371,15.5,,Q,,,
3,0,"Leonard, Mr. Lionel",male,36,0,0,LINE,0,,S,,,
3,0,"Lester, Mr. James",male,39,0,0,A/4 48871,24.15,,S,,,
3,0,"Lievens, Mr. Rene Aime",male,24,0,0,345781,9.5,,S,,,
3,0,"Lindahl, Miss. Agda Thorilda Viktoria",female,25,0,0,347071,7.775,,S,,,
3,0,"Lindblom, Miss. Augusta Charlotta",female,45,0,0,347073,7.75,,S,,,
3,0,"Lindell, Mr. Edvard Bengtsson",male,36,1,0,349910,15.55,,S,A,,
3,0,"Lindell, Mrs. Edvard Bengtsson (Elin Gerda Persson)",female,30,1,0,349910,15.55,,S,A,,
3,1,"Lindqvist, Mr. Eino William",male,20,1,0,STON/O 2. 3101285,7.925,,S,15,,
3,0,"Linehan, Mr. Michael",male,,0,0,330971,7.8792,,Q,,,
3,0,"Ling, Mr. Lee",male,28,0,0,1601,56.4958,,S,,,
3,0,"Lithman, Mr. Simon",male,,0,0,S.O./P.P. 251,7.55,,S,,,
3,0,"Lobb, Mr. William Arthur",male,30,1,0,A/5. 3336,16.1,,S,,,
3,0,"Lobb, Mrs. William Arthur (Cordelia K Stanlick)",female,26,1,0,A/5. 3336,16.1,,S,,,
3,0,"Lockyer, Mr. Edward",male,,0,0,1222,7.8792,,S,,153,
3,0,"Lovell, Mr. John Hall ('Henry')",male,20.5,0,0,A/5 21173,7.25,,S,,,
3,1,"Lulic, Mr. Nikola",male,27,0,0,315098,8.6625,,S,15,,
3,0,"Lundahl, Mr. Johan Svensson",male,51,0,0,347743,7.0542,,S,,,
3,1,"Lundin, Miss. Olga Elida",female,23,0,0,347469,7.8542,,S,10,,
3,1,"Lundstrom, Mr. Thure Edvin",male,32,0,0,350403,7.5792,,S,15,,
3,0,"Lyntakoff, Mr. Stanko",male,,0,0,349235,7.8958,,S,,,
3,0,"MacKay, Mr. George William",male,,0,0,C.A. 42795,7.55,,S,,,
3,1,"Madigan, Miss. Margaret "'Maggie'",female,,0,0,370370,7.75,,Q,15,,
3,1,"Madsen, Mr. Fridtjof Arne",male,24,0,0,C 17369,7.1417,,S,13,,
3,0,"Maenpaa, Mr. Matti Alexanteri",male,22,0,0,STON/O 2. 3101275,7.125,,S,,,
3,0,"Mahon, Miss. Bridget Delia",female,,0,0,330924,7.8792,,Q,,,
3,0,"Mahon, Mr. John",male,,0,0,AQ/4 3130,7.75,,Q,,,
3,0,"Maisner, Mr. Simon",male,,0,0,A/S 2816,8.05,,S,,,
3,0,"Makinen, Mr. Kalle Edvard",male,29,0,0,STON/O 2. 3101268,7.925,,S,,,
3,1,"Mamee, Mr. Hanna",male,,0,0,2677,7.2292,,C,15,,
3,0,"Mangan, Miss. Mary",female,30.5,0,0,364850,7.75,,Q,,61,
3,1,"Mannion, Miss. Margareth",female,,0,0,36866,7.7375,,Q,16,,
3,0,"Mardirosian, Mr. Sarkis",male,,0,0,2655,7.2292,F E46,C,,,
3,0,"Markoff, Mr. Marin",male,35,0,0,349213,7.8958,,C,,,
3,0,"Markun, Mr. Johann",male,33,0,0,349257,7.8958,,S,,,
3,1,"Masselmani, Mrs. Fatima",female,,0,0,2649,7.225,,C,C,,
3,0,"Matinoff, Mr. Nicola",male,,0,0,349255,7.8958,,C,,,
3,1,"McCarthy, Miss. Catherine "'Katie'",female,,0,0,383123,7.75,,Q,15 16,,
3,1,"McCormack, Mr. Thomas Joseph",male,,0,0,367228,7.75,,Q,,,
3,1,"McCoy, Miss. Agnes",female,,2,0,367226,23.25,,Q,16,,
3,1,"McCoy, Miss. Alicia",female,,2,0,367226,23.25,,Q,16,,
3,1,"McCoy, Mr. Bernard",male,,2,0,367226,23.25,,Q,16,,
3,1,"McDermott, Miss. Brigdet Delia",female,,0,0,330932,7.7875,,Q,13,,
3,0,"McEvoy, Mr. Michael",male,,0,0,36568,15.5,,Q,,,
3,1,"McGovern, Miss. Mary",female,,0,0,330931,7.8792,,Q,13,,
3,1,"McGowan, Miss. Anna "'Annie'",female,15,0,0,330923,8.0292,,Q,,,
3,0,"McGowan, Miss. Katherine",female,35,0,0,9232,7.75,,Q,,,
3,0,"McMahon, Mr. Martin",male,,0,0,370372,7.75,,Q,,,
3,0,"McNamee, Mr. Neal",male,24,1,0,376566,16.1,,S,,,
3,0,"McNamee, Mrs. Neal (Eileen O'Leary)",female,19,1,0,376566,16.1,,S,,53,
3,0,"McNeill, Miss. Bridget",female,,0,0,370368,7.75,,Q,,,
3,0,"Meanwell, Miss. (Marion Ogden)",female,,0,0,SOTON/O.Q. 392087,8.05,,S,,,
3,0,"Meek, Mrs. Thomas (Annie Louise Rowley)",female,,0,0,343095,8.05,,S,,,
3,0,"Meo, Mr. Alfonzo",male,55.5,0,0,A.5. 11206,8.05,,S,,201,
3,0,"Mernagh, Mr. Robert",male,,0,0,368703,7.75,,Q,,,
3,1,"Midtsjo, Mr. Karl Albert",male,21,0,0,345501,7.775,,S,15,,
3,0,"Miles, Mr. Frank",male,,0,0,359306,8.05,,S,,,
3,0,"Mineff, Mr. Ivan",male,24,0,0,349233,7.8958,,S,,,
3,0,"Minkoff, Mr. Lazar",male,21,0,0,349211,7.8958,,S,,,
3,0,"Mionoff, Mr. Stoytcho",male,28,0,0,349207,7.8958,,S,,,
3,0,"Mitkoff, Mr. Mito",male,,0,0,349221,7.8958,,S,,,
3,1,"Mockler, Miss. Helen Mary "'Ellie'",female,,0,0,330980,7.8792,,Q,16,,
3,0,"Moen, Mr. Sigurd Hansen",male,25,0,0,348123,7.65,F G73,S,,309,
3,1,"Moor, Master. Meier",male,6,0,1,392096,12.475,E121,S,14,,
3,1,"Moor, Mrs. (Beila)",female,27,0,1,392096,12.475,E121,S,14,,
3,0,"Moore, Mr. Leonard Charles",male,,0,0,A4. 54510,8.05,,S,,,
3,1,"Moran, Miss. Bertha",female,,1,0,371110,24.15,,Q,16,,
3,0,"Moran, Mr. Daniel J",male,,1,0,371110,24.15,,Q,,,
3,0,"Moran, Mr. James",male,,0,0,330877,8.4583,,Q,,,
3,0,"Morley, Mr. William",male,34,0,0,364506,8.05,,S,,,
3,0,"Morrow, Mr. Thomas Rowan",male,,0,0,372622,7.75,,Q,,,
3,1,"Moss, Mr. Albert Johan",male,,0,0,312991,7.775,,S,B,,
3,1,"Moubarek, Master. Gerios",male,,1,1,2661,15.2458,,C,C,,
3,1,"Moubarek, Master. Halim Gonios ('William George')",male,,1,1,2661,15.2458,,C,C,,
3,1,"Moubarek, Mrs. George (Omine 'Amenia' Alexander)",female,,0,2,2661,15.2458,,C,C,,
3,1,"Moussa, Mrs. (Mantoura Boulos)",female,,0,0,2626,7.2292,,C,,,
3,0,"Moutal, Mr. Rahamin Haim",male,,0,0,374746,8.05,,S,,,
3,1,"Mullens, Miss. Katherine "'Katie'",female,,0,0,35852,7.7333,,Q,16,,
3,1,"Mulvihill, Miss. Bertha E",female,24,0,0,382653,7.75,,Q,15,,
3,0,"Murdlin, Mr. Joseph",male,,0,0,A./5. 3235,8.05,,S,,,
3,1,"Murphy, Miss. Katherine "'Kate'",female,,1,0,367230,15.5,,Q,16,,
3,1,"Murphy, Miss. Margaret Jane",female,,1,0,367230,15.5,,Q,16,,
3,1,"Murphy, Miss. Nora",female,,0,0,36568,15.5,,Q,16,,
3,0,"Myhrman, Mr. Pehr Fabian Oliver Malkolm",male,18,0,0,347078,7.75,,S,,,
3,0,"Naidenoff, Mr. Penko",male,22,0,0,349206,7.8958,,S,,,
3,1,"Najib, Miss. Adele Kiamie "'Jane'",female,15,0,0,2667,7.225,,C,C,,
3,1,"Nakid, Miss. Maria ('Mary')",female,1,0,2,2653,15.7417,,C,C,,
3,1,"Nakid, Mr. Sahid",male,20,1,1,2653,15.7417,,C,C,,
3,1,"Nakid, Mrs. Said (Waika 'Mary' Mowad)",female,19,1,1,2653,15.7417,,C,C,,
3,0,"Nancarrow, Mr. William Henry",male,33,0,0,A./5. 3338,8.05,,S,,,
3,0,"Nankoff, Mr. Minko",male,,0,0,349218,7.8958,,S,,,
3,0,"Nasr, Mr. Mustafa",male,,0,0,2652,7.2292,,C,,,
3,0,"Naughton, Miss. Hannah",female,,0,0,365237,7.75,,Q,,,
3,0,"Nenkoff, Mr. Christo",male,,0,0,349234,7.8958,,S,,,
3,1,"Nicola-Yarred, Master. Elias",male,12,1,0,2651,11.2417,,C,C,,
3,1,"Nicola-Yarred, Miss. Jamila",female,14,1,0,2651,11.2417,,C,C,,
3,0,"Nieminen, Miss. Manta Josefina",female,29,0,0,3101297,7.925,,S,,,
3,0,"Niklasson, Mr. Samuel",male,28,0,0,363611,8.05,,S,,,
3,1,"Nilsson, Miss. Berta Olivia",female,18,0,0,347066,7.775,,S,D,,
3,1,"Nilsson, Miss. Helmina Josefina",female,26,0,0,347470,7.8542,,S,13,,
3,0,"Nilsson, Mr. August Ferdinand",male,21,0,0,350410,7.8542,,S,,,
3,0,"Nirva, Mr. Iisakki Antino Aijo",male,41,0,0,SOTON/O2 3101272,7.125,,S,,,"Finland Sudbury, ON"
3,1,"Niskanen, Mr. Juha",male,39,0,0,STON/O 2. 3101289,7.925,,S,9,,
3,0,"Nosworthy, Mr. Richard Cater",male,21,0,0,A/4. 39886,7.8,,S,,,
3,0,"Novel, Mr. Mansouer",male,28.5,0,0,2697,7.2292,,C,,181,
3,1,"Nysten, Miss. Anna Sofia",female,22,0,0,347081,7.75,,S,13,,
3,0,"Nysveen, Mr. Johan Hansen",male,61,0,0,345364,6.2375,,S,,,
3,0,"O'Brien, Mr. Thomas",male,,1,0,370365,15.5,,Q,,,
3,0,"O'Brien, Mr. Timothy",male,,0,0,330979,7.8292,,Q,,,
3,1,"O'Brien, Mrs. Thomas (Johanna 'Hannah' Godfrey)",female,,1,0,370365,15.5,,Q,,,
3,0,"O'Connell, Mr. Patrick D",male,,0,0,334912,7.7333,,Q,,,
3,0,"O'Connor, Mr. Maurice",male,,0,0,371060,7.75,,Q,,,
3,0,"O'Connor, Mr. Patrick",male,,0,0,366713,7.75,,Q,,,
3,0,"Odahl, Mr. Nils Martin",male,23,0,0,7267,9.225,,S,,,
3,0,"O'Donoghue, Ms. Bridget",female,,0,0,364856,7.75,,Q,,,
3,1,"O'Driscoll, Miss. Bridget",female,,0,0,14311,7.75,,Q,D,,
3,1,"O'Dwyer, Miss. Ellen "'Nellie'",female,,0,0,330959,7.8792,,Q,,,
3,1,"Ohman, Miss. Velin",female,22,0,0,347085,7.775,,S,C,,
3,1,"O'Keefe, Mr. Patrick",male,,0,0,368402,7.75,,Q,B,,
3,1,"O'Leary, Miss. Hanora "'Norah'",female,,0,0,330919,7.8292,,Q,13,,
3,1,"Olsen, Master. Artur Karl",male,9,0,1,C 17368,3.1708,,S,13,,
3,0,"Olsen, Mr. Henry Margido",male,28,0,0,C 4001,22.525,,S,,173,
3,0,"Olsen, Mr. Karl Siegwart Andreas",male,42,0,1,4579,8.4042,,S,,,
3,0,"Olsen, Mr. Ole Martin",male,,0,0,Fa 265302,7.3125,,S,,,
3,0,"Olsson, Miss. Elina",female,31,0,0,350407,7.8542,,S,,,
3,0,"Olsson, Mr. Nils Johan Goransson",male,28,0,0,347464,7.8542,,S,,,
3,1,"Olsson, Mr. Oscar Wilhelm",male,32,0,0,347079,7.775,,S,A,,
3,0,"Olsvigen, Mr. Thor Anderson",male,20,0,0,6563,9.225,,S,,89,"Oslo, Norway Cameron, WI"
3,0,"Oreskovic, Miss. Jelka",female,23,0,0,315085,8.6625,,S,,,
3,0,"Oreskovic, Miss. Marija",female,20,0,0,315096,8.6625,,S,,,
3,0,"Oreskovic, Mr. Luka",male,20,0,0,315094,8.6625,,S,,,
3,0,"Osen, Mr. Olaf Elon",male,16,0,0,7534,9.2167,,S,,,
3,1,"Osman, Mrs. Mara",female,31,0,0,349244,8.6833,,S,,,
3,0,"O'Sullivan, Miss. Bridget Mary",female,,0,0,330909,7.6292,,Q,,,
3,0,"Palsson, Master. Gosta Leonard",male,2,3,1,349909,21.075,,S,,4,
3,0,"Palsson, Master. Paul Folke",male,6,3,1,349909,21.075,,S,,,
3,0,"Palsson, Miss. Stina Viola",female,3,3,1,349909,21.075,,S,,,
3,0,"Palsson, Miss. Torborg Danira",female,8,3,1,349909,21.075,,S,,,
3,0,"Palsson, Mrs. Nils (Alma Cornelia Berglund)",female,29,0,4,349909,21.075,,S,,206,
3,0,"Panula, Master. Eino Viljami",male,1,4,1,3101295,39.6875,,S,,,
3,0,"Panula, Master. Juha Niilo",male,7,4,1,3101295,39.6875,,S,,,
3,0,"Panula, Master. Urho Abraham",male,2,4,1,3101295,39.6875,,S,,,
3,0,"Panula, Mr. Ernesti Arvid",male,16,4,1,3101295,39.6875,,S,,,
3,0,"Panula, Mr. Jaako Arnold",male,14,4,1,3101295,39.6875,,S,,,
3,0,"Panula, Mrs. Juha (Maria Emilia Ojala)",female,41,0,5,3101295,39.6875,,S,,,
3,0,"Pasic, Mr. Jakob",male,21,0,0,315097,8.6625,,S,,,
3,0,"Patchett, Mr. George",male,19,0,0,358585,14.5,,S,,,
3,0,"Paulner, Mr. Uscher",male,,0,0,3411,8.7125,,C,,,
3,0,"Pavlovic, Mr. Stefo",male,32,0,0,349242,7.8958,,S,,,
3,0,"Peacock, Master. Alfred Edward",male,0.75,1,1,SOTON/O.Q. 3101315,13.775,,S,,,
3,0,"Peacock, Miss. Treasteall",female,3,1,1,SOTON/O.Q. 3101315,13.775,,S,,,
3,0,"Peacock, Mrs. Benjamin (Edith Nile)",female,26,0,2,SOTON/O.Q. 3101315,13.775,,S,,,
3,0,"Pearce, Mr. Ernest",male,,0,0,343271,7,,S,,,
3,0,"Pedersen, Mr. Olaf",male,,0,0,345498,7.775,,S,,,
3,0,"Peduzzi, Mr. Joseph",male,,0,0,A/5 2817,8.05,,S,,,
3,0,"Pekoniemi, Mr. Edvard",male,21,0,0,STON/O 2. 3101294,7.925,,S,,,
3,0,"Peltomaki, Mr. Nikolai Johannes",male,25,0,0,STON/O 2. 3101291,7.925,,S,,,
3,0,"Perkin, Mr. John Henry",male,22,0,0,A/5 21174,7.25,,S,,,
3,1,"Persson, Mr. Ernst Ulrik",male,25,1,0,347083,7.775,,S,15,,
3,1,"Peter, Master. Michael J",male,,1,1,2668,22.3583,,C,C,,
3,1,"Peter, Miss. Anna",female,,1,1,2668,22.3583,F E69,C,D,,
3,1,"Peter, Mrs. Catherine (Catherine Rizk)",female,,0,2,2668,22.3583,,C,D,,
3,0,"Peters, Miss. Katie",female,,0,0,330935,8.1375,,Q,,,
3,0,"Petersen, Mr. Marius",male,24,0,0,342441,8.05,,S,,,
3,0,"Petranec, Miss. Matilda",female,28,0,0,349245,7.8958,,S,,,
3,0,"Petroff, Mr. Nedelio",male,19,0,0,349212,7.8958,,S,,,
3,0,"Petroff, Mr. Pastcho ('Pentcho')",male,,0,0,349215,7.8958,,S,,,
3,0,"Petterson, Mr. Johan Emil",male,25,1,0,347076,7.775,,S,,,
3,0,"Pettersson, Miss. Ellen Natalia",female,18,0,0,347087,7.775,,S,,,
3,1,"Pickard, Mr. Berk (Berk Trembisky)",male,32,0,0,SOTON/O.Q. 392078,8.05,E10,S,9,,
3,0,"Plotcharsky, Mr. Vasil",male,,0,0,349227,7.8958,,S,,,
3,0,"Pokrnic, Mr. Mate",male,17,0,0,315095,8.6625,,S,,,
3,0,"Pokrnic, Mr. Tome",male,24,0,0,315092,8.6625,,S,,,
3,0,"Radeff, Mr. Alexander",male,,0,0,349223,7.8958,,S,,,
3,0,"Rasmussen, Mrs. (Lena Jacobsen Solvang)",female,,0,0,65305,8.1125,,S,,,
3,0,"Razi, Mr. Raihed",male,,0,0,2629,7.2292,,C,,,
3,0,"Reed, Mr. James George",male,,0,0,362316,7.25,,S,,,
3,0,"Rekic, Mr. Tido",male,38,0,0,349249,7.8958,,S,,,
3,0,"Reynolds, Mr. Harold J",male,21,0,0,342684,8.05,,S,,,
3,0,"Rice, Master. Albert",male,10,4,1,382652,29.125,,Q,,,
3,0,"Rice, Master. Arthur",male,4,4,1,382652,29.125,,Q,,,
3,0,"Rice, Master. Eric",male,7,4,1,382652,29.125,,Q,,,
3,0,"Rice, Master. Eugene",male,2,4,1,382652,29.125,,Q,,,
3,0,"Rice, Master. George Hugh",male,8,4,1,382652,29.125,,Q,,,
3,0,"Rice, Mrs. William (Margaret Norton)",female,39,0,5,382652,29.125,,Q,,327,
3,0,"Riihivouri, Miss. Susanna Juhantytar "'Sanni'",female,22,0,0,3101295,39.6875,,S,,,
3,0,"Rintamaki, Mr. Matti",male,35,0,0,STON/O 2. 3101273,7.125,,S,,,
3,1,"Riordan, Miss. Johanna "'Hannah'",female,,0,0,334915,7.7208,,Q,13,,
3,0,"Risien, Mr. Samuel Beard",male,,0,0,364498,14.5,,S,,,
3,0,"Risien, Mrs. Samuel (Emma)",female,,0,0,364498,14.5,,S,,,
3,0,"Robins, Mr. Alexander A",male,50,1,0,A/5. 3337,14.5,,S,,119,
3,0,"Robins, Mrs. Alexander A (Grace Charity Laury)",female,47,1,0,A/5. 3337,14.5,,S,,7,
3,0,"Rogers, Mr. William John",male,,0,0,S.C./A.4. 23567,8.05,,S,,,
3,0,"Rommetvedt, Mr. Knud Paust",male,,0,0,312993,7.775,,S,,,
3,0,"Rosblom, Miss. Salli Helena",female,2,1,1,370129,20.2125,,S,,,
3,0,"Rosblom, Mr. Viktor Richard",male,18,1,1,370129,20.2125,,S,,,
3,0,"Rosblom, Mrs. Viktor (Helena Wilhelmina)",female,41,0,2,370129,20.2125,,S,,,
3,1,"Roth, Miss. Sarah A",female,,0,0,342712,8.05,,S,C,,
3,0,"Rouse, Mr. Richard Henry",male,50,0,0,A/5 3594,8.05,,S,,,
3,0,"Rush, Mr. Alfred George John",male,16,0,0,A/4. 20589,8.05,,S,,,
3,1,"Ryan, Mr. Edward",male,,0,0,383162,7.75,,Q,14,,
3,0,"Ryan, Mr. Patrick",male,,0,0,371110,24.15,,Q,,,
3,0,"Saad, Mr. Amin",male,,0,0,2671,7.2292,,C,,,
3,0,"Saad, Mr. Khalil",male,25,0,0,2672,7.225,,C,,,
3,0,"Saade, Mr. Jean Nassr",male,,0,0,2676,7.225,,C,,,
3,0,"Sadlier, Mr. Matthew",male,,0,0,367655,7.7292,,Q,,,
3,0,"Sadowitz, Mr. Harry",male,,0,0,LP 1588,7.575,,S,,,
3,0,"Saether, Mr. Simon Sivertsen",male,38.5,0,0,SOTON/O.Q. 3101262,7.25,,S,,32,
3,0,"Sage, Master. Thomas Henry",male,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Master. William Henry",male,14.5,8,2,CA. 2343,69.55,,S,,67,
3,0,"Sage, Miss. Ada",female,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Miss. Constance Gladys",female,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Miss. Dorothy Edith "'Dolly'",female,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Miss. Stella Anna",female,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Mr. Douglas Bullen",male,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Mr. Frederick",male,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Mr. George John Jr",male,,8,2,CA. 2343,69.55,,S,,,
3,0,"Sage, Mr. John George",male,,1,9,CA. 2343,69.55,,S,,,
3,0,"Sage, Mrs. John (Annie Bullen)",female,,1,9,CA. 2343,69.55,,S,,,
3,0,"Salander, Mr. Karl Johan",male,24,0,0,7266,9.325,,S,,,
3,1,"Salkjelsvik, Miss. Anna Kristine",female,21,0,0,343120,7.65,,S,C,,
3,0,"Salonen, Mr. Johan Werner",male,39,0,0,3101296,7.925,,S,,,
3,0,"Samaan, Mr. Elias",male,,2,0,2662,21.6792,,C,,,
3,0,"Samaan, Mr. Hanna",male,,2,0,2662,21.6792,,C,,,
3,0,"Samaan, Mr. Youssef",male,,2,0,2662,21.6792,,C,,,
3,1,"Sandstrom, Miss. Beatrice Irene",female,1,1,1,PP 9549,16.7,G6,S,13,,
3,1,"Sandstrom, Mrs. Hjalmar (Agnes Charlotta Bengtsson)",female,24,0,2,PP 9549,16.7,G6,S,13,,
3,1,"Sandstrom, Miss. Marguerite Rut",female,4,1,1,PP 9549,16.7,G6,S,13,,
3,1,"Sap, Mr. Julius",male,25,0,0,345768,9.5,,S,11,,
3,0,"Saundercock, Mr. William Henry",male,20,0,0,A/5. 2151,8.05,,S,,,
3,0,"Sawyer, Mr. Frederick Charles",male,24.5,0,0,342826,8.05,,S,,284,
3,0,"Scanlan, Mr. James",male,,0,0,36209,7.725,,Q,,,
3,0,"Sdycoff, Mr. Todor",male,,0,0,349222,7.8958,,S,,,
3,0,"Shaughnessy, Mr. Patrick",male,,0,0,370374,7.75,,Q,,,
3,1,"Sheerlinck, Mr. Jan Baptist",male,29,0,0,345779,9.5,,S,11,,
3,0,"Shellard, Mr. Frederick William",male,,0,0,C.A. 6212,15.1,,S,,,
3,1,"Shine, Miss. Ellen Natalia",female,,0,0,330968,7.7792,,Q,,,
3,0,"Shorney, Mr. Charles Joseph",male,,0,0,374910,8.05,,S,,,
3,0,"Simmons, Mr. John",male,,0,0,SOTON/OQ 392082,8.05,,S,,,
3,0,"Sirayanian, Mr. Orsen",male,22,0,0,2669,7.2292,,C,,,
3,0,"Sirota, Mr. Maurice",male,,0,0,392092,8.05,,S,,,
3,0,"Sivic, Mr. Husein",male,40,0,0,349251,7.8958,,S,,,
3,0,"Sivola, Mr. Antti Wilhelm",male,21,0,0,STON/O 2. 3101280,7.925,,S,,,
3,1,"Sjoblom, Miss. Anna Sofia",female,18,0,0,3101265,7.4958,,S,16,,
3,0,"Skoog, Master. Harald",male,4,3,2,347088,27.9,,S,,,
3,0,"Skoog, Master. Karl Thorsten",male,10,3,2,347088,27.9,,S,,,
3,0,"Skoog, Miss. Mabel",female,9,3,2,347088,27.9,,S,,,
3,0,"Skoog, Miss. Margit Elizabeth",female,2,3,2,347088,27.9,,S,,,
3,0,"Skoog, Mr. Wilhelm",male,40,1,4,347088,27.9,,S,,,
3,0,"Skoog, Mrs. William (Anna Bernhardina Karlsson)",female,45,1,4,347088,27.9,,S,,,
3,0,"Slabenoff, Mr. Petco",male,,0,0,349214,7.8958,,S,,,
3,0,"Slocovski, Mr. Selman Francis",male,,0,0,SOTON/OQ 392086,8.05,,S,,,
3,0,"Smiljanic, Mr. Mile",male,,0,0,315037,8.6625,,S,,,
3,0,"Smith, Mr. Thomas",male,,0,0,384461,7.75,,Q,,,
3,1,"Smyth, Miss. Julia",female,,0,0,335432,7.7333,,Q,13,,
3,0,"Soholt, Mr. Peter Andreas Lauritz Andersen",male,19,0,0,348124,7.65,F G73,S,,,
3,0,"Somerton, Mr. Francis William",male,30,0,0,A.5. 18509,8.05,,S,,,
3,0,"Spector, Mr. Woolf",male,,0,0,A.5. 3236,8.05,,S,,,
3,0,"Spinner, Mr. Henry John",male,32,0,0,STON/OQ. 369943,8.05,,S,,,
3,0,"Staneff, Mr. Ivan",male,,0,0,349208,7.8958,,S,,,
3,0,"Stankovic, Mr. Ivan",male,33,0,0,349239,8.6625,,C,,,
3,1,"Stanley, Miss. Amy Zillah Elsie",female,23,0,0,CA. 2314,7.55,,S,C,,
3,0,"Stanley, Mr. Edward Roland",male,21,0,0,A/4 45380,8.05,,S,,,
3,0,"Storey, Mr. Thomas",male,60.5,0,0,3701,,,S,,261,
3,0,"Stoytcheff, Mr. Ilia",male,19,0,0,349205,7.8958,,S,,,
3,0,"Strandberg, Miss. Ida Sofia",female,22,0,0,7553,9.8375,,S,,,
3,1,"Stranden, Mr. Juho",male,31,0,0,STON/O 2. 3101288,7.925,,S,9,,
3,0,"Strilic, Mr. Ivan",male,27,0,0,315083,8.6625,,S,,,
3,0,"Strom, Miss. Telma Matilda",female,2,0,1,347054,10.4625,G6,S,,,
3,0,"Strom, Mrs. Wilhelm (Elna Matilda Persson)",female,29,1,1,347054,10.4625,G6,S,,,
3,1,"Sunderland, Mr. Victor Francis",male,16,0,0,SOTON/OQ 392089,8.05,,S,B,,
3,1,"Sundman, Mr. Johan Julian",male,44,0,0,STON/O 2. 3101269,7.925,,S,15,,
3,0,"Sutehall, Mr. Henry Jr",male,25,0,0,SOTON/OQ 392076,7.05,,S,,,
3,0,"Svensson, Mr. Johan",male,74,0,0,347060,7.775,,S,,,
3,1,"Svensson, Mr. Johan Cervin",male,14,0,0,7538,9.225,,S,13,,
3,0,"Svensson, Mr. Olof",male,24,0,0,350035,7.7958,,S,,,
3,1,"Tenglin, Mr. Gunnar Isidor",male,25,0,0,350033,7.7958,,S,13 15,,
3,0,"Theobald, Mr. Thomas Leonard",male,34,0,0,363294,8.05,,S,,176,
3,1,"Thomas, Master. Assad Alexander",male,0.4167,0,1,2625,8.5167,,C,16,,
3,0,"Thomas, Mr. Charles P",male,,1,0,2621,6.4375,,C,,,
3,0,"Thomas, Mr. John",male,,0,0,2681,6.4375,,C,,,
3,0,"Thomas, Mr. Tannous",male,,0,0,2684,7.225,,C,,,
3,1,"Thomas, Mrs. Alexander (Thamine 'Thelma')",female,16,1,1,2625,8.5167,,C,14,,
3,0,"Thomson, Mr. Alexander Morrison",male,,0,0,32302,8.05,,S,,,
3,0,"Thorneycroft, Mr. Percival",male,,1,0,376564,16.1,,S,,,
3,1,"Thorneycroft, Mrs. Percival (Florence Kate White)",female,,1,0,376564,16.1,,S,10,,
3,0,"Tikkanen, Mr. Juho",male,32,0,0,STON/O 2. 3101293,7.925,,S,,,
3,0,"Tobin, Mr. Roger",male,,0,0,383121,7.75,F38,Q,,,
3,0,"Todoroff, Mr. Lalio",male,,0,0,349216,7.8958,,S,,,
3,0,"Tomlin, Mr. Ernest Portage",male,30.5,0,0,364499,8.05,,S,,50,
3,0,"Torber, Mr. Ernst William",male,44,0,0,364511,8.05,,S,,,
3,0,"Torfa, Mr. Assad",male,,0,0,2673,7.2292,,C,,,
3,1,"Tornquist, Mr. William Henry",male,25,0,0,LINE,0,,S,15,,
3,0,"Toufik, Mr. Nakli",male,,0,0,2641,7.2292,,C,,,
3,1,"Touma, Master. Georges Youssef",male,7,1,1,2650,15.2458,,C,C,,
3,1,"Touma, Miss. Maria Youssef",female,9,1,1,2650,15.2458,,C,C,,
3,1,"Touma, Mrs. Darwis (Hanne Youssef Razi)",female,29,0,2,2650,15.2458,,C,C,,
3,0,"Turcin, Mr. Stjepan",male,36,0,0,349247,7.8958,,S,,,
3,1,"Turja, Miss. Anna Sofia",female,18,0,0,4138,9.8417,,S,15,,
3,1,"Turkula, Mrs. (Hedwig)",female,63,0,0,4134,9.5875,,S,15,,
3,0,"van Billiard, Master. James William",male,,1,1,A/5. 851,14.5,,S,,,
3,0,"van Billiard, Master. Walter John",male,11.5,1,1,A/5. 851,14.5,,S,,1,
3,0,"van Billiard, Mr. Austin Blyler",male,40.5,0,2,A/5. 851,14.5,,S,,255,
3,0,"Van Impe, Miss. Catharina",female,10,0,2,345773,24.15,,S,,,
3,0,"Van Impe, Mr. Jean Baptiste",male,36,1,1,345773,24.15,,S,,,
3,0,"Van Impe, Mrs. Jean Baptiste (Rosalie Paula Govaert)",female,30,1,1,345773,24.15,,S,,,
3,0,"van Melkebeke, Mr. Philemon",male,,0,0,345777,9.5,,S,,,
3,0,"Vande Velde, Mr. Johannes Joseph",male,33,0,0,345780,9.5,,S,,,
3,0,"Vande Walle, Mr. Nestor Cyriel",male,28,0,0,345770,9.5,,S,,,
3,0,"Vanden Steen, Mr. Leo Peter",male,28,0,0,345783,9.5,,S,,,
3,0,"Vander Cruyssen, Mr. Victor",male,47,0,0,345765,9,,S,,,
3,0,"Vander Planke, Miss. Augusta Maria",female,18,2,0,345764,18,,S,,,
3,0,"Vander Planke, Mr. Julius",male,31,3,0,345763,18,,S,,,
3,0,"Vander Planke, Mr. Leo Edmondus",male,16,2,0,345764,18,,S,,,
3,0,"Vander Planke, Mrs. Julius (Emelia Maria Vandemoortele)",female,31,1,0,345763,18,,S,,,
3,1,"Vartanian, Mr. David",male,22,0,0,2658,7.225,,C,13 15,,
3,0,"Vendel, Mr. Olof Edvin",male,20,0,0,350416,7.8542,,S,,,
3,0,"Vestrom, Miss. Hulda Amanda Adolfina",female,14,0,0,350406,7.8542,,S,,,
3,0,"Vovk, Mr. Janko",male,22,0,0,349252,7.8958,,S,,,
3,0,"Waelens, Mr. Achille",male,22,0,0,345767,9,,S,,,"Antwerp, Belgium / Stanton, OH"
3,0,"Ware, Mr. Frederick",male,,0,0,359309,8.05,,S,,,
3,0,"Warren, Mr. Charles William",male,,0,0,C.A. 49867,7.55,,S,,,
3,0,"Webber, Mr. James",male,,0,0,SOTON/OQ 3101316,8.05,,S,,,
3,0,"Wenzel, Mr. Linhart",male,32.5,0,0,345775,9.5,,S,,298,
3,1,"Whabee, Mrs. George Joseph (Shawneene Abi-Saab)",female,38,0,0,2688,7.2292,,C,C,,
3,0,"Widegren, Mr. Carl/Charles Peter",male,51,0,0,347064,7.75,,S,,,
3,0,"Wiklund, Mr. Jakob Alfred",male,18,1,0,3101267,6.4958,,S,,314,
3,0,"Wiklund, Mr. Karl Johan",male,21,1,0,3101266,6.4958,,S,,,
3,1,"Wilkes, Mrs. James (Ellen Needs)",female,47,1,0,363272,7,,S,,,
3,0,"Willer, Mr. Aaron ('Abi Weller')",male,,0,0,3410,8.7125,,S,,,
3,0,"Willey, Mr. Edward",male,,0,0,S.O./P.P. 751,7.55,,S,,,
3,0,"Williams, Mr. Howard Hugh "'Harry'",male,,0,0,A/5 2466,8.05,,S,,,
3,0,"Williams, Mr. Leslie",male,28.5,0,0,54636,16.1,,S,,14,
3,0,"Windelov, Mr. Einar",male,21,0,0,SOTON/OQ 3101317,7.25,,S,,,
3,0,"Wirz, Mr. Albert",male,27,0,0,315154,8.6625,,S,,131,
3,0,"Wiseman, Mr. Phillippe",male,,0,0,A/4. 34244,7.25,,S,,,
3,0,"Wittevrongel, Mr. Camille",male,36,0,0,345771,9.5,,S,,,
3,0,"Yasbeck, Mr. Antoni",male,27,1,0,2659,14.4542,,C,C,,
3,1,"Yasbeck, Mrs. Antoni (Selini Alexander)",female,15,1,0,2659,14.4542,,C,,,
3,0,"Youseff, Mr. Gerious",male,45.5,0,0,2628,7.225,,C,,312,
3,0,"Yousif, Mr. Wazli",male,,0,0,2647,7.225,,C,,,
3,0,"Yousseff, Mr. Gerious",male,,0,0,2627,14.4583,,C,,,
3,0,"Zabour, Miss. Hileni",female,14.5,1,0,2665,14.4542,,C,,328,
3,0,"Zabour, Miss. Thamine",female,,1,0,2665,14.4542,,C,,,
3,0,"Zakarian, Mr. Mapriededer",male,26.5,0,0,2656,7.225,,C,,304,
3,0,"Zakarian, Mr. Ortin",male,27,0,0,2670,7.225,,C,,,
3,0,"Zimmerman, Mr. Leo",male,29,0,0,315082,7.875,,S,,,"""
        |> fromCSV
```