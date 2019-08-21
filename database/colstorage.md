## ROWORIENTEDEXECUTION
### Vertical Partitioning 
This approach creates one physical table for each column in the logical schema, where the ith table has twocolumns, one with values from column i of the logical schema and one with the corresponding value in the position column.
* table1                                           
* primarykey col1 col2 col3
### 
* table1.col1
* primarykey col1_value      
###
* tabl1.col2
* primarykey   col2_value  
### Index-only plans:
build index on predicate column, build second index for predicate less column.
For example, consider the query
SELECT AVG(salary) FROM emp WHERE age>40 – if we
have a composite index with an (age,salary) key, then we can answer
this query directly from this index. If we have separate indices
on (age) and (salary), an index only plan will have to find record-ids
corresponding to records with satisfying ages and then merge this
with the complete list of (record-id, salary) pairs extracted from the (salary) index, which will be much slower
### Materialized Views:
In this approach, we create an optimal set of materialized
views for every query flight in the workload, where the optimal
view for a given flight has only the columns needed to answer
queries in that flight. We do not pre-join columns from different
tables in these views. Our objective with this strategy is to allow
System X to access just the data it needs from disk, avoiding the
overheads of explicitly storing record-id or positions, and storing
tuple headers just once per tuple
## COLUMNORIENTED EXECUTION
### Compression
Compression algorithms perform better on data with low information entropy
* In a columnstore, it is extremely easy to summarize these value repeats and operate directly on this summary. 
* In a row-store, the surrounding data from other attributes significantly complicates this process. 
Thus, in general, compression will have a larger impact on query performance if a high percentage of the columns accessed by that query have some level of order.
### Late Materialization
In a column-store, information about a logical entity (e.g., a person) is stored in multiple locations on disk (e.g. name, e-mail address, phone number, etc. are all stored in separate columns), whereas in a row store such information is usually co-located in a single row of a table.
Take, for example, a query that applies a predicate on two columns and projects a third attribute from all tuples that pass the predicates. In a column-store that uses late materialization, the predicates are applied to the column for each attribute separately and a list of positions (ordinal offsets within a column) of values that passed the predicates are produced. Depending on the predicate selectivity, this list of positions can be represented as a simple array, a bit string (where a 1 in the ith bit indicates that the ith value passed the predicate) or as a set of ranges of positions. These position representations are then intersected (if they are bit-strings, bit-wise AND operations can be used) to create a single position list. This list is then sent to the third column to extract values at the desired positions.