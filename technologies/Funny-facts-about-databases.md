# The Reason
While working with databases in the context of Camunda BPM, we encounter some funny facts about databases and how they differ from each other.

## Identifiers
The max length of an identifier differs between dbs:
* Oracle: max of 30 characters for a table/index name
* Oracle: the maximum number of characters to store is restricted to 2000 (so ```Nvarchar2(2000)```) 
* MsSql: the ordering differs from the other databases. Let's assume we have the following entries: {P1:,P2:,P3:,P3:,P4:,P5:,P6:,P7:,P8:,P9:,P10:}. Then the shown ordering is also the one mssql returns. However, all other databases return the follwing ordering: {P10:,P1:,P2:,P3:,P3:,P4:,P5:,P6:,P7:,P8:,P9:}

## DB Specifics

### MySql/MariaDB

If a column of type `TIMESTAMP` is created without any constraint or with constraint `NOT NULL`, when an update statement is executed against the containing table, the value of this column's fields is automatically updated with the current timestamp. To prevent this, columns that need timestamp values should use the `DATETIME` type.
