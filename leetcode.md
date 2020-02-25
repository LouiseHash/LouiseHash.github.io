## Leetcode Database:

### 176. Second Highest Salary ***
select max(Salary) as SecondHighestSalary  from

   ( select Salary,
   
    Rank() over (order by Salary desc) rank
    
    from Employee
    
    ) se1
    
where se1.rank=2

### 177. Nth Highest Salary
<font color=#062F67>Write a SQL query to get the nth highest salary from the Employee table.</font>

+----+--------+

| Id | Salary |

+----+--------+

| 1  | 100    |

| 2  | 200    |

| 3  | 300    |

+----+--------+

For example, given the above Employee table, the nth highest salary where n = 2 is 200. If there is no nth highest salary, then the query should return null.

+------------------------+

| getNthHighestSalary(2) |

+------------------------+

| 200                    |

+------------------------+

#### My solution:
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT

 BEGIN
 
   RETURN (
   
      select 
      
      IF(N > (select count(*)
      
     from (select distinct Salary from Employee) se3),NULL,Salary) as result

      from (select distinct Salary
     
       FROM Employee 
       
       order by Salary DESC
       
       limit N
       
       )se1
       
       order by Salary
       
       limit 1
       
   );
   
 END

### 180. Consecutive Numbers ***
Write a SQL query to find all numbers that appear at least three times consecutively.

+----+-----+

| Id | Num |

+----+-----+

| 1  |  1  |

| 2  |  1  |

| 3  |  1  |

| 4  |  2  |

| 5  |  1  |

| 6  |  2  |

| 7  |  2  |

+----+-----+

For example, given the above Logs table, 1 is the only number that appears consecutively for at least three times.

+-----------------+

| ConsecutiveNums |

+-----------------+

| 1               |

+-----------------+

#### My solution:

Select distinct l1.Num ConsecutiveNums 

#### from Logs l1,Logs l2, Logs l3

where l2.Id=l1.Id+1 and l3.Id=l1.Id+2 and l2.Num=l1.Num and l3.Num=l1.Num

### 184. Department Highest Salary
The Employee table holds all employees. Every employee has an Id, a salary, and there is also a column for the department Id.

+----+-------+--------+--------------+

| Id | Name  | Salary | DepartmentId |

+----+-------+--------+--------------+

| 1  | Joe   | 70000  | 1            |

| 2  | Jim   | 90000  | 1            |

| 3  | Henry | 80000  | 2            |

| 4  | Sam   | 60000  | 2            |

| 5  | Max   | 90000  | 1            |

+----+-------+--------+--------------+

The Department table holds all departments of the company.

+----+----------+

| Id | Name     |

+----+----------+

| 1  | IT       |

| 2  | Sales    |

+----+----------+

Write a SQL query to find employees who have the highest salary in each of the departments. For the above tables, your SQL query should return the following rows (order of rows does not matter).

+------------+----------+--------+

| Department | Employee | Salary |

+------------+----------+--------+

| IT         | Max      | 90000  |

| IT         | Jim      | 90000  |

| Sales      | Henry    | 80000  |

+------------+----------+--------+

#### My solution:
select Department,Employee,Salary from

(select D.Name as Department, E.Name as Employee, Salary,

#### RANK () OVER ( 

 PARTITION BY DepartmentId 
 
 ORDER BY Salary DESC
 
 ) as salary_rank
 
from Employee E, Department D

where E.DepartmentId=D.Id) se1

where salary_rank=1
### 262. Trips and Users
The Trips table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the Users table. Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).

+----+-----------+-----------+---------+--------------------+----------+

| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|

+----+-----------+-----------+---------+--------------------+----------+

| 1  |     1     |    10     |    1    |     completed      |2013-10-01|

| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|

| 3  |     3     |    12     |    6    |     completed      |2013-10-01|

| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|

| 5  |     1     |    10     |    1    |     completed      |2013-10-02|

| 6  |     2     |    11     |    6    |     completed      |2013-10-02|

| 7  |     3     |    12     |    6    |     completed      |2013-10-02|

| 8  |     2     |    12     |    12   |     completed      |2013-10-03|

| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 

| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|

+----+-----------+-----------+---------+--------------------+----------+

The Users table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).

+----------+--------+--------+

| Users_Id | Banned |  Role  |

+----------+--------+--------+

|    1     |   No   | client |

|    2     |   Yes  | client |

|    3     |   No   | client |

|    4     |   No   | client |

|    10    |   No   | driver |

|    11    |   No   | driver |

|    12    |   No   | driver |

|    13    |   No   | driver |

+----------+--------+--------+

Write a SQL query to find the cancellation rate of requests made by unbanned users (both client and driver must be unbanned) between Oct 1, 2013 and Oct 3, 2013. The cancellation rate is computed by dividing the number of canceled (by client or driver) requests made by unbanned users by the total number of requests made by unbanned users.

For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to two decimal places.

+------------+-------------------+

|     Day    | Cancellation Rate |

+------------+-------------------+

| 2013-10-01 |       0.33        |

| 2013-10-02 |       0.00        |

| 2013-10-03 |       0.50        |

+------------+-------------------+

#### My solution:
select Request_at Day,

round(SUM(CASE WHEN [Status] != 'completed' THEN 1 ELSE 0 END)/cast(count(*) as float),2)   AS 'Cancellation Rate'

from Trips t

where t.Client_Id in (select Users_Id from Users where Banned='No' )

and t.Driver_Id in (select Users_Id from Users where Banned='No' )

and Request_at between '2013-10-01' and '2013-10-03'

group by Request_at


### 570. Managers with at Least 5 Direct Reports
The Employee table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.

+------+----------+-----------+----------+

|Id    |Name 	  |Department |ManagerId |

+------+----------+-----------+----------+

|101   |John 	  |A 	      |null      |

|102   |Dan 	  |A 	      |101       |

|103   |James 	  |A 	      |101       |

|104   |Amy 	  |A 	      |101       |

|105   |Anne 	  |A 	      |101       |

|106   |Ron 	  |B 	      |101       |

+------+----------+-----------+----------+

Given the Employee table, write a SQL query that finds out managers with at least 5 direct report. For the above table, your SQL query should return:

+-------+

| Name  |

+-------+

| John  |

+-------+

#### My solution:
select Name 
from Employee 
where Id in (select e2.ManagerId mid

from Employee e1,Employee e2

where e1.Id=e2.ManagerId

group by e2.ManagerId

#### having count(*)>4) 

### 578. Get Highest Answer Rate Question
Get the highest answer rate question from a table survey_log with these columns: id, action, question_id, answer_id, q_num, timestamp.

id means user id; action has these kind of values: "show", "answer", "skip"; answer_id is not null when action column is "answer", while is null for "show" and "skip"; q_num is the numeral order of the question in current session.

Write a sql query to identify the question which has the highest answer rate.

Example:

Input:
+------+-----------+--------------+------------+-----------+------------+

| id   | action    | question_id  | answer_id  | q_num     | timestamp  |

+------+-----------+--------------+------------+-----------+------------+

| 5    | show      | 285          | null       | 1         | 123        |

| 5    | answer    | 285          | 124124     | 1         | 124        |

| 5    | show      | 369          | null       | 2         | 125        |

| 5    | skip      | 369          | null       | 2         | 126        |

+------+-----------+--------------+------------+-----------+------------+

Output:

+-------------+

| survey_log  |

+-------------+

|    285      |

+-------------+

Explanation:
question 285 has answer rate 1/1, while question 369 has 0/1 answer rate, so output 285.

#### My solution:

select question_id as survey_log

from (select question_id,count(*) Total from survey_log

where action='answer'

group by question_id

order by Total DESC ) se1
      
limit 1

### 580. Count Student Number in Departments
A university uses 2 data tables, student and department, to store data about its students and the departments associated with each major.

Write a query to print the respective department name and number of students majoring in each department for all departments in the department table (even ones with no current students).

Sort your results by descending number of students; if two or more departments have the same number of students, then sort those departments alphabetically by department name.

The student is described as follow:

| Column Name  | Type      |

|--------------|-----------|

| student_id   | Integer   |

| student_name | String    |

| gender       | Character |

| dept_id      | Integer   |

where student_id is the student's ID number, student_name is the student's name, gender is their gender, and dept_id is the department ID associated with their declared major.

And the department table is described as below:

| Column Name | Type    |

|-------------|---------|

| dept_id     | Integer |

| dept_name   | String  |

where dept_id is the department's ID number and dept_name is the department name.

Here is an example input:
student table:

| student_id | student_name | gender | dept_id |

|------------|--------------|--------|---------|

| 1          | Jack         | M      | 1       |

| 2          | Jane         | F      | 1       |

| 3          | Mark         | M      | 2       |

department table:

| dept_id | dept_name   |

|---------|-------------|

| 1       | Engineering |

| 2       | Science     |

| 3       | Law         |

The Output should be:

| dept_name   | student_number |

|-------------|----------------|

| Engineering | 2              |

| Science     | 1              |

| Law         | 0              |

#### My solution:
select dept_name, 
#### IFNULL(total, 0) student_number 
#### from department d left join  (select s.dept_id did, count(*) total
from student s  join department d
on s.dept_id=d.dept_id
group by s.dept_id
order by total desc
 )se1 
on d.dept_id =did
order by student_number desc,dept_name


### 602. Friend Requests II: Who Has the Most Friends
In social network like Facebook or Twitter, people send friend requests and accept others' requests as well.

 

Table request_accepted

+--------------+-------------+------------+

| requester_id | accepter_id | accept_date|

|--------------|-------------|------------|

| 1            | 2           | 2016_06-03 |

| 1            | 3           | 2016-06-08 |

| 2            | 3           | 2016-06-08 |

| 3            | 4           | 2016-06-09 |

+--------------+-------------+------------+

This table holds the data of friend acceptance, while requester_id and accepter_id both are the id of a person.
 

Write a query to find the the people who has most friends and the most friends number under the following rules:

It is guaranteed there is only 1 people having the most friends.
The friend request could only been accepted once, which mean there is no multiple records with the same requester_id and accepter_id value.
For the sample data above, the result is:

Result table:

+------+------+

| id   | num  |

|------|------|

| 3    | 3    |

+------+------+

The person with id '3' is a friend of people '1', '2' and '4', so he has 3 friends in total, which is the most number than any others.

#### My solution:
select requester_id id, sum(Total1) num

from

(

select requester_id,Total1

from (select requester_id , count(*) Total1

from request_accepted

group by requester_id

order by Total1 desc

) se1

union all

select accepter_id,Total2

from (select accepter_id , count(*) Total2

from request_accepted

group by accepter_id 

order by Total2 desc

) se2

) se3

group by id

order by num desc

limit 1


### 608. Tree Node

Given a table tree, id is identifier of the tree node and p_id is its parent node's id.

+----+------+

| id | p_id |

+----+------+

| 1  | null |

| 2  | 1    |

| 3  | 1    |

| 4  | 2    |

| 5  | 2    |

+----+------+

Each node in the tree can be one of three types:
Leaf: if the node is a leaf node.
Root: if the node is the root of the tree.
Inner: If the node is neither a leaf node nor a root node.
 

Write a query to print the node id and the type of the node. Sort your output by the node id. The result for the above sample is:
 

+----+------+

| id | Type |

+----+------+

| 1  | Root |

| 2  | Inner|

| 3  | Leaf |

| 4  | Leaf |

| 5  | Leaf |

+----+------+

#### My solution:
select id, 'Root' as Type

from (

select id,p_id 

from tree

where p_id is null

) se1

union

select id, 'Inner' as Type

from (select id

from tree

where id in (select p_id from tree) and p_id is not null

)se2

union 

select id, 'Leaf' as Type

from (select id

from tree

where id not in (select p_id from tree where p_id is not null) and p_id is not null

)se3

### 1127. User Purchase Platform
Table: Spending

+-------------+---------+

| Column Name | Type    |

+-------------+---------+

| user_id     | int     |

| spend_date  | date    |

| platform    | enum    | 

| amount      | int     |

+-------------+---------+

The table logs the spendings history of users that make purchases from an online shopping website which has a desktop and a mobile application.
(user_id, spend_date, platform) is the primary key of this table.
The platform column is an ENUM type of ('desktop', 'mobile').
Write an SQL query to find the total number of users and the total amount spent using mobile only, desktop only and both mobile and desktop together for each date.

The query result format is in the following example:

Spending table:
+---------+------------+----------+--------+

| user_id | spend_date | platform | amount |

+---------+------------+----------+--------+

| 1       | 2019-07-01 | mobile   | 100    |

| 1       | 2019-07-01 | desktop  | 100    |

| 2       | 2019-07-01 | mobile   | 100    |

| 2       | 2019-07-02 | mobile   | 100    |

| 3       | 2019-07-01 | desktop  | 100    |

| 3       | 2019-07-02 | desktop  | 100    |

+---------+------------+----------+--------+

Result table:
+------------+----------+--------------+-------------+

| spend_date | platform | total_amount | total_users |

+------------+----------+--------------+-------------+

| 2019-07-01 | desktop  | 100          | 1           |

| 2019-07-01 | mobile   | 100          | 1           |

| 2019-07-01 | both     | 200          | 1           |

| 2019-07-02 | desktop  | 100          | 1           |

| 2019-07-02 | mobile   | 100          | 1           |

| 2019-07-02 | both     | 0            | 0           |

+------------+----------+--------------+-------------+ 

On 2019-07-01, user 1 purchased using both desktop and mobile, user 2 purchased using mobile only and user 3 purchased using desktop only.
On 2019-07-02, user 2 purchased using mobile only, user 3 purchased using desktop only and no one purchased using both platforms.

### My solution:
(need to create a table first according to the result table's no-null columns)

select 

    ta.spend_date,
    
    ta.platform,
    
    ifnull(tb.total_amount, 0) AS total_amount,
    
    ifnull(tb.total_users, 0) AS total_users
    
from (

    select DISTINCT(spend_date), a.platform
    
    from Spending join
    
        (   select 'desktop' AS platform UNION
        
            select 'mobile' AS platform UNION
            
            select 'both' AS platform
            
        ) AS a 
        
) AS ta

LEFT JOIN

(select spend_date,platform,sum(a) total_amount, count(*) total_users

from 

(select user_id,spend_date,count(*) num,amount a,platform

from Spending 

group by user_id,spend_date

having num=1)se4

group by spend_date,platform

union

select spend_date,'both' as platform,sum(a) total_amount, count(*) total_users 

from 

(select  user_id,spend_date,count(*) num,sum(amount) a

from Spending

group by user_id,spend_date

having num=2)se3

group by spend_date

)as tb

ON ta.platform = tb.platform

AND ta.spend_date = tb.spend_date
