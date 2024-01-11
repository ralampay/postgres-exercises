# Concurrent Connections

Allows us to examine the behavior of PostgreSQL in different loads and make adjustments accordingly.

## 1. Verify the Ugly State

In your PostgreSQL database server, determine the limit of your configuration using pgbench:

```
# Run pgbench with default settings to create a performance bottleneck
pgbench -i -s 10 mydatabase  # Replace "mydatabase" with your actual database name
pgbench -c 10 -T 60 -S mydatabase  # Simulate a scenario with 10 concurrent clients for 60 seconds
```

## 2. Optimize the Configuration

### Option 1: Adjust the `postgresql.conf` parameters:

```
shared_buffers = 256MB
effective_cache_size = 512MB
```

### Option 2: Adjust from PostgreSQL

```
-- Adjust the shared_buffers parameter (e.g., set it to 25% of available RAM)
ALTER SYSTEM SET shared_buffers = '256MB';

-- Adjust the effective_cache_size parameter (e.g., set it to 50% of available RAM)
ALTER SYSTEM SET effective_cache_size = '512MB';

-- Restart PostgreSQL for the changes to take effect
SELECT pg_reload_conf();
```

## 3. Check if the state is Ideal

```
pgbench -i -s 10 mydatabase  # Reload the data with optimized settings
pgbench -c 10 -T 60 -S mydatabase  # Simulate the workload with optimized settings
```

# Options for Monitoring Memory

## 1. `pg_stat_bgwriter`

```
SELECT * FROM pg_stat_bgwriter;
```

## 2. Extract Information about the contents of the `shared_buffer_cache`

```
CREATE EXTENSION pg_buffercache;

SELECT * FROM pg_buffercache;
```

## 3. Linux Related Commands

* `top`
* `free -m`
