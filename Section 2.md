```
create table emp(emp_id int,emp_name varchar(10),salary int ,manager_id int);

insert into emp values(1,'Ankit',10000,4);
insert into emp values(2,'Mohit',15000,5);
insert into emp values(3,'Vikas',10000,4);
insert into emp values(4,'Rohit',5000,2);
insert into emp values(5,'Mudit',12000,6);
insert into emp values(6,'Agam',12000,2);
insert into emp values(7,'Sanjay',9000,2);
insert into emp values(8,'Ashish',5000,2);

select * from emp;

```
| emp_id | emp_name | salary | manager_id |
|--------|----------|--------|-------------|
| 1      | Ankit    | 10000  | 4           |
| 2      | Mohit    | 15000  | 5           |
| 3      | Vikas    | 10000  | 4           |
| 4      | Rohit    | 5000   | 2           |
| 5      | Mudit    | 12000  | 6           |
| 6      | Agam     | 12000  | 2           |
| 7      | Sanjay   | 9000   | 2           |
| 8      | Ashish   | 5000   | 2           |

Expected Output:

 <img width="383" height="91" alt="Day-2-expected_output" src="https://github.com/user-attachments/assets/e47bfe4e-c1af-406a-bc29-17c9e62ba265" />

## List employees along with their manager’s name.

### Step 1 
```sql
SELECT 
e.emp_id, 
e.emp_name, 
e.salary AS emp_salary, 
m.emp_name AS manager_name, 
m.salary AS manager_salary
FROM emp AS e
INNER JOIN emp AS m ON e.manager_id = m.emp_id 
ORDER BY emp_id
```

Output:

| **emp_id** | **emp_name** | **emp_salary** | **manager_name** | **manager_salary** |
| ---------- | ------------ | -------------- | ---------------- | ------------------ |
| 1          | Ankit        | 10,000         | Rohit            | 5,000              |
| 2          | Mohit        | 15,000         | Mudit            | 12,000             |
| 3          | Vikas        | 10,000         | Rohit            | 5,000              |
| 4          | Rohit        | 5,000          | Mohit            | 15,000             |
| 5          | Mudit        | 12,000         | Agam             | 12,000             |
| 6          | Agam         | 12,000         | Mohit            | 15,000             |
| 7          | Sanjay       | 9,000          | Mohit            | 15,000             |
| 8          | Ashish       | 5,000          | Mohit            | 15,000             |

## List employees with salary more than with their manager’s.

### Step 2
```sql
SELECT 
e.emp_id, 
e.emp_name, 
e.salary AS emp_salary, 
m.emp_name AS manager_name, 
m.salary AS manager_salary
FROM emp AS e
INNER JOIN emp AS m ON e.manager_id = m.emp_id 
WHERE e.salary > m.salary
ORDER BY emp_id ;
```

Output:

| **emp_id** | **emp_name** | **emp_salary** | **manager_name** | **manager_salary** |
| ---------- | ------------ | -------------- | ---------------- | ------------------ |
| 1          | Ankit        | 10,000         | Rohit            | 5,000              |
| 2          | Mohit        | 15,000         | Mudit            | 12,000             |
| 3          | Vikas        | 10,000         | Rohit            | 5,000              |

### Employees with same manager
```sql
SELECT 
    e1.emp_name AS employee_1,
    e2.emp_name AS employee_2,
    e1.manager_id
FROM emp e1
JOIN emp e2
    ON e1.manager_id = e2.manager_id
    AND e1.emp_id < e2.emp_id;
```
*This query identifies pairs of employees who report to the same manager. The condition e1.emp_id < e2.emp_id prevents duplicate pairs such as: (A, B) and (B, A), (A, A)*

Output:

| employee_1 | employee_2 | manager_id |
|------------|------------|------------|
| Ankit      | Vikas      | 4          |
| Rohit      | Agam       | 2          |
| Agam       | Sanjay     | 2          |
| Rohit      | Sanjay     | 2          |
| Sanjay     | Ashish     | 2          |
| Agam       | Ashish     | 2          |
| Rohit      | Ashish     | 2          |

```sql
# Group employees by managers, Not all SQL dialects support GROUP_CONCAT
SELECT manager_id, GROUP_CONCAT(emp_name)
FROM emp
GROUP BY manager_id;
```
| manager_id | group_concat(emp_name)   |
|------------|---------------------------|
| 2          | Rohit, Agam, Sanjay, Ashish |
| 4          | Ankit, Vikas              |
| 5          | Mohit                     |
| 6          | Mudit                     |


### Difference in salary between employees with consecutive employee_ids
```sql
SELECT 
    e1.emp_id AS emp_id_1,
    e1.emp_name AS employee_1,
    e1.salary AS salary_1,
    e2.emp_id AS emp_id_2,
    e2.emp_name AS employee_2,
    e2.salary AS salary_2,
    (e2.salary - e1.salary) AS salary_difference
FROM emp e1
JOIN emp e2 ON e2.emp_id = e1.emp_id + 1
ORDER BY e1.emp_id;
```

```sql
# Alternate using Lead function
SELECT emp_id,
       emp_name,
       salary,
       LEAD(salary) OVER (ORDER BY emp_id) AS next_salary,
       LEAD(salary) OVER (ORDER BY emp_id) - salary AS salary_difference
FROM emp;
```

Output:

| emp_id_1 | employee_1 | salary_1 | emp_id_2 | employee_2 | salary_2 | salary_difference |
|----------|------------|----------|----------|------------|----------|-------------------|
| 1        | Ankit      | 10000    | 2        | Mohit      | 15000    | 5000              |
| 2        | Mohit      | 15000    | 3        | Vikas      | 10000    | -5000             |
| 3        | Vikas      | 10000    | 4        | Rohit      | 5000     | -5000             |
| 4        | Rohit      | 5000     | 5        | Mudit      | 12000    | 7000              |
| 5        | Mudit      | 12000    | 6        | Agam       | 12000    | 0                 |
| 6        | Agam       | 12000    | 7        | Sanjay     | 9000     | -3000             |
| 7        | Sanjay     | 9000     | 8        | Ashish     | 5000     | -4000             |

### When do we use a self join?
1. To compare an employee to another employee
2. To compare a product to another product
3. To compare rows within the same table
4. To find hierarchical relationships (employees ↔ managers)









