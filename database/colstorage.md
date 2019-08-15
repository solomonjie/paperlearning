## ROWORIENTEDEXECUTION
### Vertical Partitioning 
This approach creates one physical table for each column in the logical schema, where the ith table has twocolumns, one with values from column i of the logical schema and one with the corresponding value in the position column.
.table name table1                            table1.col1                tabl1.col2
.col schema primarykey col1 col2 col3    =>   primarykey col1_value      primarykey   col2_value  
