# Aggregate Functions and Grouping

## Learning Goals

- Use aggregate functions `count`, `sum`, `min`, `max`, and `average`.
- Use the `GROUP BY` clause to group rows with the same value in one or more columns.
- Use the `HAVING` clause to select a subset of grouped rows matching a predicate.

## Introduction

```sql
SELECT [comma-separated list of columns and/or aggregate functions]
FROM [table_name]
WHERE [row selection predicate]
GROUP BY [comma-separated list of columns]
HAVING [aggregate function selection predicate]
ORDER BY [comma-separated list of columns]
LIMIT [count];
```

The basic syntax of the `SELECT` statement is enhanced to support aggregate functions and grouping:

- The `SELECT` clause may include one or more aggregate function calls.
- The `GROUP BY` clause is optional.  The clause groups sets of rows that contain the same value
  within one or more columns.
- The `HAVING` clause is optional. The clause selects a subset of grouped rows that match a predicate.

## Table Setup

We will work with the same `pet` table from the previous lesson.  
The statements to create the table and insert 8 rows is provided below:

```sql
DROP TABLE IF EXISTS pet;

CREATE TABLE pet (
        id  INTEGER PRIMARY KEY,
        name TEXT,
        species TEXT,
        breed TEXT,
        age INTEGER
);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (1, 'Moe', 'cat', 'Tabby', 10);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (2, 'Hana', 'cat', 'Tabby', 1);

-- Use two single quotes '' to escape a single quote character in the name
INSERT INTO pet (id, name, species, breed, age) 
VALUES (3, 'Lil'' Bub', 'cat', 'American Shorthair', 5);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (4, 'Snuggles', 'dog', 'Bichon', 5);

--unknown age
INSERT INTO pet (id, name, species, breed)
VALUES (5, 'Honey', 'dog', 'Cavachon');

-- unknown breed
INSERT INTO pet (id, name, species, age)
VALUES (6, 'Herbie', 'fish', 4);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (7, 'Fifi', 'dog', 'Poodle', 1);

INSERT INTO pet (id, name, species, breed, age) 
VALUES (8, 'Lulu', 'dog', 'Yorkiepoo', 12);
```


## Aggregate Functions  (Code Along)

SQL provides functions for aggregating values across multiple rows:

- `COUNT(col)` - return a count of non-null values in column `col`.
- `SUM(col)` - return the sum of values in column `col`.
- `MIN(col)` - return the minimum value in column `col`.
- `MAX(col)` - return the maximum value in column `col`.
- `AVG(col)` - return the average value in column `col`.


<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Count all rows:

<pre>
<code>
SELECT COUNT(*)
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
count

8
</pre>

</td>
</tr>

<tr>
<td>
Count rows
with a non-null value in name:

<pre>
<code>
SELECT COUNT(name)
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
count

8
</pre>

</td>
</tr>

<tr>
<td>
Count rows
with a non-null value in age:

<pre>
<code>
SELECT COUNT(age)
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
count

7
</pre>

</td>
</tr>

<tr>
<td>
Count of distinct species:

<pre>
<code>
SELECT COUNT(DISTINCT species)
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
count

3
</pre>

</td>
</tr>

<tr>
<td>
Count the number of dogs:

<pre>
<code>
SELECT COUNT(*)
FROM pet
WHERE species = 'dog';
</code>
</pre>

</td>
<td>

<pre>
count

4
</pre>

</td>
</tr>

<tr>
<td>
Sum the ages of all pets:

<pre>
<code>
SELECT SUM(age)
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
sum

38
</pre>

</td>
</tr>

<tr>
<td>
Sum the ages of Tabby cats:

<pre>
<code>
SELECT SUM(age)
FROM pet
WHERE breed = 'Tabby' AND species = 'cat' ;
</code>
</pre>

</td>
<td>

<pre>
sum

11
</pre>

</td>
</tr>

<tr>
<td>
Minimum, maximum and average age
among all pets:

<pre>
<code>
SELECT MIN(age), MAX(age), AVG(age)
FROM pet;
</code>
</pre>

</td>
<td>

<pre>
min max avg

1   12  5.428571428    
</pre>

</td>
</tr>

<tr>
<td>
Minimum, maximum and average age
among all cats:

<pre>
<code>
SELECT MIN(age), MAX(age), AVG(age)
FROM pet
WHERE species = 'cat';
</code>
</pre>

</td>
<td>

<pre>
min max avg

1   10  5.333333333
</pre>

</td>
</tr>


<tr>
<td>
Truncate to display 2 digits after decimal point:

<pre>
<code>
SELECT MIN(age), MAX(age), TRUNC(AVG(age),2) 
FROM pet
WHERE species = 'cat';
</code>
</pre>

</td>
<td>

<pre>
min max avg

1   10  5.33
</pre>

</td>
</tr>

</table>



## GROUP BY

The `GROUP BY` clause collects rows with the same value for a column into a group.


```text
id  name        species	breed	            age

1   Moe         cat     Tabby	            10
2   Hana        cat     Tabby	            1
3   Lil' Bub    cat     American Shorthair  5
4   Snuggles    dog     Bichon	            5
5   Honey       dog     Cavachon            NULL
6   Herbie      fish    NULL                4
7   Fifi        dog     Poodle              1
8   Lulu        dog     Yorkiepoo           12
```


For example, grouping the `pet` table by `species` creates three groups of rows:

- cat group: rows with id 1, 2, and 3
- dog group: rows with id 4, 5, 7, and 8
- fish group: row with id 6

We often use the `GROUP BY` clause with an aggregate function
to calculate a measure for each group.  The `SELECT` clause
should list the grouped column, along with one or more aggregate
functions.  

NOTE: Your row order may differ from the output displayed below.

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Display a count of rows per species:

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
GROUP BY species;
</code>
</pre>

</td>
<td>

<pre>
species count

dog     4
cat     3
fish    1
</pre>

</td>
</tr>


<tr>
<td>
Sort the species count
in ascending order:

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
GROUP BY species
ORDER BY count;
</code>
</pre>

</td>
<td>

<pre>
species count

fish    1
cat     3
dog     4
</pre>

</td>
</tr>



<tr>
<td>
Count rows per age,
omitting null values:

<pre>
<code>
SELECT age, COUNT(*)
FROM pet
WHERE age IS NOT NULL
GROUP BY age;
</code>
</pre>

</td>
<td>

<pre>
age count

5   2
4   1
10  1
12  1
1   2
</pre>

</td>
</tr>

<tr>
<td>
Sort the age count
in descending order:

<pre>
<code>
SELECT age, COUNT(*)
FROM pet
WHERE age IS NOT NULL
GROUP BY age
ORDER BY count DESC;
</code>
</pre>

</td>
<td>

<pre>
age count

5   2
1   2
4   1
10  1
12  1
</pre>

</td>
</tr>


<tr>
<td>
Min and max age per species:

<pre>
<code>
SELECT species, MIN(age), MAX(age)
FROM pet
GROUP BY species;
</code>
</pre>

</td>
<td>

<pre>
species min max

dog     1   12
cat     1   10
fish    4   4
</pre>

</td>
</tr>

</table>


NOTE: Not all databases label the aggregate column `count`, `min`, etc.
We can set the label of a column in a query result using the `AS` keyword.
The new label can be used for ordering if necessary:

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Display a count of rows per species:

<pre>
<code>
SELECT species, COUNT(*) AS species_count
FROM pet
GROUP BY species
ORDER BY species_count;
</code>
</pre>

</td>
<td>

<pre>
species species_count

fish    1
cat     3
dog     4
</pre>

</td>
</tr>

</table>



## HAVING

The `WHERE` clause is used to select a subset of table rows that
match a predicate.  However, the `WHERE` clause can't be used
on the value produced by an aggregate function.
Instead, we use the `HAVING` clause to test  an aggregate function result.
Groups that match the `HAVING` predicate will be included in the result.


NOTE: The aggregate function call must be used in the `HAVING` clause,
i.e. `COUNT(*)`.  Do not use the column label `count` shown in the output.

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>

Display species count:

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
GROUP BY species;
</code>
</pre>

</td>
<td>

<pre>
species count

dog     4
cat     3
fish    1
</pre>

</td>
</tr>

<tr>
<td>

Display species counts
that exceed 1:

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
GROUP BY species
HAVING COUNT(*) > 1;
</code>
</pre>

</td>
<td>

<pre>
species count

dog     4
cat     3
</pre>

</td>
</tr>


<tr>
<td>

Display species counts
that exceed 1 sorted
in ascending order:

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
GROUP BY species
HAVING COUNT(*) > 1
ORDER BY count;
</code>
</pre>

</td>
<td>

<pre>
species count

cat     3
dog     4
</pre>

</td>
</tr>


<tr>
<td>

Omit rows with null
values for age from
the species count:

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
WHERE age IS NOT NULL
GROUP BY species
HAVING COUNT(*) > 1;
</code>
</pre>

</td>
<td>

<pre>
species count

dog     3
cat     3
</pre>

</td>
</tr>


</table>




### Common errors


If you omit the grouped column from the `SELECT` statement,
the query result is not useful since we don't know which group
a particular value corresponds to:

<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>
Omit species in SELECT clause

<pre>
<code>
SELECT COUNT(*)
FROM pet
GROUP BY species
</code>
</pre>

</td>
<td>

<pre>
count

4
3
1
</pre>

</td>
</tr>
</table>


Another common error is to list a column along with an aggregate
function in the `SELECT` clause without specifying a `GROUP BY`
clause for that same column.

The query below will result in an error because it is missing
`GROUP BY species`:


<table>
<tr>
<th>
Query
</th>
<th>
Result
</th>
</tr>

<tr>
<td>

<pre>
<code>
SELECT species, COUNT(*)
FROM pet
</code>
</pre>

</td>
<td>

<pre>
ERROR:  column "pet.species" must appear in the GROUP BY clause
 or be used in an aggregate function
</pre>

</td>
</tr>
</table>




## Conclusion

An aggregate function is used to compute a measure
across one or more groups of rows.  The `GROUP BY`
clause groups a set of rows containing the same value in
one or more columns.  The `HAVING` clause restricts the
result to those groups that match a predicate.


## Resources

- [PostgreSQL Aggregate Functions](https://www.postgresql.org/docs/9.5/functions-aggregate.html)       
- [PostgreSQL GROUP BY](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-group-by/)     
- [PostgreSQL HAVING](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-having/)  

