1. SELECT name, surname, scholarship 
   FROM students 
   WHERE scholarship = (SELECT MAX(scholarship) FROM students) 
   ORDER BY surname ASC;
2. SELECT name, surname, scholarship 
   FROM students 
   WHERE scholarship > (SELECT AVG(scholarship) FROM students);
3. SELECT name, surname, university_id, course 
   FROM students 
   WHERE city = 'Воронеж' 
   ORDER BY university_id ASC, course ASC;
4. SELECT subject 
   FROM curriculum 
   GROUP BY subject 
   HAVING SUM(hours) = (SELECT MAX(total_hours) FROM (SELECT SUM(hours) AS total_hours FROM curriculum GROUP BY subject) AS hours_table);
5. SELECT name, surname 
   FROM students 
   WHERE city <> (SELECT city FROM universities WHERE universities.university_id = students.university_id);
6. SELECT name, rating 
   FROM universities 
   WHERE city = 'Москва' AND rating < (SELECT rating FROM universities WHERE name = 'ВГУ');



   ===========================================================================================



   1. SELECT name, surname 
   FROM students 
   WHERE city = (SELECT city FROM universities WHERE universities.university_id = students.university_id);
2. SELECT name, surname, university_id, course 
   FROM students 
   WHERE city <> (SELECT city FROM universities WHERE universities.university_id = students.university_id) 
   ORDER BY university_id ASC, course ASC;
3. SELECT surname, name, university_id, city 
   FROM teachers 
   WHERE city <> (SELECT city FROM universities WHERE universities.university_id = teachers.university_id) 
   ORDER BY university_id ASC, city ASC;
4. SELECT subject, semester 
   FROM curriculum 
   GROUP BY semester 
   HAVING SUM(hours) = (SELECT MAX(total_hours) FROM (SELECT SUM(hours) AS total_hours FROM curriculum GROUP BY semester) AS hours_table) 
   ORDER BY semester ASC;
5. SELECT name, surname 
   FROM students 
   WHERE scholarship > (SELECT AVG(scholarship) FROM students WHERE course = students.course);
6. SELECT name, scholarship, university_id 
   FROM students 
   WHERE scholarship = (SELECT MIN(scholarship) FROM students WHERE university_id = students.university_id) 
   ORDER BY university_id ASC, scholarship ASC;
7. SELECT name, rating 
   FROM universities 
   WHERE (SELECT COUNT(*) FROM students WHERE students.university_id = universities.university_id) > 50 
   ORDER BY rating DESC;
8. SELECT name, rating 
   FROM universities 
   WHERE (SELECT COUNT(*) FROM teachers WHERE teachers.university_id = universities.university_id) > 5 
   ORDER BY rating DESC;
9. SELECT name, surname, university_id, course 
   FROM students 
   WHERE NOT EXISTS (SELECT * FROM grades WHERE grades.student_id = students.student_id AND grade <> 'Отлично') 
   ORDER BY university_id ASC, course ASC;
10. SELECT name, surname, university_id, course 
    FROM students 
    WHERE EXISTS (SELECT * FROM grades WHERE grades.student_id = students.student_id AND grade = 'Неудовлетворительно') 
    ORDER BY university_id ASC, course ASC;
11. SELECT name, surname 
    FROM students 
    GROUP BY name, surname 
    HAVING AVG((SELECT value FROM grades WHERE grades.student_id = students.student_id)) > 4;
12. SELECT surname 
    FROM students 
    WHERE NOT EXISTS (SELECT * FROM grades WHERE grades.student_id = students.student_id AND grade <> 'Отлично') AND city <> (SELECT city FROM universities WHERE universities.university_id = students.university_id);




    ======================================================================================================================



    1. SELECT value 
   FROM grades 
   WHERE student_id = (SELECT student_id FROM students WHERE surname = 'Иванов');
   Этот запрос будет корректным только в том случае, если в таблице students нет двух студентов с фамилией Иванов, иначе запрос вернет ошибку.
2. SELECT name 
   FROM students 
   WHERE subject_id = 101 AND value > (SELECT AVG(value) FROM grades WHERE subject_id = 101) + 1;
3. SELECT name 
   FROM students 
   WHERE subject_id = 102 AND value < (SELECT AVG(value) FROM grades WHERE subject_id = 102) - 1;
4. SELECT COUNT(subject_id), student_id 
   FROM grades 
   GROUP BY student_id 
   HAVING COUNT(subject_id) > 20;
5. SELECT name, student_id 
   FROM students 
   WHERE scholarship = (SELECT MAX(scholarship) FROM students WHERE city = (SELECT city FROM students WHERE student_id = students.student_id));
6. SELECT name, student_id 
   FROM students 
   WHERE city NOT IN (SELECT city FROM universities);
7. С использованием связанного подзапроса:
   SELECT name, student_id 
   FROM students 
   WHERE city <> (SELECT city FROM universities WHERE universities.university_id = students.university_id);
   С использованием соединения:
   SELECT name, student_id 
   FROM students JOIN universities ON students.city <> universities.city AND students.university_id = universities.university_id;




   ==========================================================================




   1. SELECT * 
   FROM students 
   WHERE EXISTS (SELECT university_id FROM universities WHERE rating > 300 AND universities.university_id = students.university_id);
2. SELECT * 
   FROM students JOIN universities ON students.university_id = universities.university_id AND universities.rating > 300;
3. SELECT * 
   FROM students 
   WHERE EXISTS (SELECT university_id FROM universities WHERE universities.city = students.city AND universities.university_id NOT IN (SELECT university_id FROM students WHERE student_id = students.student_id));
4. SELECT name 
   FROM subject 
   WHERE subject_id IN (SELECT subject_id FROM grades GROUP BY subject_id HAVING COUNT(DISTINCT student_id) > 1);
5. SELECT DISTINCT city 
   FROM students JOIN universities ON students.university_id = universities.university_id;
6. SELECT DISTINCT city 
   FROM students LEFT JOIN universities ON students.university_id = universities.university_id WHERE universities.city IS NULL;
7. SELECT name 
   FROM subject 
   WHERE subject_id NOT IN (SELECT subject_id FROM teaches);
8. SELECT DISTINCT name 
   FROM subject 
   WHERE semester = 1;
9. SELECT DISTINCT name 
   FROM subject 
   WHERE subject_id IN (SELECT subject_id FROM subject GROUP BY subject_id HAVING COUNT(DISTINCT semester) > 1);
10. SELECT name 
    FROM universities 
    WHERE university_id NOT IN (SELECT university_id FROM teaches);
11. SELECT name 
    FROM universities 
    WHERE university_id NOT IN (SELECT university_id FROM students);
12. SELECT name 
    FROM subject 
    WHERE subject_id NOT IN (SELECT subject_id FROM grades WHERE value < 4);
13. SELECT name 
    FROM subject 
    WHERE subject_id IN (SELECT subject_id FROM grades WHERE value < 4);
14. SELECT * 
    FROM students 
    WHERE student_id NOT IN (SELECT student_id FROM grades WHERE value < 4);
15. SELECT * 
    FROM students 
    WHERE student_id IN (SELECT student_id FROM grades WHERE value < 4);
16. SELECT name, surname 
    FROM students 
    WHERE student_id IN (SELECT student_id FROM grades WHERE value = 5);
17. SELECT name, surname 
    FROM students 
    WHERE student_id NOT IN (SELECT student_id FROM grades WHERE value = 5);
18. SELECT COUNT(DISTINCT student_id) 
    FROM students 
    WHERE student_id NOT IN (SELECT student_id FROM grades);
19. SELECT COUNT(DISTINCT student_id) 
    FROM grades 
    WHERE value = 5 AND subject_id IN (SELECT subject_id FROM subject);
20. SELECT COUNT(DISTINCT student_id) 
    FROM students JOIN universities ON students.university_id = universities.university_id JOIN grades ON students.student_id = grades.student_id 
    WHERE value < 4 AND universities.city <> students.city;





    ========================================================================================





    1. SELECT name 
   FROM subject 
   WHERE subject_id IN (SELECT subject_id FROM subject GROUP BY subject_id HAVING COUNT(DISTINCT semester) > 1);
2. SELECT name, surname 
   FROM students JOIN grades ON students.student_id = grades.student_id 
   WHERE NOT EXISTS (SELECT * FROM grades WHERE student_id = students.student_id AND value < 3);
3. SELECT DISTINCT student_id 
   FROM grades 
   WHERE value IN (SELECT value FROM grades WHERE student_id = 12) AND student_id <> 12;
4. SELECT COUNT(DISTINCT student_id) 
   FROM students 
   WHERE student_id NOT IN (SELECT student_id FROM grades);
5. SELECT DISTINCT name 
   FROM subject JOIN teaches ON subject.subject_id = teaches.subject_id JOIN teachers ON teaches.teacher_id = teachers.teacher_id 
   WHERE teachers.surname = 'Колесников';
6. SELECT DISTINCT teachers.name, teachers.surname 
   FROM teaches JOIN teachers ON teaches.teacher_id = teachers.teacher_id JOIN subject ON teaches.subject_id = subject.subject_id 
   WHERE subject.semester = 1;
7. SELECT DISTINCT teachers.name, teachers.surname 
   FROM teaches JOIN teachers ON teaches.teacher_id = teachers.teacher_id JOIN subject ON teaches.subject_id = subject.subject_id JOIN teaches AS t2 ON subject.subject_id = t2.subject_id JOIN teachers AS t3 ON t2.teacher_id = t3.teacher_id 
   WHERE t3.surname = 'Сорокин';
8. SELECT DISTINCT students.surname 
   FROM students JOIN universities ON students.university_id = universities.university_id 
   WHERE universities.city = (SELECT city FROM universities ORDER BY city LIMIT 1);
9. SELECT DISTINCT students.surname 
   FROM students JOIN universities ON students.university_id = universities.university_id JOIN grades ON students.student_id = grades.student_id 
   WHERE grades.value = 5 AND students.city <> universities.city AND NOT EXISTS (SELECT * FROM grades WHERE student_id = students.student_id AND value < 5);




=======================================================================================================





1. SELECT name 
   FROM universities 
   WHERE rating >= (SELECT rating FROM universities WHERE name = 'ВГУ');
2. SELECT surname 
   FROM students 
   WHERE NOT EXISTS (SELECT * FROM universities WHERE city = students.city AND university_id = students.university_id);
3. SELECT subject_name 
   FROM exam_marks 
   WHERE subject_id NOT IN (SELECT subject_id FROM exam_marks WHERE mark <= ALL(SELECT mark FROM exam_marks WHERE subject_id = 105));
4. SELECT subject_name 
   FROM exam_marks 
   WHERE mark > (SELECT MAX(mark) FROM exam_marks WHERE subject_id = 105);
