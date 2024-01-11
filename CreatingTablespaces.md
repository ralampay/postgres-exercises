# Creating Tablespaces

Demonstrates how you can create your tablespace in a separate drive in the host server.

## 1. Create a Directory for Tablespace:

```
# Create the directory for the tablespace
sudo mkdir -p /path/to/my_tablespace

# Assign appropriate permissions
sudo chown -R postgres:postgres /path/to/my_tablespace

# Ensure PostgreSQL has access
sudo chmod 700 /path/to/my_tablespace
```

## 2. Create a Tablespace

From `postgresql` run the following query:

```
CREATE TABLESPACE my_tablespace LOCATION '/path/to/my_tablespace';
```

## 3. Create a Table Under the Tablespace

```
CREATE TABLE example_table (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
) TABLESPACE my_tablespace;
```

## 4. Determine the Location of the Tablespace

```
-- Find the location of the tablespace 'my_tablespace'
SELECT spcname, pg_tablespace_location(oid) AS location
FROM pg_tablespace
WHERE spcname = 'my_tablespace';
```

## 5. Verify Symlink Location

```
# Assuming 'base' is the name of the symlink pointing to the tablespace
ls -l /path/to/pgdata/base
```
