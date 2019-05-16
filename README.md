# crossfilter

## what is crossfilter
crossfiltering deals with multidimensional filtering, with many factors involved. it is a purely analytic tool that can give insight in the data provided. for example, it can determine, "how many x, given y and z", or "which is the most frequent, given x, y, z, and a".

## how to use crossfilter and basic functions (use fake3.csv as reference)
### **basics**
to begin any crossfiltering of any data set, we have to start with  <br> `var cf = crossfilter(data);` &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (names of variables are used by convention)

`cf.size();` <br>
returns the amount of rows found in the given dataset (doesn't count the first row with labels)

### **using and modifying dimensions**

`var raceDim = cf.dimension(d => d.race);` or <br>
`var raceDim = cf.dimension(function(d) {return d.race;});` <br>
we can think of raceDim as "holding" a column of the data. it represents the "race" column of the data found in fake3.csv. we can now make a few filters with this newly created raceDim <br>

`raceDim.top(2);` <br> this returns an array with the top 2 highest string values, example below:<br>

| Date    | County  | ...     | Race    | ... |
|---------|---------|---------|---------|-----|
| 1984-01-01 | Benzie | ... | White | ... |
| 1984-01-02 | Ottowa | ... | White | ... |

>we can think about this as reverse alphabetical order

`raceDim.bottom(2);` <br>
this returns an array with the bottom 2 lowest strings: 

| Date    | County  | ...     | Race    | ... |
|---------|---------|---------|---------|-----|
| 1984-03-27 | St Clair | ... | American Indian or Alask Native | ... |
| 1984-03-20 | Calhoun | ... | American Indian or Alask Native | ... |

>we can think of this as alphabetical order

dimensions can also be further filtered after they've been created<br>
`raceDim.filter(d => d > 'MMMMM');` <br>
raceDim is a dimension with all race vlues which are greater than the string literal 'MMMMM'.

`raceDim.top(2);` is unchanged while <br> <br>
`raceDim.bottom(2);` is now <br>

| Date    | County  | ...     | Race    | ... |
|---------|---------|---------|---------|-----|
| 1984-03-25 | Chippewa | ... | Native Hawaiian or Other Pacific Islander | ... |
| 1984-03-15 | Wayne | ... | Native Hawaiian or Other Pacific Islander | ... |

if we want to remove our previous filters, we can write
`raceDim.filterAll();` <br>
now, when we write `raceDim.top(2);` or `raceDim.bottom(2);` we get the original tables before `raceDim.filter(d => d > 'MMMMM');` was applied. 

### **using and modifying groups**
crossfilter grouping allows us to group data from a particular dimension and use count, average, or sum functions on that given dimension. the main difference is that dimensions are rows of original data while groups are an array of a key-value pair. 

`var group = raceDim.group();` <br>
creates the array of groups with key-value pairs

`group.all();` <br>
gets the array, which looks like so:

| key | value |
|-----|-------|
| American Indian or Alask Native | 12 |
| Asian | 20 |
| Black | 94 |
| ... | ... |
> note that this is in alphabetical order, where the string values are getting larger as we go down

similar to dimensions, we can list out the top _n_ values by calling: <br> <br>
`group.top(2);` which gives
| key | value |
|-----|-------|
| Black | 94 |
| White | 89 |
>note that since the value column is the column we are comparing, top in this case means the highest two integers that appear in the value column, which is 94 and 89 here. 

