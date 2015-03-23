# TR55

[![Build Status](https://travis-ci.org/caseypt/TR55.jl.svg?branch=master)](https://travis-ci.org/caseypt/TR55.jl)

**This is a work in progress and is just an excerise to learn Julia.**

An implementation of TR-55 in Julia.

From the [manual](http://www.nrcs.usda.gov/Internet/FSE_DOCUMENTS/stelprdb1044171.pdf):

> Technical Release 55 (TR-55) presents simplified procedures to calculate storm runoff volume, peak rate of discharge, hydrographs, and storage volumes required for floodwater reservoirs. These procedures are applicable in small watersheds, especially urbanizing watersheds, in the United States

### Calculating runoff

This example follows Figure 2-6 in the TR-55 manual.

> The watershed covers 250 acres in Dyer County, northwestern Tennessee. Seventy percent (175 acres) is a Loring soil, which is in hydrologic soil group C. Thirty percent (75 acres) is a Memphis soil, which is in group B. The event is a 25-year frequency, 24-hour storm with total rainfall of 6 inches.

The curve numbers referenced below for each region are derived from a look up table in the manual.

<table>
  <tr>
      <th>Soil name and hydrologic group</th>
      <th>Cover description</th>
      <th>Curve number</th>
      <th>Area (acres)</th>
  </tr>
  <tr>
    <td>Memphis, B</td>
    <td>25% impervious, 1/2 acre lots, good condition</td>
    <td>70</td>
    <td>75</td>
  </tr>
  <tr>
    <td>Loring, C</td>
    <td>25% impervious, 1/2 acre lots, good condition</td>
    <td>80</td>
    <td>100</td>
  </tr>
    <tr>
    <td>Memphis, B</td>
    <td>Open space, good condition</td>
    <td>74</td>
    <td>75</td>
  </tr>
</table>

We first need to calculate the **weighted curve number** for the watershed. `False` here indicates that the areas are not in percentiles, but measured area.

```julia
julia> using TR55

julia> regions = [
  { "cn" => 70, "area" => 75 },
  { "cn" => 80, "area" => 100 },
  {  "cn" => 74, "area" => 75 }
]

julia> CN = TR55.weighted_cn(regions, false)
75
```

Now that we have the weighted curve number, we can calculate the **potential maximum retention after runoff begins**:

```julia
julia> S = TR55.max_retention(CN)
3.33
```

Finally, we calculate the amount of **runoff**, in inches, from the watershed for a rainfall amount of 6 inches.

```julia
julia> P = 6
6

julia> Q = TR55.runoff(S, P)
3.28
```

For the watershed described above, during a storm that produces 6 inches of rainfall, there would be **3.28** inches of runoff.
