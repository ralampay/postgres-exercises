# Slow Queries

Demonstrates how to monitor slow queries. Make sure to setup the following to start tracking slow queries:

In your `postgresql.conf` file:

```
shared_preload_libraries = 'pg_stat_statements'

pg_stat_statements.track = all
```

## 1. Create the Necessary Setup

First create a database called `slow_queries_exercise`:

```
CREATE DATABASE slow_queries_exercise;
```

Next create the necessary tables:

```
-- Create Teachers table
CREATE TABLE teachers (
    id SERIAL PRIMARY KEY,
    teacher_name VARCHAR(100),
    subject_id INT
);

-- Create Students table
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    student_name VARCHAR(100),
    age INT,
    grade VARCHAR(2)
);

-- Create Subjects table
CREATE TABLE subjects (
    id SERIAL PRIMARY KEY,
    subject_name VARCHAR(100)
);
```

Insert some random data:

```
-- Insert random data into Teachers table
INSERT INTO teachers (teacher_name, subject_id)
SELECT
    'Teacher' || generate_series(1, 100),
    (random() * 10 + 1)::int
FROM generate_series(1, 100);

-- Insert random data into Students table
INSERT INTO students (student_name, age, grade)
SELECT
    'Student' || generate_series(1, 500),
    (random() * 10 + 15)::int,
    chr((random() * 5 + 65)::int)
FROM generate_series(1, 500);

-- Insert random data into Subjects table
INSERT INTO subjects (subject_name)
SELECT 'Subject' || generate_series(1, 10);
```

## 2. Perform Some Complex Queries

### Query 1: Find the average age of students in each grade

```
SELECT grade, AVG(age) AS average_age
FROM students
GROUP BY grade;
```

### Query 2: List teachers along with the subjects they teach 

```
SELECT teacher_name, subject_name
FROM teachers
JOIN subjects ON teachers.subject_id = subjects.id;
```

### Query 3: Count the number of students in each grade for a specific subject.

```
SELECT s.grade, COUNT(*) AS student_count
FROM students s
JOIN teachers t ON s.grade = t.grade
WHERE t.subject_id = 3 -- Replace with the subject_id of your choice
GROUP BY s.grade;
```

### Query 4: Find the subject with the most teachers

```
SELECT subject_name, COUNT(*) AS teacher_count
FROM teachers
JOIN subjects ON teachers.subject_id = subjects.id
GROUP BY subject_name
ORDER BY teacher_count DESC
LIMIT 1;
```

### Query 5: Identify students who have teachers teaching multiple subjects


```
SELECT student_name, COUNT(DISTINCT teachers.subject_id) AS unique_subject_count
FROM students
JOIN teachers ON students.grade = teachers.grade
GROUP BY student_name
HAVING COUNT(DISTINCT teachers.subject_id) > 1;
```

## 3. Write a query to select the top 3 slowest queries:

```
SELECT
    query,
    calls,
    total_exec_time,
    rows,
    mean_exec_time
FROM
    pg_stat_statements
ORDER BY
    total_time DESC
LIMIT 3;
```

## 4. Provide an indexing solution for the slowest queries

Make sure to perform the following:
* Perform `EXPLAIN ANALYZE` for each of the slow queries
* Create the appropriate index
* Perform `EXPLAIN ANALYZE` to check for any improvement
* Run the previous query in number 3 to determine if it is still considered a slow query.
