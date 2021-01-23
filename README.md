# A brief Introduction to Oracle-SQL-PL SQL,taught in CSE216 Database Sessional course, is a SQL practice book written by [Prof. Sukarna Barua](https://cse.buet.ac.bd/faculty/facdetail.php?id=sukarnabarua).The book contains most of the fundamental sql queries using join,subquery,group by,set operation,pl-sql etc.There are 11 chapters in total and several exercises follows.All the exercises are based on HR schema of `ORACLE`.Let's look at the overview of HR schema
![hr_schema](https://lh3.googleusercontent.com/proxy/glVSEQ3auTa6TE3xs75Xqvk7e-fNRZkC6HEse74dxZK_TcuT7hNtmKONnzH92t6SjYoQWOAQUYp9Br_2HNCBUYkJ6A0knlWnZra4oH3Isg)

In this repository,You won't find solutions to all the exercises but you will find what you need.I have left out those ones which are too easy or similar to the ones I listed hereor is easily doable by looking at the examples of this book.

## Chapter 3: Use of Oracle Single Row Functions
### 3.3b
```
Suppose you need to find the number of days each employee worked during the first month of his joining. Write an SQL query to find this information for all employees
```
```sql
SELECT 30+MOD(EXTRACT(MONTH FROM HIRE_DATE),2) - EXTRACT(DAY FROM HIRE_DATE) as day_worked_join_month from EMPLOYEES;
```
### 3.5
```
Print hire dates of all employees in the following formats:
(i) 13th February, 1998 (ii) 13 February, 1998.
```
```sql
SELECT TO_CHAR(HIRE_DATE,'DD MON,YYYY'), CONCAT(CONCAT(SUBSTR(HIRE_DATE,1, 2), 'th'), SUBSTR(TO_CHAR(HIRE_DATE,'DD MON,YYYY'),3,9)) FROM EMPLOYEES;
```
## Chapter 4: Aggregate Functions
### 4.1c
```
Find the minimum, maximum, and average salary of all departments except DEPARTMENT_ID 80. Print DEPARTMENT_ID, minimum, maximum, and average salary.Sort the results in descending order of average salary first, then maximum salary, then minimum salary. Use column alias to rename column names in output for better display
```
```sql
SELECT DEPARTMENT_ID,MAX(SALARY) as mx,min(SALARY) as mn, avg(SALARY) as avg
from EMPLOYEES
where DEPARTMENT_ID <>80
GROUP BY DEPARTMENT_ID
order by avg desc,mx,mn;
```
### 4.3a
```
Find number of employees in each salary group. Salary groups are considered as follows.
Group 1: 0k to <5K, 5k to <10k, 10k to <15k, and so on.
```
```sql
SELECT TRUNC(SALARY/5000 , 0)+1 salary_grp_no , TRUNC(SALARY/5000 , 0)*5000 lower_limit , TRUNC(SALARY/5000, 0)*5000+5000-1 upper_limit , COUNT(*)
FROM EMPLOYEES
GROUP BY TRUNC(SALARY/5000 , 0)
ORDER BY salary_grp_no;
```
### 4.3b
```
Find the number of employees that were hired in each year in each job type. Print year, job id,and total employees hired.
```
```sql
SELECT TO_CHAR(HIRE_DATE , 'YYYY') YEAR , JOB_ID , COUNT(*) Frequency
from EMPLOYEES
GROUP BY TO_CHAR(HIRE_DATE , 'YYYY'),JOB_ID
ORDER BY YEAR ASC ;
```

