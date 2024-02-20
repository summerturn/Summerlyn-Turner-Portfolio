# Walkthrough

## Ticket #1

### Issue

Hi folks,
I wanted to add interactivity to my map, but for some reason I can't make my popups work. What am I doing wrong? Please find my code here. Thanks!
Please find my code [here](https://gist.github.com/pablomoniz/51568ba2dbfdba51ecfe35904e361a07#file-index-html), and a live version [here](https://bl.ocks.org/pablomoniz/raw/51568ba2dbfdba51ecfe35904e361a07). Thanks!

### Solution

Please take a look at my [Revised File](https://github.com/summert21/CARTO-skills-test/blob/master/Support-Tickets/PopUpFix.html)!

``` 
        popup.setContent(`
        <h3> ${data.name} </h3>
        <p>Between ${data.pop_min} and ${data.pop_max} inhabitants</p>
        </div>`);
```

To fix the pop ups, I added a setContent function that would allow the population min and max as well as the region name while maintaining proper syntax.

## Ticket #2

### Issue

Hello Support,
I'm going through your materials, I've seen this query somewhere, and I'd need some help from your side on understanding what it does, can you give me a hand?

```sql
      SELECT e.name,
             count(*) AS counts,
             sum(p.population) as population
        FROM european_countries e
        JOIN populated_places p
          ON ST_Intersects(p.the_geom, e.the_geom)
    GROUP BY e.name
``` 

### Solution

This SQL query pulls the name (country name in this case), a count of all the rows in the european_countries table and names the resulting column "counts", and the sum of population numbers in the populated_places table and names the resulting column "population."

The base table is european_countries which is providing the "name" and "counts" columns.

The join table is populated_places which is providing the population data which is summed.

These two tables are join by a match in a column called "the_geom" in the european_countries table and another column called "the_geom" in the populated_places table.

The e and the p in front of the table names are aliases. These aliases can be defined instead of having to use the entire table name.

The data is then being grouped by name.

So the resulting table would most likely look like this:

             name       | count | population
        _____________________________________
        country_name1   | 39203 | 29018727
        country_name2   | 56398 | 54039837
        country_name3   | 8941  | 6039826

