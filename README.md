A brief Introduction to Oracle-SQL-PL SQL,taught in our CSE216 Database Sessional course, is a SQL practice book written by [Prof. Sukarna Barua](https://cse.buet.ac.bd/faculty/facdetail.php?id=sukarnabarua).The book contains most of the fundamental sql queries using join,subquery,group by,set operation,pl-sql etc.There are 11 chapters in total and several exercises follows.All the exercises are based on HR schema of `ORACLE`.

First , Let's look at the overview of HR schema

![hr_schema](https://static.webucator.com/materials/manuals/courseware-oracle/hr-ed-with-labels.png)

In this repository,You won't find solutions to all the exercises but you will find what you need.I have left out those ones which are too easy or similar to the ones I listed here or is easily doable by looking at the examples of this book.

## Chapter 3: Use of Oracle Single Row Functions
### 3.3b
```
Suppose you need to find the number of days each employee worked during the first 
month of his joining. Write an SQL query to find this information for all employees
```
```sql
SELECT 30+MOD(EXTRACT(MONTH FROM HIRE_DATE),2) 
	- 
EXTRACT(DAY FROM HIRE_DATE) as day_worked_join_month from EMPLOYEES;
```
### 3.5
```
Print hire dates of all employees in the following formats:
(i) 13th February, 1998 (ii) 13 February, 1998.
```
```sql
SELECT TO_CHAR(HIRE_DATE,'DD MON,YYYY'), CONCAT(CONCAT(SUBSTR(HIRE_DATE,1, 2), 'th'), 
SUBSTR(TO_CHAR(HIRE_DATE,'DD MON,YYYY'),3,9)) FROM EMPLOYEES;
```
## Chapter 4: Aggregate Functions
### 4.1c
```
Find the minimum, maximum, and average salary of all departments except DEPARTMENT_ID 80. 
Print DEPARTMENT_ID, minimum, maximum, and average salary.Sort the results in descending order
of average salary first, then maximum salary, then minimum salary. Use column alias to rename
column names in output for better display.
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
SELECT TRUNC(SALARY/5000 , 0)+1 salary_grp_no , TRUNC(SALARY/5000 , 0)*5000 lower_limit ,
TRUNC(SALARY/5000, 0)*5000+5000-1 upper_limit , COUNT(*)
FROM EMPLOYEES
GROUP BY TRUNC(SALARY/5000 , 0)
ORDER BY salary_grp_no;
```
### 4.3b
```
Find the number of employees that were hired in each year in each job type. Print year, job id,
and total employees hired.
```
```sql
SELECT TO_CHAR(HIRE_DATE , 'YYYY') YEAR , JOB_ID , COUNT(*) Frequency
from EMPLOYEES
GROUP BY TO_CHAR(HIRE_DATE , 'YYYY'),JOB_ID
ORDER BY YEAR ASC ;
```
## Chapter 5: Query Multiple Tables – Joins
### 5.1
### a
```
For each employee print last name, salary, and job title
```
```sql
SELECT E.LAST_NAME , E.SALARY , J.JOB_TITLE
from EMPLOYEES E LEFT JOIN JOBS J
ON(E.JOB_ID = J.JOB_ID)
ORDER BY E.LAST_NAME;
```
### b
```
For each department, print department name and country name it is situated in
```
```sql
SELECT D.DEPARTMENT_NAME , C.COUNTRY_NAME
FROM DEPARTMENTS D LEFT JOIN LOCATIONS L
ON(D.LOCATION_ID = L.LOCATION_ID)
LEFT JOIN COUNTRIES C
ON(L.COUNTRY_ID = C.COUNTRY_ID)
ORDER BY D.DEPARTMENT_NAME;
```
### c
```
For each country, finds total number of departments situated in the country
```
```sql
SELECT C.COUNTRY_NAME , COUNT(D.DEPARTMENT_NAME)
from COUNTRIES C LEFT JOIN LOCATIONS L
ON(C.COUNTRY_ID = L.COUNTRY_ID)
LEFT JOIN DEPARTMENTS D
ON(D.LOCATION_ID = L.LOCATION_ID)
GROUP BY C.COUNTRY_NAME
ORDER BY COUNTRY_NAME;
```
### d
```
For each employee, finds the number of job switches of the employee.
```
```sql
SELECT e.EMPLOYEE_ID , COUNT(jh.JOB_ID)
from EMPLOYEES e
JOIN JOB_HISTORY jh
ON( e.EMPLOYEE_ID = jh.EMPLOYEE_ID )
GROUP BY e.EMPLOYEE_ID
ORDER BY e.EMPLOYEE_ID;
```
### e
```
For each department and job types, find the total number of employees working. Print 
department names, job titles, and total employees working.
```
```sql
SELECT D.DEPARTMENT_NAME , J.JOB_TITLE , COUNT(EMPLOYEE_ID)
from EMPLOYEES E LEFT JOIN JOBS J
ON(E.JOB_ID = J.JOB_ID)
JOIN DEPARTMENTS D
ON(E.DEPARTMENT_ID = D.DEPARTMENT_ID)
GROUP BY D.DEPARTMENT_NAME,J.JOB_TITLE
ORDER BY DEPARTMENT_NAME;
```
### f
```
For each employee, finds the total number of employees those were hired before him/her.
Print employee last name and total employees.
```
```sql
SELECT E1.LAST_NAME , COUNT(E2.EMPLOYEE_ID) Hired_Before
FROM EMPLOYEES E1 JOIN EMPLOYEES E2
ON(E1.HIRE_DATE>E2.HIRE_DATE)
GROUP BY E1.EMPLOYEE_ID,E1.LAST_NAME
ORDER BY E1.LAST_NAME;
```
### g
```
For each employee, finds the total number of employees those were hired before him/her and
those were hired after him/her. Print employee last name, total employees hired before him,
and total employees hired after him
```
```sql
SELECT t1.last_name , t1.hired_before , t2.hired_after
from(
	SELECT E1.EMPLOYEE_ID id, E1.LAST_NAME as last_name , COUNT(E2.EMPLOYEE_ID) hired_before
	FROM EMPLOYEES E1 JOIN EMPLOYEES E2
	ON(E1.HIRE_DATE>E2.HIRE_DATE)
	GROUP BY E1.EMPLOYEE_ID,E1.LAST_NAME
) t1
JOIN (
	SELECT E1.EMPLOYEE_ID id, E1.LAST_NAME as last_name,COUNT(E3.EMPLOYEE_ID) hired_after
	FROM EMPLOYEES E1 JOIN EMPLOYEES E3
	ON(E1.HIRE_DATE<E3.HIRE_DATE)
	GROUP BY E1.EMPLOYEE_ID,E1.LAST_NAME
) t2
ON(t1.id=t2.id)
ORDER BY t1.last_name
;
```
### h
```
Find the employees having salaries greater than at least three other employees
```
```sql
SELECT e1.LAST_NAME , e1.SALARY FROM EMPLOYEES e1 
WHERE (
	3 <= (SELECT COUNT(e2.SALARY) FROM EMPLOYEES e2 WHERE e1.SALARY>e2.SALARY)
)
ORDER BY e1.SALARY;
```
### i
```
For each employee, find his rank, i.e., position with respect to salary. The highest 
salaried employee should get rank 1 and lowest salaried employee should get the last 
rank. Employees with same salary should get same rank value. Print employee last names
and his/he rank.
```
```sql
SELECT e1.LAST_NAME , COUNT(DISTINCT e2.SALARY)+1 as Rank
FROM EMPLOYEES e1 left join EMPLOYEES e2
on (e1.SALARY < e2.SALARY)
GROUP BY e1.EMPLOYEE_ID , e1.LAST_NAME
ORDER BY Rank asc;
```
### j
```
Find the names of employees and their salaries for the top three highest salaried employees.
The number of employees in your output should be more than three if there are employees with
same salary.
```
```sql
SELECT e1.LAST_NAME , e1.SALARY
FROM EMPLOYEES e1 
WHERE((SELECT COUNT(e2.EMPLOYEE_ID) FROM EMPLOYEES e2 WHERE e2.SALARY>e1.SALARY) <= 3) 
AND e1.SALARY is not null
ORDER BY e1.SALARY desc;
```
## Chapter 6: Query Multiple Tables – Sub-query
### 6.1
## a
```
Find the last names of all employees that work in the SALES department.
```
```sql
SELECT LAST_NAME FROM EMPLOYEES
WHERE DEPARTMENT_ID = (
		SELECT DEPARTMENT_ID from DEPARTMENTS WHERE DEPARTMENT_NAME='Sales'
);
```
### b
```Find the last names and salaries of those employees who get higher salary than at 
least one employee of SALES department

```
```sql
SELECT LAST_NAME, SALARY
FROM EMPLOYEES
WHERE SALARY > ANY
(
SELECT SALARY
FROM EMPLOYEES
WHERE DEPARTMENT_ID = (
		SELECT DEPARTMENT_ID from DEPARTMENTS WHERE DEPARTMENT_NAME='Sales'
) );
```
### c
```
Find the last names and salaries of those employees whose salary is higher than all 
employees of SALES department.
```
```sql
SELECT LAST_NAME, SALARY
FROM EMPLOYEES
WHERE SALARY > ALL
(
SELECT SALARY
FROM EMPLOYEES
WHERE DEPARTMENT_ID = (
		SELECT DEPARTMENT_ID from DEPARTMENTS WHERE DEPARTMENT_NAME='Sales'
) );
```
### d
```
Find the last names and salaries of those employees whose salary is within ± 5k of 
the average salary of SALES department.
```
```sql
SELECT LAST_NAME,SALARY
FROM EMPLOYEES
WHERE SALARY
<=(SELECT 5000 + round(avg(SALARY))
FROM EMPLOYEES
WHERE DEPARTMENT_ID = (
		SELECT DEPARTMENT_ID from DEPARTMENTS WHERE DEPARTMENT_NAME='Sales'
	))
AND
SALARY>=(SELECT  -5000 + round(avg(SALARY))
FROM EMPLOYEES
WHERE DEPARTMENT_ID = (
		SELECT DEPARTMENT_ID from DEPARTMENTS WHERE DEPARTMENT_NAME='Sales'
	) )
;
```
### 6.2
### a
```
Find those employees whose salary is higher than at least three other employees. Print
last names and salary of each employee. You cannot use join in the main query. Use sub-query
in WHERE clause only. You can use join in the sub-queries.
```
```sql
SELECT e1.LAST_NAME,e1.SALARY
FROM EMPLOYEES e1
WHERE
3<=(
	SELECT COUNT(e2.EMPLOYEE_ID)
	FROM EMPLOYEES e2
	WHERE (e2.SALARY<e1.SALARY)
) ORDER BY e1.LAST_NAME;
```
### b
```
Find those departments whose average salary is greater than the minimum salary of all other 
departments. Print department names. Use sub-query. You can use join in the sub-queries.
```
```sql
SELECT d.DEPARTMENT_ID,d.DEPARTMENT_NAME
FROM DEPARTMENTS d 
WHERE 
(SELECT avg(e.SALARY) FROM EMPLOYEES e WHERE e.DEPARTMENT_ID = d.DEPARTMENT_ID)
>
(SELECT MAX(minsal) FROM (SELECT min(SALARY) minsal FROM EMPLOYEES GROUP BY DEPARTMENT_ID));
```
### c
```
Find those department names which have the highest number of employees in service. Print 
department names. Use sub-query. You can use join in the sub-queries.
```
```sql
SELECT d.DEPARTMENT_NAME,e.No_Of_Employee
FROM DEPARTMENTS d,(SELECT DEPARTMENT_ID, COUNT(EMPLOYEE_ID ) No_Of_Employee 
FROM EMPLOYEES GROUP BY DEPARTMENT_ID) e
WHERE d.DEPARTMENT_ID = e.DEPARTMENT_ID
ORDER BY e.No_Of_Employee desc;
```
### d
```
Find those employees who worked in more than one department in the company. Print employee 
last names. You cannot use join in the main query. Use sub-query. You can use join
in the sub-queries
```
```sql
SELECT e.LAST_NAME ,e.EMPLOYEE_ID
FROM EMPLOYEES e
WHERE e.DEPARTMENT_ID <> ( SELECT  DISTINCT j.DEPARTMENT_ID FROM JOB_HISTORY j WHERE 
e.EMPLOYEE_ID = j.EMPLOYEE_ID );
```
### e
```
For each employee, find the minimum and maximum salary of his/her department. Print 
employee last name, minimum salary, and maximum salary. Do not use sub-query in WHERE
clause. Use sub-query in FROM clause.
```
```sql
SELECT E.LAST_NAME, D.MINSAL, D.MAXSAL
FROM EMPLOYEES E,
(
SELECT DEPARTMENT_ID AS DEPT, MIN(SALARY) MINSAL, MAX(SALARY) MAXSAL
FROM EMPLOYEES
GROUP BY DEPARTMENT_ID
) D
WHERE (E.DEPARTMENT_ID = D.DEPT)
;
```
### f
```
For each job type, find the employee who gets the highest salary. Print job title and 
last name of the employee. Assume that there is one and only one such employee for every
job type.
```
```sql
SELECT ln , jt
FROM
(
	SELECT	
	ROW_NUMBER() OVER(
		PARTITION BY j.JOB_TITLE 
		ORDER BY e.SALARY desc
	) row_num,
	e.LAST_NAME ln, j.JOB_TITLE  jt
	FROM EMPLOYEES e JOIN JOBS j ON( e.JOB_ID = j.JOB_ID )
) t
WHERE row_num=1;
```
## Chapter 7: Set operations
### 7.1
### a
```
Find EMPLOYEE_ID of those employees who are not managers. Use minus operator to perform
this.
```
```sql
SELECT EMPLOYEE_ID FROM EMPLOYEES
MINUS
(SELECT MANAGER_ID FROM EMPLOYEES);
```
### b
```
Find last names of those employees who are not managers. Use minus operator to perform this.
```
```sql
SELECT LAST_NAME FROM EMPLOYEES WHERE EMPLOYEE_ID
IN
(
	SELECT EMPLOYEE_ID FROM EMPLOYEES
	MINUS
	(SELECT MANAGER_ID FROM EMPLOYEES)
)
```
### c
```
Find the LOCATION_ID of those locations having no departments.
```
```sql
SELECT LOCATION_ID FROM LOCATIONS
MINUS
(SELECT LOCATION_ID FROM DEPARTMENTS);
```
## Chapter 8: Data Manipulation Language (DML)
### 8.2
### b
```
Update salary of all employees to the maximum salary of the department in which he/she works.
```
```sql
UPDATE EMPLOYEES e
SET e.SALARY = (SELECT max(SALARY) FROM EMPLOYEES x WHERE x.DEPARTMENT_ID=e.DEPARTMENT_ID);
```
### c
```
Update COMMISSION_PCT to N times for each employee where N is the number of employees he/she 
manages. When N = 0, keep the old value of COMMISSION_PCT column.
```
```sql
UPDATE EMPLOYEES e
SET e.COMMISSION_PCT = e.COMMISSION_PCT*(SELECT COUNT(MANAGER_ID)
FROM EMPLOYEES x WHERE e.DEPARTMENT_ID=x.MANAGER_ID)
WHERE (SELECT COUNT(MANAGER_ID) 
FROM EMPLOYEES x WHERE e.DEPARTMENT_ID=x.MANAGER_ID)>0;
```
### d
```
Update the hiring dates of all employees to the first day of the same year.
Do not change this for those employees who joined on or after year 2000.
```
```sql
UPDATE EMPLOYEES e
SET HIRE_DATE = (TO_DATE(concat('01/01/' , (EXTRACT(YEAR FROM HIRE_DATE ))), 'DD/MM/YYYY'))
WHERE EXTRACT(YEAR from HIRE_DATE ) <2000;
```
### 8.3
### b
```
Delete those locations having no departments.
```
```sql
DELETE FROM LOCATIONS
WHERE LOCATION_ID NOT IN(SELECT LOCATION_ID FROM DEPARTMENTS);
```
## Chapter 11: Introduction to PL/SQL
### 11.1 a
```
Write a PL/SQL block that will print ‘Happy Anniversary X’ for each employee X whose
hiring date is today. Use cursor FOR loop for the task.
```
```sql
DECLARE
YEARS NUMBER ;
hd_m NUMBER;
hd_d NUMBER;
cd NUMBER;
cm NUMBER;
BEGIN

FOR R IN (SELECT HIRE_DATE,LAST_NAME FROM EMPLOYEES )
LOOP
hd_m :=extract(MONTH from R.HIRE_DATE);
hd_d := extract(DAY from R.HIRE_DATE);
cd := EXTRACT(DAY from SYSDATE);
cm := EXTRACT(MONTH from SYSDATE);

IF cd=hd_d AND hd_m=cm THEN
	DBMS_OUTPUT.PUT_LINE('Happy Anniversary' || R.LAST_NAME ) ;	
END IF;
END LOOP ;
END ;
/
```
### 11.2 b
```
Write an example PL/SQL block that inserts a new arbitrary row to the COUNTRIES table.
The block should handle the exception DUP_VAL_ON_INDEX and OTHERS. Run the
block for different COUNTRY_ID and observe the cases when above exception occurs.
```
```sql
DECLARE
BEGIN
INSERT INTO COUNTRIES
VALUES('BD' , 'Bangladesh' , 4);

EXCEPTION
WHEN DUP_VAL_ON_INDEX THEN
DBMS_OUTPUT.PUT_LINE('Duplicate value found!!') ;
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE('I dont know what happened!') ;
END;
/
```

You are invited to correct me in case I made a mistake and are welcomed to add those few missing exercises if you want.






























