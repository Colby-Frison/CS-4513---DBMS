# SQL (Structured Query Language) - Part 2: Complete Notes

## Overview
**Source**: Database System Concepts by Silberschatz, Korth, and Sudarshan  
**Topic Coverage**: Comprehensive SQL language fundamentals and advanced features

## Table of Contents
1. SQL History and Overview
2. Data Definition Language (DDL)
3. Data Manipulation Language (DML)
4. Basic Query Structure
5. Advanced Query Operations
6. Set Operations
7. Null Values and Three-Valued Logic
8. Aggregate Functions
9. Nested Subqueries
10. Database Modification
11. Practical Examples and Poll Questions

## 1. SQL History and Overview

### Historical Development
- **IBM Sequel** language developed as part of System R project at IBM San Jose Research Laboratory
- Renamed to **Structured Query Language (SQL)**
- **ANSI and ISO Standards**:
  - SQL-86, SQL-89, SQL-92
  - SQL:1999, SQL:2003, SQL:2008
- Commercial systems offer most SQL-92 features plus proprietary extensions

### SQL Characteristics
- **Declarative Programming Language**: Specify what data you want, not how to get it
- **Two Main Components**:
  - **DDL (Data Definition Language)**: Create, alter, remove tables
  - **DML (Data Manipulation Language)**: CRUD operations (Create, Read, Update, Delete)

## 2. Data Definition Language (DDL)

### Purpose
DDL allows specification of:
- Schema for each relation
- Domain of values for each attribute
- Integrity constraints
- Indices for each relation
- Security and authorization information
- Physical storage structure

### Domain Types in SQL
- **char(n)**: Fixed length character string
- **varchar(n)**: Variable length character string
- **int**: Integer (machine-dependent)
- **smallint**: Small integer
- **numeric(p,d)**: Fixed point number with precision
- **real, double precision**: Floating point numbers
- **float(n)**: Floating point with specified precision

### Create Table Construct
```sql
create table r (
    A₁ D₁,
    A₂ D₂,
    ...,
    Aₙ Dₙ,
    (integrity-constraint₁),
    ...,
    (integrity-constraintₖ)
)
```

### Integrity Constraints
- **not null**: Attribute cannot be null
- **primary key (A₁, ..., Aₙ)**: Defines primary key
- **foreign key (Aₘ, ..., Aₙ) references r**: Defines foreign key

**Example**:
```sql
create table instructor (
    ID varchar(5),
    name varchar(20) not null,
    dept_name varchar(20),
    salary numeric(8,2),
    primary key (ID),
    foreign key (dept_name) references department
)
```

**Note**: Primary key declaration automatically ensures not null and unique

### Table Modification
- **drop table student**: Deletes table and contents
- **delete from student**: Deletes contents but retains table (DML)
- **alter table r add A D**: Adds new attribute
- **alter table r drop A**: Drops attribute (not widely supported)

## 3. Data Manipulation Language (DML)

### Basic Query Structure
```sql
select A₁, A₂, ..., Aₙ
from r₁, r₂, ..., rₘ
where P
```
- **Aᵢ**: Attributes
- **Rᵢ**: Relations
- **P**: Predicate
- **Result**: Always a relation

## 4. Basic Query Structure Components

### SELECT Clause
- Lists desired attributes in result
- **Case insensitive**: Name = NAME = name
- **Duplicates**: Allowed by default
- **distinct**: Eliminates duplicates
- **all**: Explicitly keeps duplicates (default)
- *****: Selects all attributes
- **Arithmetic expressions**: Can use +, -, *, /

**Examples**:
```sql
select name from instructor
select distinct dept_name from instructor
select ID, name, salary/12 from instructor
```

### WHERE Clause
- Specifies conditions result must satisfy
- Corresponds to relational algebra selection
- Uses logical connectives: and, or, not

**Example**:
```sql
select name
from instructor
where dept_name = 'Comp. Sci.' and salary > 80000
```

### FROM Clause
- Lists relations involved in query
- **Cartesian product**: Generated when multiple relations listed
- Combined with WHERE clause for useful results

**Example**:
```sql
select * from instructor, teaches
```

### Joins
- Combine data from multiple relations
- **Equijoin**: Matching tuples based on equality

**Examples**:
```sql
-- Natural join
select name, course_id
from instructor, teaches
where instructor.ID = teaches.ID

-- Multi-table join with conditions
select section.course_id, semester, year, title
from section, course
where section.course_id = course.course_id 
  and dept_name = 'Comp. Sci.'
```

### Rename Operation (AS Clause)
- Renames relations and attributes
- **as** keyword optional (required to be omitted in Oracle)

**Examples**:
```sql
select ID, name, salary/12 as monthly_salary
from instructor

-- Self-join with correlation variables
select distinct T.name
from instructor as T, instructor as S
where T.salary > S.salary and S.dept_name = 'Comp. Sci.'
```

### ORDER BY Clause
- Controls display order of tuples
- **asc**: Ascending order (default)
- **desc**: Descending order
- Multiple attributes supported

**Example**:
```sql
select distinct name
from instructor
order by name desc
```

### BETWEEN Operator
- Range comparison
- Inclusive of endpoints

**Example**:
```sql
select name
from instructor
where salary between 90000 and 100000
```

## 5. Advanced Query Operations

### String Operations
- Pattern matching with **like**
- **%**: Matches any substring
- **_**: Matches any single character

### Tuple Comparison
- Compare multiple attributes simultaneously

**Example**:
```sql
select name, course_id
from instructor, teaches
where (instructor.ID, dept_name) = (teaches.ID, 'Biology')
```

## 6. Set Operations

### Basic Set Operations
- **union**: Combines results, eliminates duplicates
- **intersect**: Finds common tuples
- **except**: Finds tuples in first but not second
- **union all**, **intersect all**, **except all**: Keep duplicates

**Examples**:
```sql
-- Courses in Fall 2009 OR Spring 2010
select course_id from section 
where semester = 'Fall' and year = 2009
union
select course_id from section 
where semester = 'Spring' and year = 2010

-- Courses in Fall 2009 AND Spring 2010
select course_id from section 
where semester = 'Fall' and year = 2009
intersect
select course_id from section 
where semester = 'Spring' and year = 2010

-- Courses in Fall 2009 BUT NOT Spring 2010
select course_id from section 
where semester = 'Fall' and year = 2009
except
select course_id from section 
where semester = 'Spring' and year = 2010
```

**Note**: Use **minus** instead of **except** in Oracle

## 7. Null Values and Three-Valued Logic

### Null Value Properties
- Signifies unknown value or non-existent value
- **Arithmetic with null**: Any operation returns null
  - Example: `5 + null` returns null
- **Comparison with null**: Returns unknown

### Three-Valued Logic
- **Truth values**: true, false, unknown
- **OR**:
  - (unknown or true) = true
  - (unknown or false) = unknown
  - (unknown or unknown) = unknown
- **AND**:
  - (true and unknown) = unknown
  - (false and unknown) = false
  - (unknown and unknown) = unknown
- **NOT**: (not unknown) = unknown

### Testing for Null
- **is null**: Checks for null values
- **is not null**: Checks for non-null values

**Example**:
```sql
select name
from instructor
where salary is null
```

### WHERE Clause Behavior
- Predicate treated as **false** if it evaluates to unknown

## 8. Aggregate Functions

### Available Functions
- **avg**: Average value
- **min**: Minimum value
- **max**: Maximum value
- **sum**: Sum of values
- **count**: Number of values

### Basic Examples
```sql
-- Average salary in Computer Science
select avg(salary)
from instructor
where dept_name = 'Comp. Sci.'

-- Count instructors teaching in Spring 2010
select count(distinct ID)
from teaches
where semester = 'Spring' and year = 2010

-- Count all courses
select count(*) from course
```

### GROUP BY Clause
- Groups tuples by specified attributes
- Non-aggregated attributes in SELECT must be in GROUP BY

**Example**:
```sql
select dept_name, avg(salary)
from instructor
group by dept_name
```

### HAVING Clause
- Applies conditions to groups (after group formation)
- WHERE clause applies before group formation

**Example**:
```sql
select dept_name, avg(salary)
from instructor
group by dept_name
having avg(salary) > 42000
```

### Null Values and Aggregates
- All aggregates except **count(*)** ignore null values
- **count** returns 0 if only null values
- Other aggregates return null if only null values

## 9. Nested Subqueries

### Subquery Concepts
- **Subquery**: SELECT-FROM-WHERE expression nested within another query
- Used for set membership, comparisons, and cardinality tests

### Set Membership (IN, NOT IN)
**Examples**:
```sql
-- Courses in Fall 2009 AND Spring 2010
select distinct course_id
from section
where semester = 'Fall' and year = 2009
  and course_id in (
    select course_id
    from section
    where semester = 'Spring' and year = 2010
  )

-- Courses in Fall 2009 BUT NOT Spring 2010
select distinct course_id
from section
where semester = 'Fall' and year = 2009
  and course_id not in (
    select course_id
    from section
    where semester = 'Spring' and year = 2010
  )
```

### Set Comparison (SOME, ALL)
- **> some**: Greater than at least one
- **> all**: Greater than all

**Examples**:
```sql
-- Salary greater than some Biology instructor
select name
from instructor
where salary > some (
  select salary
  from instructor
  where dept_name = 'Biology'
)

-- Salary greater than all Biology instructors
select name
from instructor
where salary > all (
  select salary
  from instructor
  where dept_name = 'Biology'
)
```

### Test for Empty Relations (EXISTS, NOT EXISTS)
- **exists**: Returns true if subquery non-empty
- **not exists**: Returns true if subquery empty

**Examples**:
```sql
-- Courses in both Fall 2009 and Spring 2010
select course_id
from section as S
where semester = 'Fall' and year = 2009
  and exists (
    select *
    from section as T
    where semester = 'Spring' and year = 2010
      and S.course_id = T.course_id
  )

-- Students who took ALL Biology courses
select distinct S.ID, S.name
from student as S
where not exists (
  (select course_id
   from course
   where dept_name = 'Biology')
  except
  (select T.course_id
   from takes as T
   where S.ID = T.ID)
)
```

### Correlation Variables
- Subqueries can reference attributes from outer query
- Enables complex correlated subqueries

### Subqueries in FROM Clause
- Use subquery results as relations

**Example**:
```sql
select dept_name, avg_salary
from (
  select dept_name, avg(salary) as avg_salary
  from instructor
  group by dept_name
)
where avg_salary > 42000
```

### Scalar Subqueries
- Return single value
- Used where single value expected
- Runtime error if returns multiple tuples

**Examples**:
```sql
-- Department with instructor count
select dept_name,
  (select count(*)
   from instructor
   where department.dept_name = instructor.dept_name)
   as num_instructors
from department

-- Instructors with salary > 10% of department budget
select name
from instructor
where salary * 10 > (
  select budget
  from department
  where department.dept_name = instructor.dept_name
)
```

## 10. Database Modification

### Deletion
**Examples**:
```sql
-- Delete all instructors
delete from instructor

-- Delete Finance department instructors
delete from instructor
where dept_name = 'Finance'

-- Delete instructors in Watson building
delete from instructor
where dept_name in (
  select dept_name
  from department
  where building = 'Watson'
)

-- Delete instructors with below-average salary (with CTE)
with avg_salary as (
  select avg(salary) as avg_sal from instructor
)
delete from instructor
where salary < (select avg_sal from avg_salary)
```

### Insertion
**Examples**:
```sql
-- Insert single tuple
insert into course
values ('CS-437', 'Database Systems', 'Comp. Sci.', 4)

-- Explicit attribute list
insert into course (course_id, title, dept_name, credits)
values ('CS-437', 'Database Systems', 'Comp. Sci.', 4)

-- Insert with null values
insert into student
values ('3003', 'Green', 'Finance', null)

-- Insert from query result
insert into student
select ID, name, dept_name, 0
from instructor
```

### Updates
**Examples**:
```sql
-- Conditional update (problematic order)
update instructor
set salary = salary * 1.03
where salary > 100000

update instructor
set salary = salary * 1.05
where salary <= 100000

-- Better: Using CASE statement
update instructor
set salary = case
  when salary <= 100000 then salary * 1.05
  else salary * 1.03
end
```

## 11. Practical Examples and Poll Questions

### Poll Questions Summary

**Poll 6**: SELECT * FROM department returns 3 attributes  
**Poll 7**: SELECT dept_name, building FROM department returns 2 attributes  
**Poll 8**: Join query with 3 instructor tuples and 3 teaches tuples returns 3 rows  
**Poll 9**: Query results allow duplicate tuples (True)  
**Poll 10**: Non-aggregated column not in GROUP BY causes error  
**Poll 11**: EXISTS returns only customers with at least one order  
**Poll 12**: Customers with more than 1 order returns 2 rows  
**Poll 13**: GROUP BY product_id returns 4 rows  
**Poll 14**: Scalar subquery with customer orders returns 3 rows  
**Poll 15**: Complex customer query returns 1 column

### Practice Examples
The slides include multiple practice scenarios with customer and order tables demonstrating:
- Subqueries with COUNT and EXISTS
- GROUP BY with HAVING
- Scalar subqueries
- Complex nested queries

## Key Takeaways

1. **SQL is declarative**: Specify what you want, not how to get it
2. **Two main components**: DDL for structure, DML for data
3. **Query structure**: SELECT-FROM-WHERE forms the foundation
4. **Joins combine data**: From multiple relations using conditions
5. **Set operations**: UNION, INTERSECT, EXCEPT for combining results
6. **Three-valued logic**: True, false, unknown with null values
7. **Aggregates summarize data**: With GROUP BY and HAVING
8. **Subqueries enable complexity**: For set operations and correlations
9. **Database modification**: INSERT, UPDATE, DELETE with query support
10. **Order matters**: Especially in updates and complex queries

This comprehensive coverage ensures understanding of SQL from basic queries to advanced features like nested subqueries and complex modifications.