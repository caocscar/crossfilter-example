# Crossfilter

## What is the Crossfilter Library
Crossfiltering deals with multidimensional filtering, with many factors involved. It is a purely analytic tool that can give insight in the data provided. For example, it can determine, how many x, given y and z, or which is the most frequent, given x, y, z, and a.

## How to Use the Crossfilter Library with Basic Functions (use data.csv as reference)
### **Basics**
Before we start using crossfilter library, we must include it in our HTML file. To add this library, we can write  
`<script src="https://cdnjs.cloudflare.com/ajax/libs/crossfilter2/1.4.7/crossfilter.min.js"></script>`

To begin crossfiltering of any data set, we have to start with  
`var cf = crossfilter(data);` &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (names of variables are used by convention)

We can check if we are reading in the data correctly. Write `cf.size();` to return the amount of rows that are found in the crossfilter object (not counting the first row with labels). In this case, it should return `250`.

### **Using and Modifying Dimensions**

We can think of dimensions as "holding" a column or a piece of the data. By writing  
`var countyDim = cf.dimension(function(d) {return d.county;});`  
we have `countyDim` essentially "holding" the `county` column of the data found in data.csv. 

We can return the highest and lowest values of this dimension. To return an array with the top 3 highest string `county` values, we can write `countyDim.top(3);`. The table given is shown below

**Note that all `lng` and `lat` values will be truncated to 5 decimal places for easier reading**

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-04 | __Van Buren__ | 25-34 | Male | Black | -85.99037 | 42.24792 |
| 1984-01-08 | __Sanilac__ | 25-34 | Male | Black | -82.86971 | 43.41555 |
| 1984-02-02 | __Roscommon__ | 25-34 | Male | White | -85.54674 | 44.36544 |

>We can think about `countyDim` as sorting the counties by reverse alphabetical order. Therefore, the end of the alphabet will come first. 

We can also return the 3 lowest `county` string by writing `countyDim.bottom(3);`. The table is shown below

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-12 | __Baraga__ | 25-34 | Female | White | -88.35545 | 46.70672 |
| 1984-01-10 | __Bay__ | 25-34 | Female | White | -83.98172 | 43.68304 |
| 1984-01-03 | __Bay__ | 45-54 | Male | Black | -84.91486 | 45.47611 |

__Dimensions can also be further filtered after they've been created__

Imagine for some reason, we only want to see only the counties that start with 'M' or after. We can write `countyDim.filter(function(d) {return d > 'M';});` to further filter our dimension. 

> Note that this filtering will apply to all other dimensions and all subsequently created dimensions

With some filters, the top and bottom values may have changed. We can check this by rewriting that code in the console.  

`countyDim.top(3);` is unchanged (since the top 3 string values of `county` start with a letter after 'M')

while `countyDim.bottom(3);` is now

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-07 | __Mason__ | 25-34 | Male | White | -86.21844 | 43.99430 |
| 1984-01-07 | __Monroe__ | 55+ | Male | White | -83.53092 | 41.93977 |
| 1984-01-09 | __Montcalm__ | 45-54 | Male | Black | -85.10792 | 43.28464 |

>Now, the smallest values that can be found in the `countyDim` dimension are only string values greater than 'M'

If one was curious about how many entries exist with a `county` string value that is greater than 'M', we can write: 
`countyDim.top(Infinity).length;` which gives us `15`. 

>Note the capital __I__ in <b>I</b>nfinity. JavaScript is a case sensitive language. 

The great thing about crossfiltering is that multiple dimensions can be created and compared with each other. We can create another dimension called `latDim` like so:  
`var latDim = cf.dimension(function(d) {return d.lat;});`  
This new dimension, `latDim`, represents the lat column of the data found in file data.csv. Since lat is full of float values, it is sorted from highest to lowest.

If we want to see the top and bottom of `latDim`, we can call:

`latDim.top(3);` which gives us

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-04 | __Ottawa__ | 55+ | Male | Asian | -84.54190 | __46.31725__ |
| 1984-01-07 | __Ottawa__ | 45-54 | Female | Asian | -87.84476 | __46.02377__ |
| 1984-01-08 | __Ottawa__ | 25-34 | Male | Black | -87.84271 | __45.97580__ |

and `latDim.bottom(3);` which shows 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-07 | __Monroe__ | 55+ | Male | White | -83.53092 | __41.93977__ |
| 1984-01-04 | __Van Buren__ | 25-34 | Male | Black | -85.99037 | __42.24792__ |
| 1984-01-03 | __Ottawa__ | 55+ | Male | Black | -85.26079 | __42.64399__ |

We have to remember that the `latDim` dimension is filtered because of the filtering that was applied to `countyDim` earlier. In our newly created `latDim` dimension, there are no rows of data which have a `county` string whose value is less than 'M' (as specified earlier). 

We are also able to further filter our `latDim` dimension. If we want only latitudes greater than 45, we can write  
`latDim.filter(function(d) {return d > 45;});`    

Again, this may have an effect on the top and bottom values of `latDim`. We can check this by rewriting our code into the console:

`latDim.top(3);` is unchanged (since the top 3 values of `lat` are all greater than 45)

`latDim.bottom(3);` now becomes

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-08 | __Ottawa__ | 25-34 | Male | Black | -87.84271 | __45.97580__ |
| 1984-01-07 | __Ottawa__ | 45-54 | Female | Asian | -87.84476 | __46.02377__ |
| 1984-01-04 | __Ottawa__ | 55+ | Male | Asian | -84.54190 | __46.31725__ |

We see that this filter of getting only values of `lat` higher than 45 modifies the `latDim` dimension. However, it has another side effect of modifying the `countyDim` dimension as well. Again, filters applied to one dimension will be applied to all other dimensions as well. 

We can check this by writing `countyDim.top(3);` and `countyDim.bottom(3);` to see how they have changed

`countyDim.top(3);` shows

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-07 | __Ottawa__ | 45-54 | Female | Asian | -87.84476 | __46.02377__ |
| 1984-01-04 | __Ottawa__ | 55+ | Male | Asian | -84.54190 | __46.31725__ |
| 1984-01-08 | __Ottawa__ | 25-34 | Male | Black | -87.84271 | __45.97580__ |

and `countyDim.bottom(3);` gives 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-08 | __Ottawa__ | 25-34 | Male | Black | -87.84271 | __45.97580__ |
| 1984-01-04 | __Ottawa__ | 55+ | Male | Asian | -84.54190 | __46.31725__ |
| 1984-01-07 | __Ottawa__ | 45-54 | Female | Asian | -87.84476 | __46.02377__ |

Both these dimensions are filtered by only having `lat` values that are greater than 45 and `county` values that start past the letter 'M'. Something that is harder to see is that they contain the exact same set of data, but is sorted differently, either by `lat` or `county`. 

Let's say we wanted to see a range of `lat` values, from 44 to 46. We can use the same filter function, but instead, we give it an array of two values, which represent a higher and lower bound like so:  
`latDim.filter([44, 46]);`  
> Note we do not need to clear the previous filter applied to `latDim` because a new filter overwrites the last one that was applied to that dimension. In this case, `latDim.filter([44, 46]);` overwrites `latDim.filter(function(d) {return d > 45;});`    
.

This will only give us `lat` values between 44 and 46 for both dimensions, `latDim` and `countyDim`. We can call our four functions (`latDim.top(3);`, `latDim.bottom(3);`, `countyDim.top(3);` and `countyDim.bottom(3);`) to see how this changes our arrays. 

`latDim.top(3);` now shows

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-08 | __Ottawa__ | 25-34 | Male | Black | -87.84271 | __45.97580__ |
| 1984-01-08 | __Montmorency__ | 25-34 | Male | Native Hawaiian or Other Pacific Islander | -84.14247 | __44.99624__|
| 1984-01-02 | __Ottawa__ | 25-34 | Female | White | -83.63952 | __44.40430__ |

`latDim.bottom(3);` gives us 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-10 | __Osceola__ | 25-34 | Male | Black | -85.31771 | __44.00691__ |
| 1984-01-02 | __Roscommon__ | 25-34 | Male | White | -84.63636 | __44.36544__ |
| 1984-01-02 | __Ottawa__ | 25-34 | Female | White | -83.63952 | __44.40430__ |

`countyDim.top(3);` becomes

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-02 | __Roscommon__ | 25-34 | Male | White | -84.63636 | __44.36544__ |
| 1984-01-02 | __Ottawa__ | 25-34 | Female | White | -83.63952 | __44.40430__ |
| 1984-01-10 | __Osceola__ | 25-34 | Male | Black | -85.31771 | __44.00691__ |

`countyDim.bottom(3);` changes to 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-08 | __Montmorency__ | 25-34 | Male | Native Hawaiian or Other Pacific Islander | -84.14247 | __44.99624__|
| 1984-01-10 | __Osceola__ | 25-34 | Male | Black | -85.31771 | __44.00691__ |
| 1984-01-08 | __Ottawa__ | 25-34 | Male | Black | -87.84271 | __45.97580__ |

Lastly, what if we want to see the highest and lowest `lat` values, without any filtering from `countyDim` or `latDim`? Right now, our `countyDim` and `latDim` filters are restricting the data we see, but we can write  
`countyDim.filterAll();` and then `latDim.filterAll();`  
This removes all filtering that was applied to both these dimensions. Calling the same four functions, we have 

`countyDim.top(3);` is

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-04 | __Van Buren__ | 25-34 | Male | Black | -85.99037 | 42.24792 |
| 1984-01-08 | __Sanilac__ | 25-34 | Male | Black | -82.86971 | 43.41555 |
| 1984-02-02 | __Roscommon__ | 25-34 | Male | White | -85.54674 | 44.36544 |

`countyDim.bottom(3);` shows 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-12 | __Baraga__ | 25-34 | Female | White | -88.35545 | 46.70672 |
| 1984-01-10 | __Bay__ | 25-34 | Female | White | -83.98172 | 43.68304 |
| 1984-01-03 | __Bay__ | 45-54 | Male | Balck | -84.91486 | 45.47611 |

> `countyDim` is back to it's original sorting, with `countyDim.top(3);` sorting it by reverse alphabetical order and `countyDim.bottom(3);` sorting it in alphabetical order.

`latDim.top(3);` gives 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-12 | Baraga | 25-34 | Female | White | -88.35545 | __46.70672__ |
| 1984-01-04 | Luce | 0-25 | Female | Black | -85.59021 | __46.46788__ |
| 1984-01-04 | Ottawa | 55+| Male | Asian | -84.54190 | __46.31725__ |

and `latDim.bottom(3);` is 

| Date    | County  | Age | Gender | Race | lng | lat |
|---------|---------|-----|--------|------|-----|-----|
| 1984-01-12| Lenawee | 25-34 | Male | Black | -84.10959 | __41.91983__ |
| 1984-01-07 | Monroe | 55+ | Male | White | -83.53092 | __41.93977__ |
| 1984-01-06 | Houghton | 0-25 | Male | Black | -83.57521 | __41.95413__ |

> `latDim` is sorting its values without any filters, with `latDim.top(3);` sorting by highest value and `latDim.bottom(3);` sorting by lowest value. 

### **Using and Modifying Groups**
Crossfilter grouping allows us to group data from a particular dimension and use count, average, or sum functions on that given dimension. The main difference is that dimensions are rows of original data while groups are an array of a key-value pair. 

We've decided we want to analyze the `Race` dimension of the data.csv file. We first start by creating a dimension that holds the `Race` column.   
`var raceDim = cf.dimension(function(d) {return d.Race;});`

Next, we create a group of key-value from that dimension by writing
`var raceGroup = raceDim.group();`  

`raceGroup.all();` gets the array, which looks like so:

| key | value |
|-----|-------|
| Asian | 3 |
| Black | 17 |
| Hispanic or Latino | 2 |
| Native Hawaiian or Other Pacific Islander | 2 |
| Unknown | 1 |
| White | 10 |

> Note that this is in alphabetical order, where the string values are getting larger as we go down

Similar to dimensions, we can list out the top _n_ values by calling:  

`raceGroup.top(3);` which gives  

| key | value |
|-----|-------|
| Black | 17 |
| White | 10 |
| Asian | 3 |

> Note that since the value column is the column we are comparing, top in this case means the highest two integers that appear in the value column, which is 17 (Black) and 10 (White) here. 

Groups in crossfilter are also powerful as we can find statistics based on a certain `Race`, `county`, `Age`, etc. In our case, we have a `Race` dimension and group so we'll use that.

Imagine we wanted to find the sum of the latitudes of each accident of each `Race`. We can do this by using a `reduceSum` function like so:  
`raceGroup.reduceSum(d => d.lat).all();` This gives us:

| key | value |
|-----|-------|
| Asian | 136.30300 |
| Black | 742.34418 |
| Hispanic or Latino | 90.50490 |
| Native Hawaiian or Other Pacific Islander | 88.31314 | 
| Unknown | 43.53510 | 
| White | 439.69293 |

> Note that this example is used only demonstrating purposes only and will most likely never be used in real life. It just gives the idea of what `reduceSum` can do with other forms of data. 

Crossfilter documentation can be found here: https://animateddata.co.uk/articles/crossfilter/