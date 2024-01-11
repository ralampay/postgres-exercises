# Maintenance Vacuum

As a continuation of `SlowQueries`, let's see how we can manage different tablespaces and make sure that we are efficient in terms of storage.

## 1. Create a Tablespace in a separate location

```
CREATE TABLESPACE my_tablespace LOCATION '/path/to/my_tablespace';
```

## 2. Alter a table to a new Tablespace

```
ALTER TABLE my_table SET TABLESPACE new_tablespace;
```

## 3. Monitor the size of the tablespace usage

```
-- Size of tablespaces
SELECT spcname, pg_tablespace_size(oid) AS size FROM pg_tablespace;
```

## 4. Create and Insert some data in your table

This is similar to the previous exercise

```
-- Create a table in the specified tablespace
CREATE TABLE example_table (
    id SERIAL PRIMARY KEY,
    data VARCHAR(100)
) TABLESPACE exercise_tablespace;

-- Insert a large amount of data
INSERT INTO example_table (data)
SELECT 'Data' || generate_series(1, 1000000);
```

## 5. Determine the before size

```
-- Size of the table
SELECT pg_size_pretty(pg_total_relation_size('example_table')) AS table_size;

-- Size of the tablespace
SELECT spcname, pg_size_pretty(pg_tablespace_size('exercise_tablespace')) AS tablespace_size
FROM pg_tablespace
WHERE spcname = 'exercise_tablespace';
```

## 6. Perform the vacuum on the table

```
VACUUM example_table;
```

### Additional Monitoring Commands in Linux
* `du -h /path/to/tablespace`: Checks for size of contents of directory
