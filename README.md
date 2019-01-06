# zebras - data analysis library for javascript

<div align="center"><img src ="code_example.png" width = "60%" /></div>

Zebras is a data manipulation and analysis library written in JavaScript offering the convenience of pandas or R. 

Zebras is especially useful for JS data analysis and visualization projects in notebooks on [ObservableHQ](https://beta.observablehq.com/) or using the [JS Jupyter kernel](https://github.com/n-riesco/ijavascript). 

Unlike pandas, zebras is purely functional. All zebras functions accept data as an argument and return a new data structure transformed, and 'dataframes' are simply arrays of JS objects without any special methods. 

Zebras is built on top of [Ramda](https://github.com/Ramda/ramda). Think of zebras as a collection of Ramda functions, and compositions of them, curated and repackaged for common data processing and data analysis tasks. 

As in Ramda, all zebras functions are curried, making them easier to chain together. This means that, for example, `zebras.sortByCol(col, df)` is equivalent to `zebras.sortByCol(col)(df)`. 

**An interactive introduction to the zebras library - using S&P 500 data - is available [as an Observable notebook](https://beta.observablehq.com/@nickslevine/introduction-to-zebras-a-data-analysis-library-for-javascr).**

**See the project roadmap on the zebras wiki [here](https://github.com/nickslevine/zebras/wiki/Zebras---Project-Roadmap).**

**A guide to using zebras in a JS Jupyter notebook can be found on the zebras wiki [here](https://github.com/nickslevine/zebras/wiki/Zebras-in-a-Jupyter-Notebook).**

## To Install
### Locally with Node / Jupyter
```sh
npm install zebras --save
```

```javascript
const z = require('zebras')
```

### Online in an Observable notebook

```javascript
z = require('https://bundle.run/zebras')
```

## Example Usage
```javascript
const z = require('zebras')
const data = z.readCSV('data.csv')
const parsedData = z.parseNums(['speed','acc'], data)
const speed = z.getCol('speed', parsedData)
const avgSpeed = z.mean(speed)
```

Piping example:

```javascript
const z = require('zebras')
const avgSpeed = z.pipe(
  [           // array of functions for input to travel through
    z.readCSV,
    z.parseNums(['speed','acc']),
    z.getCol('speed'),
    z.mean
  ]
)('data.csv')
```

## Documentation

In the documentation, `df` signifies a zebras dataframe - an array of Javascript objects such as: `[{label: 'A', value: 2}, {label: 'B', value: 4}]`. An `arr` is a simple JavaScript array of values.

### IO

#### Read a CSV file
```javascript
z.readCSV(filepath) 
```

Or if you're working in Observable:
```javascript
d3 = require("d3")
data = d3.csv(csv-url)
```
:point_right: Returns a zebras dataframe - a simple array of JS objects. 

#### Write a dataframe to a CSV file
```javascript
z.toCSV(filepath, df)
```

#### Print dataframe
```javascript
z.print(df)
```
:point_right: Returns the entire dataframe as an ASCII table. If working in a local Node environment, wrap this and other printing functions in `console.log()` to display. ASCII tables look like this:

```
┌────────────┬───────┬───────┬───────┬───────┬───────────┬─────────┐
│ Date       │ Open  │ High  │ Low   │ Close │ Adj Close │ Volume  │
├────────────┼───────┼───────┼───────┼───────┼───────────┼─────────┤
│ 1950-01-03 │ 16.66 │ 16.66 │ 16.66 │ 16.66 │ 16.66     │ 1260000 │
├────────────┼───────┼───────┼───────┼───────┼───────────┼─────────┤
│ 1950-01-04 │ 16.85 │ 16.85 │ 16.85 │ 16.85 │ 16.85     │ 1890000 │
├────────────┼───────┼───────┼───────┼───────┼───────────┼─────────┤
│ 1950-01-05 │ 16.93 │ 16.93 │ 16.93 │ 16.93 │ 16.93     │ 2550000 │
├────────────┼───────┼───────┼───────┼───────┼───────────┼─────────┤
│ 1950-01-06 │ 16.98 │ 16.98 │ 16.98 │ 16.98 │ 16.98     │ 2010000 │
├────────────┼───────┼───────┼───────┼───────┼───────────┼─────────┤
│ 1950-01-09 │ 17.08 │ 17.08 │ 17.08 │ 17.08 │ 17.08     │ 2520000 │
└────────────┴───────┴───────┴───────┴───────┴───────────┴─────────┘
```

#### Print first n rows of dataframe
```javascript
z.head(n, df)
```

#### Print last n rows of dataframe
```javascript
z.tail(n, df)
```

### Manipulating dataframes

#### Filter rows
```javascript
z.filter(func, df)
```
:point_right: `z.filter()` accepts a test function that determines which rows of the supplied dataframe are returned. For example: 
```javascript
const df = [{'label': 'A', 'value': 2}, {'label': 'B', 'value': 10}, {'label': 'C', 'value': 30}]
const dfFiltered = z.filter((r) => r >= 10, df)
// dfFiltered: [{'label': 'B', 'value': 10}, {'label': 'C', 'value': 30}]
```
#### Sort dataframe rows by a column
```javascript
z.sortByCol(columnName, direction, df)
```
:point_right: Set the `direction` argument to `'asc'` for ascending and `'desc'` for descending


#### Sort dataframe rows using custom sorting function
```javascript
z.sort(func, df)
```
:point_right: `z.sort()` accepts a sorting function that determines the order of rows in the dataframe returned. For example:
```javascript
const df = [{'label': 'A', 'value': 7}, {'label': 'B', 'value': 2}, {'label': 'C', 'value': 75}]
const dfSorted = z.sort((a,b) => b.value - a.value, df)
// dfSorted: [{ label: 'C', value: 75 },{ label: 'A', value: 7 },{ label: 'B', value: 2 }] 
```


For more on sorting functions, see [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort). 

#### Convert columns to numerical type
```javascript
z.parseNums(columnNamesArray, df)
```

:point_right: `columnNamesArray` is an array of the names of the columns to be converted to floats. 

#### Convert columns to datestamp
```javascript
z.parseDates(columnNamesArray, df)
```

:point_right: `columnNamesArray` is an array of the names of the columns to be converted to datestamps. 

#### Select a subset of columns
```javascript
z.pickCols(columnNamesArray, df)
```

:point_right: Use this function to select a subset of columns - give it an array with the names of the columns you want to retain. 

#### Delete a column
```javascript
z.dropCol(columnName, df)
```

:point_right: Returns a new dataframe lacking the referenced column.

#### Concatenate two dataframes
```javascript
z.concat(df1, df2)
```

#### Get rows by index
```javascript
z.slice(start, end, df)
```

#### Add a new column to a dataframe from an array
```javascript
z.addCol(columnName, arr, df)
```

:point_right: Use this function to add an array as a new column in a dataframe. Make sure the array has the same length as the number of rows in the dataframe.  

#### Create a new array based on columns from existing array
```javascript
z.deriveCol(func, df)
```

:point_right: Use to create new columns derived from existing columns in a dataframe. For example:
```javascript
const df = [{'date': '1990-05-06', 'tempCelsius': 0},{'date': '1990-05-07', 'tempCelsius': 4},{'date': '1990-05-08', 'tempCelsius': 6}]
const fahrenheit = z.deriveCol((r) => r.tempCelsius * 1.8 + 32, df) // => [ 32, 39.2, 42.8 ]
const dfWithFahrenheit = z.addCol('tempFahrenheit', fahrenheit, df) // => new df includes the 'tempFahrenheit' column
```

#### Join two dataframes on a column

```javascript
z.merge(dfLeft, dfRight, leftOn, rightOn, leftSuffix, rightSuffix)
```

:point_right: Performs a left join on two dataframes. The 'On' arguments set which column in each df to join on. The 'Suffix' arguments determine what the suffix should be when the two dataframes have overlapping column names besides the one being joined on. 

### Analyzing series

#### Extract a series to an array from a dataframe
```javascript
z.getCol(columnName, df)
```
For example:

```javascript
const df = [{'date': '1990-05-06', 'tempCelsius': 0},{'date': '1990-05-07', 'tempCelsius': 4},{'date': '1990-05-08', 'tempCelsius': 6}]
const dates = z.getCol('date', df) // => ['1990-05-06', '1990-05-07', '1990-05-08']
```


#### Min of series:
```javascript
z.min(arr)
```

#### Max of series
```javascript
z.max(arr)
```

#### Sum of series:
```javascript
z.sum(arr)
```

#### Product of series:
```javascript
z.prod(arr)
```

#### Range of series
```javascript
z.getRange(arr)
```

:point_right: Returns an array of the min and max of the series: `[min, max]`

#### Mean of series
```javascript
z.mean(arr)
```

#### Median of series
```javascript
z.median(arr)
```

#### Standard deviation of series
```javascript
z.std(arr)
```

#### Skew of a series
```javascript
z.skew(arr)
```

#### Kurtosis of a series
```javascript
z.kurt(arr)
```
#### Correlation between two series
```javascript
z.corr(arr1,arr2)
```

#### Percent changes
```javascript
z.pctChange(arr)
```

:point_right: Returns a new series with the percent changes between the values in order of the input series. For example:

```javascript
const arr = [10, 15, 20, 25, 50, 55]
const prcChanges = z.pctChange(arr) // => [NaN, .5, .333, .25, 1, .1]
```

#### Differences
```javascript
z.diff(arr)
```

:point_right: Returns a new series with the differences between the values in the order of the input series. 

#### Calculate rolling statistics 
```javascript
z.rolling(func, n, arr)
```
:point_right: Calculate statistics over a moving window. Works with z.min, z.max, z.mean, z.std, z.sum, z.prod, or any other function that takes an array as a single argument.

#### Calculate cumulative statistics

```javascript
z.cumulative(func, arr)
```

:point_right: Calculate statistics over a cumulative window from the start of the array. Works wtih z.min, z.max, z.mean, z.std, z.sum, z.prod, etc., or any other function that takes an array as a single argument.

#### Get unique values in a series
```javascript
z.unique(arr)
```

#### Count number of unique values in a series
```javascript
z.countUnique(arr)
```

#### Count number of occurences of each value in a series
```javascript
z.valueCounts(arr)
```

#### Calculate summary statistics for a numerical series
```javascript
z.describe(arr)
```

:point_right: Returns a single-row df with count, unique count, min, max, median, mean and standard deviation of a numerical series.

```
┌───────┬─────────────┬──────────┬─────────┬─────────┬─────────┬─────────┐
│ count │ countUnique │ min      │ max     │ median  │ mean    │ std     │
├───────┼─────────────┼──────────┼─────────┼─────────┼─────────┼─────────┤
│ 17356 │ 17196       │ -0.20467 │ 0.11580 │ 0.00046 │ 0.00033 │ 0.00962 │
└───────┴─────────────┴──────────┴─────────┴─────────┴─────────┴─────────┘
```

#### Create groupBy object
```javascript
z.groupBy(func, df)
```

:point_right: Takes a function and returns a groupBy object grouped according to the function. The form of a groupBy object is a JS object whose keys are the groups and whose values are dataframes containing the group's rows. For example:

```javascript
const df = [{'Day': 'Monday', 'value': 10}, {'Day': 'Tuesday', 'value': 5}, {'Day': 'Monday', 'value': 7}]
const dfGrouped = z.groupBy(x => x.Day, df)
// dfGrouped = {
//  'Monday': [{'Day': 'Monday', 'value': 10}, {'Day': 'Monday', 'value': 7}], 
//  'Tuesday': [{'Day': 'Tuesday', 'value': 5}]
// }
```

##### groupBy object functions
```javascript
z.gbMin(col, groupByObject)
z.gbMax(col, groupByObject)
z.gbCount(col, groupByObject)
z.gbSum(col, groupByObject)
z.gbMean(col, groupByObject)
z.gbStd(col, groupByObject)
z.gbDescribe(col, groupByObject)
```

:point_right: Use these functions on groupBy objects - the output of z.groupBy() - to ananalyze groups. The 'col' argument determines the column within the groups to be analyzed. GroupBy functions return a df with the calculated statistics. For example: 

```javascript
z.gbDescribe('Returns', stocksGroupedByDay) // => daily S&P 500 stock returns grouped by day of the week
```

The output:

```
┌───────────┬──────────────────────┬─────────────────────┬───────┬────────────────────┬────────────────────────┬──────────────────────┐
│ group     │ min                  │ max                 │ count │ sum                │ mean                   │ std                  │
├───────────┼──────────────────────┼─────────────────────┼───────┼────────────────────┼────────────────────────┼──────────────────────┤
│ Tuesday   │ -0.05739484160042896 │ 0.10789005893857007 │ 3536  │ 1.4631920539924885 │ 0.0004137986578033056  │ 0.009621681367601694 │
├───────────┼──────────────────────┼─────────────────────┼───────┼────────────────────┼────────────────────────┼──────────────────────┤
│ Wednesday │ -0.09034977815503076 │ 0.09099355156869016 │ 3536  │ 2.7012204306809346 │ 0.0007639198050568254  │ 0.009228777204500336 │
├───────────┼──────────────────────┼─────────────────────┼───────┼────────────────────┼────────────────────────┼──────────────────────┤
│ Thursday  │ -0.07616709530292798 │ 0.06921270776786637 │ 3489  │ 1.3406664392802377 │ 0.00038425521332193684 │ 0.009238397490985052 │
├───────────┼──────────────────────┼─────────────────────┼───────┼────────────────────┼────────────────────────┼──────────────────────┤
│ Friday    │ -0.0676830448776905  │ 0.06324760362753801 │ 3467  │ 2.2649118001249873 │ 0.0006532771272353583  │ 0.008747752776127474 │
├───────────┼──────────────────────┼─────────────────────┼───────┼────────────────────┼────────────────────────┼──────────────────────┤
│ Monday    │ -0.20466930860972155 │ 0.11580036960722695 │ 3328  │ -1.98570062292691  │ -0.0005966648506390956 │ 0.011098986740715711 │
└───────────┴──────────────────────┴─────────────────────┴───────┴────────────────────┴────────────────────────┴──────────────────────┘ 
```


### Composition

#### Pipe functions together
```javascript
z.pipe([functions], df)
```

For example:

```javascript
const data = [{'Date': '1997-01-01', 'Value': '12'},{'Date': '1997-01-02', 'Value': '14'},{'Date': '1997-01-03', 'Value': '7'},{'Date': '1997-01-04', 'Value': '112'}]
const dataPiped = z.pipe(
  [
    z.parseNums(['Value']), // converts 'Value' column to floats
    z.getCol('Value'), // extracts 'Value' column to array
    z.mean() // calculates mean of 'Value' array => 36.25
  ]
)(data)
```
