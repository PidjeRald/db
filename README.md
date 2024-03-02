1----------------------------------------------------------------------------------------

1. SELECT first_name, last_name
   FROM students
   WHERE student_id IN (SELECT student_id FROM grades WHERE grade = 'A');

2. SELECT first_name, last_name
   FROM students
   WHERE student_id IN (SELECT student_id FROM grades GROUP BY student_id HAVING COUNT(DISTINCT grade) = 3);

3. SELECT student_id
   FROM grades
   WHERE grade IN (SELECT grade FROM grades WHERE student_id = 12)
   AND student_id <> 12;

4. SELECT t1.teacher_id, t2.teacher_id
   FROM teacher_subjects t1, teacher_subjects t2
   WHERE t1.subject_id = t2.subject_id
   AND t1.teacher_id <> t2.teacher_id;

5. SELECT first_name, last_name
   FROM students
   WHERE student_id NOT IN (SELECT student_id FROM grades WHERE grade = 'A');

6. SELECT subject_name
   FROM subjects
   WHERE hours > 50;

7. SELECT COUNT(*)
   FROM students
   WHERE student_id NOT IN (SELECT student_id FROM grades);

8. SELECT COUNT(*)
   FROM students
   WHERE student_id IN (SELECT student_id FROM grades GROUP BY student_id HAVING COUNT(grade) = 1 AND MAX(grade) = 'A');

9. SELECT *
   FROM subjects
   WHERE teacher_id = (SELECT teacher_id FROM teachers WHERE last_name = 'Колесников');

10. SELECT teachers.first_name, teachers.last_name
    FROM teachers, teacher_subjects
    WHERE teachers.teacher_id = teacher_subjects.teacher_id
    AND teacher_subjects.course = 1;

11. SELECT students.first_name, students.last_name
    FROM students, universities
    WHERE students.university_city <> universities.city;

12. SELECT COUNT(*)
    FROM grades
    WHERE student_id = 32 AND grade <> 'F';

13. SELECT teachers.first_name, teachers.last_name
    FROM teachers, teacher_subjects
    WHERE teachers.teacher_id = teacher_subjects.teacher_id
    GROUP BY teachers.teacher_id
    HAVING COUNT(DISTINCT subject_id) >= 2;

14. SELECT teachers.first_name, teachers.last_name
    FROM teachers, teacher_subjects
    WHERE teachers.teacher_id = teacher_subjects.teacher_id
    GROUP BY teachers.teacher_id
    HAVING COUNT(DISTINCT semester) >= 2;

15. SELECT subject_name
    FROM subjects
    GROUP BY subject_name
    HAVING COUNT(DISTINCT teacher_id) >= 2;

16. SELECT subjects.subject_name, teachers.first_name, teachers.last_name, students.city
    FROM subjects
    JOIN teacher_subjects ON subjects.subject_id = teacher_subjects.subject_id
    JOIN teachers ON teacher_subjects.teacher_id = teachers.teacher_id
    JOIN students ON subjects.university_id = students.university_id
    WHERE students.university_name = 'ВГУ';

17. SELECT SUM(hours)
    FROM subjects
    JOIN teacher_subjects ON subjects.subject_id = teacher_subjects.subject_id
    WHERE teacher_subjects.teacher_id = (SELECT teacher_id FROM teachers WHERE last_name = 'Лагутин');

18. SELECT DISTINCT t1.last_name
    FROM teacher_subjects t1, teacher_subjects t2
    WHERE t1.subject_id = t2.subject_id AND t1.teacher_id <> t2.teacher_id
    AND t2.last_name = 'Сорокин';

19. SELECT last_name
    FROM teachers
    GROUP BY last_name
    HAVING SUM(hours) > (SELECT SUM(hours) FROM teachers WHERE last_name = 'Николаев');

20. SELECT DISTINCT teachers.first_name, teachers.last_name
    FROM teacher_subjects
    JOIN teachers ON teacher_subjects.teacher_id = teachers.teacher_id
    WHERE teacher_subjects.subject_id IN (SELECT subject_id FROM teacher_subjects WHERE teacher_id = (SELECT teacher_id FROM teachers WHERE last_name = 'Сорокин'));

21. SELECT DISTINCT teachers.last_name
    FROM teachers
    JOIN teacher_subjects ON teachers.teacher_id = teacher_subjects.teacher_id
    JOIN subjects ON teacher_subjects.subject_id = subjects.subject_id
    JOIN students ON subjects.university_id = students.university_id
    JOIN universities ON students.university_id = universities.university_id
    WHERE universities.rating < 200;

22. SELECT university_name
    FROM universities
    WHERE city = 'Москва' AND rating < (SELECT rating FROM universities WHERE university_name = 'ВГУ');

23. SELECT students.last_name
    FROM students
    JOIN universities ON students.university_id = universities.university_id
    ORDER BY universities.city
    LIMIT 1;

24. SELECT last_name
    FROM students
    GROUP BY last_name
    HAVING AVG((SELECT grade FROM grades WHERE student_id = students.student_id)) > 4;

25. SELECT SUM(hours)
    FROM subjects
    JOIN students ON subjects.university_id = students.university_id
    WHERE students.course = 1 AND students.university_name = 'ВГУ';

26. SELECT AVG(hours)
    FROM subjects
    JOIN students ON subjects.university_id = students.university_id
    WHERE students.course = 2 AND students.university_name = 'ВГУ';

27. SELECT COUNT(*)
    FROM students
    WHERE student_id IN (SELECT student_id FROM grades WHERE grade = 'F')
    AND city <> (SELECT city FROM universities WHERE university_id = students.university_id);

28. SELECT last_name
    FROM students
    GROUP BY last_name
    HAVING COUNT(DISTINCT grade) = 1 AND MAX(grade) = 'A'
    AND city <> (SELECT city FROM universities WHERE university_id = students.university_id);

29. SELECT last_name
    FROM students
    GROUP BY last_name
    HAVING COUNT(DISTINCT grade) >= 2
    AND city <> (SELECT city FROM universities WHERE university_id = students.university_id);

30. Получить фамилии студентов, сдававших экзамен по информатике:
   - SELECT last_name FROM students WHERE student_id IN (SELECT student_id FROM grades WHERE subject_id = (SELECT subject_id FROM subjects WHERE subject_name = 'Информатика'));
   - SELECT last_name FROM students WHERE student_id IN (SELECT student_id FROM grades WHERE grade <> 'F' AND subject_id IN (SELECT subject_id FROM subjects WHERE subject_name = 'Информатика'));
   - ...

31. Получить фамилии преподавателей, обучающих информатике:
   - SELECT last_name FROM teachers WHERE teacher_id IN (SELECT teacher_id FROM teacher_subjects WHERE subject_id = (SELECT subject_id FROM subjects WHERE subject_name = 'Информатика'));
   - SELECT last_name FROM teachers WHERE teacher_id IN (SELECT teacher_id FROM teacher_subjects WHERE subject_id IN (SELECT subject_id FROM subjects WHERE subject_name = 'Информатика'));
   - ...

2----------------------------------------------------------------------------------------

CREATE TABLE Пользователь (
    Email CHAR(15),
    ID_пользователя INT,
    ФИО CHAR(20),
    PRIMARY KEY (ID_пользователя)
);

CREATE TABLE Товар (
    Цена DOUBLE,
    ID_товара INT,
    Название CHAR(20),
    PRIMARY KEY (ID_товара)
);

CREATE TABLE Заказ (
    ID_пользователя INT,
    Дата CHAR(10),
    ID_заказа INT,
    Статус CHAR(10),
    ID_товара INT,
    PRIMARY KEY (ID_заказа),
    FOREIGN KEY (ID_пользователя) REFERENCES Пользователь(ID_пользователя),
    FOREIGN KEY (ID_товара) REFERENCES Товар(ID_товара)
);

CREATE TABLE Заказ_Товар (
    ID_заказа INT,
    ID_товара INT,
    FOREIGN KEY (ID_заказа) REFERENCES Заказ(ID_заказа),
    FOREIGN KEY (ID_товара) REFERENCES Товар(ID_товара),
    PRIMARY KEY (ID_заказа, ID_товара)
);

-- Заполнение таблицы "Пользователь"
INSERT INTO Пользователь (Email, ID_пользователя, ФИО)
VALUES ('user1@example.com', 1, 'Иванов Иван Иванович');

-- Заполнение таблицы "Товар"
INSERT INTO Товар (Цена, ID_товара, Название)
VALUES (100.50, 1, 'Футболка'),
       (200.75, 2, 'Джинсы');

-- Заполнение таблицы "Заказ"
INSERT INTO Заказ (ID_пользователя, Дата, ID_заказа, Статус, ID_товара)
VALUES (1, '2022-05-15', 1, 'Оплачен', 1),
       (1, '2022-05-16', 2, 'В обработке', 2);

-- Заполнение таблицы "Заказ_Товар"
INSERT INTO Заказ_Товар (ID_заказа, ID_товара)
VALUES (1, 1),
       (2, 2);

3----------------------------------------------------------------------------------------

CREATE TABLE EXAM_MARKS (
    STUDENT_ID INT,
    SUBJECT_ID INT,
    EXAM_DATE DATE,
    MARK INT,
    PRIMARY KEY (STUDENT_ID, SUBJECT_ID, EXAM_DATE),
    UNIQUE (STUDENT_ID, SUBJECT_ID),
    UNIQUE (EXAM_DATE)
);

CREATE TABLE SUBJECT (
    SUBJ_ID INT PRIMARY KEY,
    SUBJECT_NAME VARCHAR(50),
    HOURS INT DEFAULT 36 CHECK (HOURS > 0),
    SEMESTER INT CHECK (SEMESTER BETWEEN 1 AND 12)
);

CREATE TABLE EXAM_MARKS (
    STUDENT_ID INT NOT NULL,
    SUBJ_ID INT NOT NULL,
    EXAM_ID INT NOT NULL,
    EXAM_DATE DATE,
    MARK INT,
    PRIMARY KEY (STUDENT_ID, SUBJ_ID, EXAM_ID),
    FOREIGN KEY (SUBJ_ID) REFERENCES SUBJECT(SUBJ_ID),
    CHECK (EXAM_ID > SUBJ_ID),
    CHECK (SUBJ_ID > STUDENT_ID)
);

4----------------------------------------------------------------------------------------

CREATE TABLE SUBJECT_1 (
    SUBJ_ID INT PRIMARY KEY,
    SUBJECT_NAME VARCHAR(50),
    HOURS INT DEFAULT 36 CHECK (HOURS > 0),
    SEMESTER INT CHECK (SEMESTER BETWEEN 1 AND 12)
);

CREATE TABLE SUBJ_LECT_1 (
    LECTURER_ID INT,
    SUBJ_ID INT,
    PRIMARY KEY (LECTURER_ID, SUBJ_ID),
    FOREIGN KEY (LECTURER_ID) REFERENCES LECTURER_1(LECTURER_ID),
    FOREIGN KEY (SUBJ_ID) REFERENCES SUBJECT_1(SUBJ_ID)
);

CREATE TABLE SUBJ_LECT_1 (
    LECTURER_ID INT,
    SUBJ_ID INT,
    PRIMARY KEY (LECTURER_ID, SUBJ_ID),
    FOREIGN KEY (LECTURER_ID) REFERENCES LECTURER_1(LECTURER_ID) ON UPDATE RESTRICT ON DELETE RESTRICT,
    FOREIGN KEY (SUBJ_ID) REFERENCES SUBJECT_1(SUBJ_ID) ON UPDATE RESTRICT ON DELETE RESTRICT
);

CREATE TABLE LECTURER_1 (
    LECTURER_ID INT PRIMARY KEY,
    UNIV_ID INT,
    FOREIGN KEY (UNIV_ID) REFERENCES UNIVERSITY_1(UNIV_ID) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE UNIVERSITY_1 (
    UNIV_ID INT PRIMARY KEY,
    UNIVERSITY_NAME VARCHAR(50)
);

CREATE TABLE EXAM_MARKS_1 (
    EXAM_ID INT,
    STUDENT_ID INT,
    SUBJ_ID INT,
    MARK INT,
    PRIMARY KEY (EXAM_ID, STUDENT_ID, SUBJ_ID),
    FOREIGN KEY (STUDENT_ID) REFERENCES STUDENT_1(STUDENT_ID) ON UPDATE CASCADE ON DELETE NO ACTION,
    FOREIGN KEY (SUBJ_ID) REFERENCES SUBJECT_1(SUBJ_ID) ON UPDATE CASCADE ON DELETE NO ACTION
);

CREATE TABLE STUDENT_1 (
    STUDENT_ID INT PRIMARY KEY,
    NAME VARCHAR(50),
    GROUP_ID INT,
    SENIOR_STUDENT INT,
    FOREIGN KEY (SENIOR_STUDENT) REFERENCES STUDENT_1(STUDENT_ID)
);

CREATE TABLE STUDENT_2 (
    STUDENT_ID INT PRIMARY KEY,
    NAME VARCHAR(50),
    GROUP_ID INT,
    UNIV_ID INT,
    FOREIGN KEY (UNIV_ID) REFERENCES UNIVERSITY_1(UNIV_ID) ON DELETE SET NULL
);

-- Запросы CREATE TABLE для всех таблиц

-- Запросы ALTER TABLE для добавления ограничений ссылочной целостности
ALTER TABLE SUBJ_LECT_1 ADD CONSTRAINT fk_subj_lect_lecturer FOREIGN KEY (LECTURER_ID) REFERENCES LECTURER_1(LECTURER_ID) ON UPDATE RESTRICT ON DELETE RESTRICT;
ALTER TABLE SUBJ_LECT_1 ADD CONSTRAINT fk_subj_lect_subject FOREIGN KEY (SUBJ_ID) REFERENCES SUBJECT_1(SUBJ_ID) ON UPDATE RESTRICT ON DELETE RESTRICT;

-- Повторите аналогичные команды ALTER TABLE для остальных таблиц, добавляя необходимые ограничения ссылочной целостности.

5----------------------------------------------------------------------------------------

CREATE VIEW EXCELLENT_STUDENTS_VIEW AS
SELECT *
FROM STUDENT_1 S
WHERE NOT EXISTS (
    SELECT 1
    FROM EXAM_MARKS_1 E
    WHERE E.STUDENT_ID = S.STUDENT_ID AND E.MARK < 5
);

CREATE VIEW STUDENTS_PER_CITY_VIEW AS
SELECT CITY, COUNT(*) AS STUDENT_COUNT
FROM STUDENT_1
GROUP BY CITY;

CREATE VIEW STUDENT_SCORES_VIEW AS
SELECT S.STUDENT_ID, S.LAST_NAME, S.FIRST_NAME, AVG(E.MARK) AS AVERAGE_MARK, SUM(E.MARK) AS TOTAL_MARK
FROM STUDENT_1 S
JOIN EXAM_MARKS_1 E ON S.STUDENT_ID = E.STUDENT_ID
GROUP BY S.STUDENT_ID, S.LAST_NAME, S.FIRST_NAME;

CREATE VIEW EXAM_COUNT_PER_STUDENT_VIEW AS
SELECT STUDENT_ID, COUNT(*) AS EXAM_COUNT
FROM EXAM_MARKS_1
GROUP BY STUDENT_ID;
