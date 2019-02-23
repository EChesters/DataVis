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

## Gapminder datasets

[Gapminder](https://www.gapminder.org/data/) contains a wealth of datasets relating to global development. Here we extract data on the average number of bad teeth in 12 year old children in 2004 as one measure of public health (just enter "bad teeth" in the [lower query area](https://www.gapminder.org/data/) if you would like to retrieve the data yourself).

The data were copied directly from CSV files extracted from the Gapminder site. The first few rows of the table are shown below:

```elm {m}
displayBTTable : List String
displayBTTable =
    tableSummary 6 badTeethTable
```

## Location dataset

In order to map the global development data, we need to know the locations of the countries listed. Below is a 'gazetteer' that provides the longitude and latitude for each country centroid.

```elm {m}
displayLocationTable : List String
displayLocationTable =
    tableSummary 6 locationLookup
```

Note the convention when using latitude and longitude is that the northern hemisphere has latitudes between 0 (equator) and 90 (north pole), and the southern hemisphere latitudes between 0 (equator) and -90 (south pole). Longitudes vary from 0 (Greenwich meridian) to -180 going west and +180 going east of the Greenwich meridian.

## Joining Tables

We can combine the bad teeth and location lookup into a single table, matching by country name with an [inner join from the Tidy package](https://package.elm-lang.org/packages/gicentre/tidy/latest/Tidy#innerJoin). That is, combine the rows of both tables where `country` in the first table matches `CountryName` in the second table:

```elm {l}
badTeethGeoTable : Table
badTeethGeoTable =
    innerJoin "CountryName"
        ( badTeethTable, "country" )
        ( locationLookup, "CountryName" )
```

```elm {m}
displayJoinedTable : List String
displayJoinedTable =
    badTeethGeoTable
        |> tableSummary 6
```

Note that the number of rows in the two original tables and the joined table are different (172, 247 and 168 respectively). To explore why this is the case we can examine the values that appear in the original tables that do no appear in the joined one. We can do this with Tidy's [leftDiff](https://package.elm-lang.org/packages/gicentre/tidy/latest/Tidy#leftDiff) and [rightDiff](https://package.elm-lang.org/packages/gicentre/tidy/latest/Tidy#rightDiff) table comparison functions:

#### Values in badTeeth table but not in country lookup

These are bad teeth data for countries for which we do not have a location. We may wish to rename the country reference so they match those in the country lookup table.

```elm {m}
displayLeftDiff : List String
displayLeftDiff =
    leftDiff ( badTeethTable, "country" )
        ( locationLookup, "CountryName" )
        |> tableSummary -1
```

#### Values in country lookup table but not in badTeeth

These are countries for which we have no bad teeth data.

```elm {m}
displayRightDiff : List String
displayRightDiff =
    rightDiff ( badTeethTable, "country" )
        ( locationLookup, "CountryName" )
        |> tableSummary -1
```

---

```elm {l=hidden}
badTeethTable : Table
badTeethTable =
    """country,2004
Afghanistan,2.9
Albania,3.02
Algeria,2.3
Angola,1.7
Antigua and Barbuda,0.7
Argentina,3.4
Armenia,2.4
Australia,0.8
Austria,1
Bahamas,1.6
Bahrain,1.4
Bangladesh,1
Barbados,0.86
Belarus,2.7
Belgium,1.1
Belize,0.6
Benin,0.8
Bhutan,1.4
Bolivia,4.7
Bosnia and Herzegovina,4.8
Botswana,0.5
Brazil,2.8
Brunei,4.8
Bulgaria,4.4
Burkina Faso,0.7
Burundi,1
Cambodia,2.3
Cameroon,1.5
Canada,2.1
Central African Republic,4.1
Chile,3.4
China,1.03
Colombia,2.3
"Congo, Dem. Rep.",0.75
Costa Rica,2.3
Croatia,3.5
Cuba,1.4
Cyprus,1.14
Czech Republic,2.5
Denmark,0.8
Djibouti,0.9
Dominica,2
Dominican Republic,4.4
Ecuador,3
Egypt,1.18
El Salvador,1.4
Estonia,2.7
Ethiopia,1.55
Fiji,1.5
Finland,1.2
France,1.2
Gabon,4.4
Gambia,2.3
Georgia,2.4
Germany,0.7
Ghana,0.4
Greece,2.2
Grenada,2.2
Guatemala,5.2
Guinea-Bissau,0.5
Guyana,1.3
Haiti,0.65
Honduras,3.7
Hungary,3.3
Iceland,1.4
India,3.94
Indonesia,2.2
Iran,1.15
Iraq,1.7
Ireland,1.1
Israel,1.66
Italy,1.1
Jamaica,1.1
Japan,1.7
Jordan,3.3
Kazakhstan,2.1
Kenya,1.35
Kiribati,1
Kuwait,2.6
Kyrgyz Republic,3.1
Lao,2
Latvia,3.4
Lebanon,3.4
Lesotho,0.4
Liberia,0.4
Libya,1.14
Liechtenstein,3.4
Lithuania,3.6
Luxembourg,3
"Macedonia, FYR",3.03
Madagascar,3.1
Malawi,0.7
Malaysia,1.6
Maldives,2.1
Mali,2.2
Malta,1.6
Mauritania,2
Mauritius,4.9
Mexico,2
"Micronesia, Fed. Sts.",2.1
Moldova,2.3
Mongolia,1.9
Morocco,2.5
Mozambique,3.05
Myanmar,1.1
Namibia,1.2
Nepal,0.5
Netherlands,0.8
New Zealand,1.6
Nicaragua,1.5
Niger,1.3
Nigeria,0.46
North Korea,3
Norway,1.7
Oman,1.65
Pakistan,1.38
Panama,3.6
Papua New Guinea,1.7
Paraguay,3.8
Peru,2.9
Philippines,2.9
Poland,3.2
Portugal,1.5
Romania,2.8
Russia,2.9
Rwanda,0.3
Samoa,2.5
San Marino,3.7
Saudi Arabia,5.9
Senegal,1.2
Seychelles,1.5
Sierra Leone,1.3
Singapore,1
Slovenia,1.8
Solomon Islands,2.7
Somalia,1
South Africa,1.05
South Korea,3.1
Spain,1.12
Sri Lanka,1.4
Saint Kitts and Nevis,5.5
Saint Lucia,6
Saint Vincent and the Grenadines,3.2
Sudan,1.4
Suriname,1.9
Swaziland,0.9
Sweden,1
Switzerland,0.86
Syria,2.3
Tajikistan,1.2
Tanzania,0.3
Thailand,1.6
Togo,0.3
Tonga,3.1
Trinidad and Tobago,0.6
Tunisia,1.3
Turkey,2.7
Turkmenistan,2.6
Tuvalu,2
Uganda,0.9
Ukraine,4.4
United Arab Emirates,1.6
United Kingdom,0.7
United States,1.19
Uruguay,2.5
Uzbekistan,1.4
Vanuatu,1.2
Venezuela,2.1
Vietnam,1.9
Yemen,3.1
Zambia,2.3
Zimbabwe,1.3"""
        |> fromCSV
        |> renameColumn "2004" "badTeethPerChild"


locationLookup : Table
locationLookup =
    """CountryName,CountryCode,latitude,longitude
Afghanistan,AF,33.93911,67.709953
Albania,AL,41.153332,20.168331
Algeria,DZ,28.033886,1.659626
American Samoa,AS,-14.270972,-170.132217
Andorra,AD,42.546245,1.601554
Angola,AO,-11.202692,17.873887
Anguilla,AI,18.220554,-63.068615
Antarctica,AQ,-75.250973,-0.071389
Antigua and Barbuda,AG,17.060816,-61.796428
Argentina,AR,-38.416097,-63.616672
Armenia,AM,40.069099,45.038189
Aruba,AW,12.52111,-69.968338
Australia,AU,-25.274398,133.775136
Austria,AT,47.516231,14.550072
Azerbaijan,AZ,40.143105,47.576927
Bahamas,BS,25.03428,-77.39628
Bahrain,BH,25.930414,50.637772
Bangladesh,BD,23.684994,90.356331
Barbados,BB,13.193887,-59.543198
Belarus,BY,53.709807,27.953389
Belgium,BE,50.503887,4.469936
Belize,BZ,17.189877,-88.49765
Benin,BJ,9.30769,2.315834
Bermuda,BM,32.321384,-64.75737
Bhutan,BT,27.514162,90.433601
Bolivia,BO,-16.290154,-63.588653
Bosnia and Herzegovina,BA,43.915886,17.679076
Botswana,BW,-22.328474,24.684866
Bouvet Island,BV,-54.423199,3.413194
Brazil,BR,-14.235004,-51.92528
British Indian Ocean Territory,IO,-6.343194,71.876519
Brunei,BN,4.535277,114.727669
Bulgaria,BG,42.733883,25.48583
Burkina Faso,BF,12.238333,-1.561593
Burundi,BI,-3.373056,29.918886
Cambodia,KH,12.565679,104.990963
Cameroon,CM,7.369722,12.354722
Canada,CA,56.130366,-106.346771
Cape Verde,CV,16.002082,-24.013197
Cayman Islands,KY,19.513469,-80.566956
Central African Republic,CF,6.611111,20.939444
Chad,TD,15.454166,18.732207
Chile,CL,-35.675147,-71.542969
China,CN,35.86166,104.195397
Christmas Island,CX,-10.447525,105.690449
Cocos (Keeling) Islands,CC,-12.14,96.88
Colombia,CO,4.570868,-74.297333
Comoros,KM,-11.875001,43.872219
"Congo, Republic",CG,-0.228021,15.827659
"Congo, Dem. Rep.",CD,-0.228021,15.827659
Cook Islands,CK,-21.236736,-159.777671
Costa Rica,CR,9.748917,-83.753428
Cote D'Ivoire,CI,7.539989,-5.54708
Croatia,HR,45.1,15.2
Cuba,CU,21.521757,-77.781167
Cyprus,CY,35.126413,33.429859
Czech Republic,CZ,49.817492,15.472962
Denmark,DK,56.26392,9.501785
Djibouti,DJ,11.825138,42.590275
Dominica,DM,15.414999,-61.370976
Dominican Republic,DO,18.735693,-70.162651
Ecuador,EC,-1.831239,-78.183406
Egypt,EG,26.820553,30.802498
El Salvador,SV,13.794185,-88.89653
Equatorial Guinea,GQ,1.650801,10.267895
Eritrea,ER,15.179384,39.782334
Estonia,EE,58.595272,25.013607
Ethiopia,ET,9.145,40.489673
Falkland Islands (Malvinas),FK,-51.796253,-59.523613
Faroe Islands,FO,61.892635,-6.911806
Fiji,FJ,-16.578193,179.414413
Finland,FI,61.92411,25.748151
France,FR,46.227638,2.213749
French Guiana,GF,3.933889,-53.125782
French Polynesia,PF,-17.679742,-149.406843
French Southern Territories,TF,-43,67
Gabon,GA,-0.803689,11.609444
Gambia,GM,13.443182,-15.310139
Georgia,GE,32.960500,83.1132
Germany,DE,51.165691,10.451526
Ghana,GH,7.946527,-1.023194
Gibraltar,GI,36.137741,-5.345374
Greece,GR,39.074208,21.824312
Greenland,GL,71.706936,-42.604303
Grenada,GD,12.262776,-61.604171
Guadeloupe,GP,16.995971,-62.067641
Guam,GU,13.444304,144.793731
Guatemala,GT,15.783471,-90.230759
Guernsey,GG,49.465691,-2.585278
Guinea,GN,9.945587,-9.696645
Guinea-Bissau,GW,11.803749,-15.180413
Guyana,GY,4.860416,-58.93018
Haiti,HT,18.971187,-72.285215
Heard Island and McDonald Islands,HM,-53.08181,73.504158
Holy See (Vatican City State),VA,41.911,12.452
Honduras,HN,15.199999,-86.241905
"Hong Kong, China",HK,22.396428,114.109497
Hungary,HU,47.162494,19.503304
Iceland,IS,64.963051,-19.020835
India,IN,20.593684,78.96288
Indonesia,ID,-0.789275,113.921327
Iran,IR,32.427908,53.688046
Iraq,IQ,33.223191,43.679291
Ireland,IE,53.41291,-8.24389
Isle of Man,IM,54.236107,-4.548056
Israel,IL,31.046051,34.851612
Italy,IT,41.87194,12.56738
Jamaica,JM,18.109581,-77.297508
Japan,JP,36.204824,138.252924
Jersey,JE,49.214439,-2.13125
Jordan,JO,30.585164,36.238414
Kazakhstan,KZ,48.019573,66.923684
Kenya,KE,-0.023559,37.906193
Kiribati,KI,-3.370417,-168.734039
"Korea, Dem. Republic",KP,40.0,127.0
"Korea, Republic",KR,37.55,126.9667
Kuwait,KW,29.31166,47.481766
Kyrgyzstan,KG,41.20438,74.766098
Laos,LA,19.85627,102.495496
Latvia,LV,56.879635,24.603189
Lebanon,LB,33.854721,35.862285
Lesotho,LS,-29.609988,28.233608
Liberia,LR,6.428055,-9.429499
Libya,LY,25,17
Liechtenstein,LI,47.166,9.555373
Lithuania,LT,55.169438,23.881275
Luxembourg,LU,49.815273,6.129583
"Macao, China",MO,22.198745,113.543873
"Macedonia, FYR",MK,41.608635,21.745275
Madagascar,MG,-18.766947,46.869107
Malawi,MW,-13.254308,34.301525
Malaysia,MY,4.210484,101.975766
Maldives,MV,3.202778,73.22068
Mali,ML,17.570692,-3.996166
Malta,MT,35.937496,14.375416
Marshall Islands,MH,7.131474,171.184478
Martinique,MQ,14.641528,-61.024174
Mauritania,MR,21.00789,-10.940835
Mauritius,MU,-20.348404,57.552152
Mayotte,YT,-12.8275,45.166244
Mexico,MX,23.634501,-102.552784
"Micronesia, Fed. Sts.",FM,7.425554,150.550812
Moldova,MD,47.411631,28.369885
Monaco,MC,43.750298,7.412841
Mongolia,MN,46.862496,103.846656
Montenegro,ME,42.708678,19.37439
Montserrat,MS,16.742498,-62.187366
Morocco,MA,31.791702,-7.09262
Mozambique,MZ,-18.665695,35.529562
Myanmar,MM,21.913965,95.956223
Namibia,NA,-22.95764,18.49041
Nauru,NR,-0.522778,166.931503
Nepal,NP,28.394857,84.124008
Netherlands,NL,52.132633,5.291266
Netherlands Antilles,AN,12.226079,-69.060087
New Caledonia,NC,-20.904305,165.618042
New Zealand,NZ,-40.900557,174.885971
Nicaragua,NI,12.865416,-85.207229
Niger,NE,17.607789,8.081666
Nigeria,NG,9.081999,8.675277
Niue,NU,-19.054445,-169.867233
Norfolk Island,NF,-29.040835,167.954712
Northern Mariana Islands,MP,17.33083,145.38469
Norway,NO,60.472024,8.468946
Oman,OM,21.512583,55.923255
Pakistan,PK,30.375321,69.345116
Palau,PW,7.51498,134.58252
"Palestinian Territory, Occupied",PS,42.094445,17.266614
Panama,PA,8.537981,-80.782127
Papua New Guinea,PG,-6.314993,143.95555
Paraguay,PY,-23.442503,-58.443832
Peru,PE,-9.189967,-75.015152
Philippines,PH,12.879721,121.774017
Pitcairn,PN,-24.703615,-127.439308
Poland,PL,51.919438,19.145136
Portugal,PT,39.399872,-8.224454
Puerto Rico,PR,18.220833,-66.590149
Qatar,QA,25.354826,51.183884
Reunion,RE,-21.115141,55.536384
Romania,RO,45.943161,24.96676
Russia,RU,61.52401,105.318756
Rwanda,RW,-1.940278,29.873888
Saint BarthŽlemy,BL,17.9,-62.833
"Saint Helena, Ascension and Tristan da Cunha",SH,-24.143474,-10.030696
Saint Kitts and Nevis,KN,17.357822,-62.782998
Saint Lucia,LC,13.909444,-60.978893
Saint Martin (French part),MF,43.589046,5.885031
Saint Pierre and Miquelon,PM,46.941936,-56.27111
Saint Vincent and the Grenadines,VC,12.984305,-61.287228
Samoa,WS,-13.759029,-172.104629
San Marino,SM,43.94236,12.457777
Sao Tome and Principe,ST,0.18636,6.613081
Saudi Arabia,SA,23.885942,45.079162
Senegal,SN,14.497401,-14.452362
Serbia,RS,44.016521,21.005859
Serbia and Montenegro,RS,44.016521,21.005859
Seychelles,SC,-4.679574,55.491977
Sierra Leone,SL,8.460555,-11.779889
Singapore,SG,1.352083,103.819836
Slovak republic,SK,48.669026,19.699024
Slovenia,SI,46.151241,14.995463
Solomon Islands,SB,-9.64571,160.156194
Somalia,SO,5.152149,46.199616
South Africa,ZA,-30.559482,22.937506
South Georgia and the South Sandwich Islands,GS,-54.429579,-36.587909
Spain,ES,40.463667,-3.74922
Sri Lanka,LK,7.873054,80.771797
Sudan,SD,12.862807,30.217636
Suriname,SR,3.919305,-56.027783
Svalbard and Jan Mayen,SJ,77.553604,23.670272
Swaziland,SZ,-26.522503,31.465866
Sweden,SE,60.128161,18.643501
Switzerland,CH,46.818188,8.227512
Syria,SY,34.802075,38.996815
"Taiwan, Province of China",TW,23.69781,120.960515
Tajikistan,TJ,38.861034,71.276093
Tanzania,TZ,-6.369028,34.888822
Thailand,TH,15.870032,100.992541
Timor-Leste,TL,-8.874217,125.727539
Togo,TG,8.619543,0.824782
Tokelau,TK,-8.967363,-171.855881
Tonga,TO,-21.178986,-175.198242
Trinidad and Tobago,TT,10.691803,-61.222503
Tunisia,TN,33.886917,9.537499
Turkey,TR,38.963745,35.243322
Turkmenistan,TM,38.969719,59.556278
Turks and Caicos Islands,TC,21.694025,-71.797928
Tuvalu,TV,-7.109535,177.64933
Uganda,UG,1.373333,32.290275
Ukraine,UA,48.379433,31.16558
United Arab Emirates,AE,23.424076,53.847818
United Kingdom,GB,52.378051,-1.435973
United States,US,37.09024,-95.712891
United States Minor Outlying Islands,UM,24.747346,-167.594906
Uruguay,UY,-32.522779,-55.765835
Uzbekistan,UZ,41.377491,64.585262
Vanuatu,VU,-15.376706,166.959158
Venezuela,VE,6.42375,-66.58973
Vietnam,VN,14.058324,108.277199
"Virgin Islands, British",VG,18.335765,-64.896335
Virgin Islands (U.S.),VI,18.335765,-64.896335
Wallis and Futuna,WF,-13.768752,-177.156097
West Bank and Gaza,PS,32.33,35
Western Sahara,EH,24.215527,-12.885834
Yemen,YE,15.552727,48.516388
Zambia,ZM,-13.133897,27.849332
Zimbabwe,ZW,-19.015438,29.154857"""
        |> fromCSV
```
