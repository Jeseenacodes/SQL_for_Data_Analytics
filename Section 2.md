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

 <img width="383" height="91" alt="Day-2-expected_output" src="https://github.com/user-attachments/assets/e47bfe4e-c1af-406a-bc29-17c9e62ba265" />

### Step 1 
```
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

### Step 2
```
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



















