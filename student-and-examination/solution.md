# 1280. Students and Examinations

## Question

```
Table: Students
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| student_name  | varchar |
+---------------+---------+
student_id is the primary key (column with unique values) for this table.
Each row of this table contains the ID and the name of one student in the school.
```
```
Table: Subjects
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| subject_name | varchar |
+--------------+---------+
subject_name is the primary key (column with unique values) for this table.
Each row of this table contains the name of one subject in the school.
```
```
Table: Examinations
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| student_id   | int     |
| subject_name | varchar |
+--------------+---------+
There is no primary key (column with unique values) for this table. It may contain duplicates.
Each student from the Students table takes every course from the Subjects table.
Each row of this table indicates that a student with ID student_id attended the exam of subject_name.
```

Write a solution to find the number of times each student attended each exam.

Return the result table ordered by `student_id` and `subject_name`.

The result format is in the following example.
 

Example 1:

Input: 
```
Students table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |
+------------+--------------+
Subjects table:
+--------------+
| subject_name |
+--------------+
| Math         |
| Physics      |
| Programming  |
+--------------+
Examinations table:
+------------+--------------+
| student_id | subject_name |
+------------+--------------+
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |
+------------+--------------+
```
Output: 
```
+------------+--------------+--------------+----------------+
| student_id | student_name | subject_name | attended_exams |
+------------+--------------+--------------+----------------+
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |
+------------+--------------+--------------+----------------+
```
Explanation:\
The result table should contain all students and all subjects.
- Alice attended the Math exam 3 times, the Physics exam 2 times, and the Programming exam 1 time.
- Bob attended the Math exam 1 time, the Programming exam 1 time, and did not attend the Physics exam.
- Alex did not attend any exams.
- John attended the Math exam 1 time, the Physics exam 1 time, and the Programming exam 1 time.

## Solution
First we need to show student_id, student_name, and subject_name, notice that in the output we need to show all combination of subject_name and student_name, so the main idea is to use `CROSS JOIN`.

```sql
# Write your MySQL query statement below
SELECT 
    *
FROM 
    Students s
    CROSS JOIN Subjects sub;
```
Output:
```
| student_id | student_name | subject_name |
| ---------- | ------------ | ------------ |
| 1          | Alice        | Programming  |
| 1          | Alice        | Physics      |
| 1          | Alice        | Math         |
| 2          | Bob          | Programming  |
| 2          | Bob          | Physics      |
| 2          | Bob          | Math         |
| 13         | John         | Programming  |
| 13         | John         | Physics      |
| 13         | John         | Math         |
| 6          | Alex         | Programming  |
| 6          | Alex         | Physics      |
| 6          | Alex         | Math         |
```
Now we want to count how many time each student attended an exam per subject_name, we have the data from `Examinations` table, we can do `LEFT JOIN` the `Examinations` table on `student_id` and `subject_name`, remember that we use `student_id` from `Students` table and `subject_name` from `Subjects` table.

```sql
# Write your MySQL query statement below
SELECT 
    *
FROM 
    Students s
    CROSS JOIN Subjects sub
    LEFT JOIN Examinations e ON s.student_id = e.student_id AND 
    sub.subject_name = e.subject_name;
```
Output:
```
| student_id | student_name | subject_name | student_id | subject_name |
| ---------- | ------------ | ------------ | ---------- | ------------ |
| 1          | Alice        | Programming  | 1          | Programming  |
| 1          | Alice        | Physics      | 1          | Physics      |
| 1          | Alice        | Physics      | 1          | Physics      |
| 1          | Alice        | Math         | 1          | Math         |
| 1          | Alice        | Math         | 1          | Math         |
| 1          | Alice        | Math         | 1          | Math         |
| 2          | Bob          | Programming  | 2          | Programming  |
| 2          | Bob          | Physics      | null       | null         |
| 2          | Bob          | Math         | 2          | Math         |
| 13         | John         | Programming  | 13         | Programming  |
| 13         | John         | Physics      | 13         | Physics      |
| 13         | John         | Math         | 13         | Math  ...

The '...' means that there are more data but Leetcode doesn't show them.
```
We get the output from both table (the `CROSS JOIN` between `Students` and `Subjects` table from the previous step and `Examinations` table), the first three fields are from the `CROSS JOIN` and the last two fields are from `Examinations` table.

Now for the `attended_exams` fields, we can count how many time `student_id` showed up, notice that there is `NULL` value for Bob programming exam because Bob never attended one, so because of this reason we can use `student_id` from `Examinations` table to count `attended_exams`, we group them by `student_id` and `student_name` from `Students` table and `subject_name` from `Subjects` table.

```sql
# Write your MySQL query statement below
SELECT 
    s.student_id, 
    s.student_name, 
    sub.subject_name,
    COUNT(e.student_id) as attended_exams
FROM 
    Students s
    CROSS JOIN Subjects sub
    LEFT JOIN Examinations e ON s.student_id = e.student_id AND 
    sub.subject_name = e.subject_name
GROUP BY s.student_id, s.student_name, sub.subject_name;
```
Output:
```
| student_id | student_name | subject_name | attended_exams |
| ---------- | ------------ | ------------ | -------------- |
| 1          | Alice        | Programming  | 1              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Math         | 3              |
| 2          | Bob          | Programming  | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Math         | 1              |
| 13         | John         | Programming  | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Math         | 1              |
| 6          | Alex         | Programming  | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Math         | 0              |
```
And then for the last step, we order them by `student_id` and `subject_name`.


```sql
# Write your MySQL query statement below
SELECT 
    s.student_id, 
    s.student_name, 
    sub.subject_name,
    COUNT(e.student_id) as attended_exams
FROM 
    Students s
    CROSS JOIN Subjects sub
    LEFT JOIN Examinations e ON s.student_id = e.student_id AND 
    sub.subject_name = e.subject_name
GROUP BY s.student_id, s.student_name, sub.subject_name
ORDER BY s.student_id, sub.subject_name;
```
Output:
```
| student_id | student_name | subject_name | attended_exams |
| ---------- | ------------ | ------------ | -------------- |
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |
```