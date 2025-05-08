## Consegna 1: Query con Select


### USE db_university; 

#### Selezionare tutti gli studenti nati nel 1990 
SELECT `name`, `surname`, `date_of_birth`  
FROM `students`  
WHERE `date_of_birth` >= '1990-01-01'  
  AND `date_of_birth` < '1991-01-01'  
ORDER BY `date_of_birth` DESC;  

#### Selezionare tutti i corsi che valgono più di 10 crediti 
SELECT `name`, `cfu`  
FROM `courses`  
WHERE `cfu` >= 10  
ORDER BY `cfu` DESC;

#### Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di laurea 

SELECT `name`, `year`, `period`  
FROM `courses`  
WHERE `year` = 1  
  AND `period` = 'I semestre'  
ORDER BY `name` ASC;  

#### Selezionare tutti gli studenti che hanno più di 30 anni 

SELECT `name`, `date_of_birth`  
FROM `students`  
WHERE TIMESTAMPDIFF(YEAR, `date_of_birth`, CURDATE()) > 30  
ORDER BY `date_of_birth` DESC;  

#### Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del 20/06/2020 
SELECT `hour`, `date`, `location`  
FROM `exams`  
WHERE `date` = '2020-06-20'  
  AND `hour` > '14:00:00'  
ORDER BY `hour` ASC;  

#### Selezionare tutti i corsi di laurea magistrale 
SELECT `name`, `level`  
FROM `degrees`  
WHERE `level` = 'magistrale'  
ORDER BY `name` ASC;  

#### Da quanti dipartimenti è composta l'università? 
SELECT COUNT(*) AS numero_dipartimenti  
FROM `departments`;  

#### Quanti sono gli insegnanti che non hanno un numero di telefono? 
SELECT COUNT(*)  
FROM `teachers`  
WHERE `phone` IS NULL;  

---
## Consegna 2: Query con GROUP BY

#### Contare quanti iscritti ci sono stati ogni anno

SELECT YEAR(`enrolment_date`) AS anno_iscrizione, COUNT(*) AS numero_studenti  
FROM `students`  
GROUP BY YEAR(`enrolment_date`)  
ORDER BY anno_iscrizione;  

#### Contare gli insegnanti che hanno l'ufficio nello stesso edificio

SELECT `office_address` AS indirizzo_ufficio, COUNT(*) AS numero_professori  
FROM `teachers`  
GROUP BY `office_address`  
ORDER BY `office_address`;  

#### Calcolare la media dei voti di ogni appello d'esame

SELECT `exam_id`, AVG(`vote`) AS media_voto  
FROM `exam_student`  
GROUP BY `exam_id`  
ORDER BY `exam_id`;  

#### Contare quanti corsi di laurea ci sono per ogni dipartimento

SELECT `department_id` AS id_dipartimento, COUNT(*) AS numero_corsi  
FROM `degrees`  
GROUP BY `department_id`  
ORDER BY `department_id`;  

---
## Consegna 3: Query con JOIN

#### Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia
SELECT `students`.`name`, `students`.`surname`, `degrees`.*  
FROM `students`  
INNER JOIN `degrees` ON `students`.`degree_id` = `degrees`.`id`  
WHERE `degrees`.`name` = 'Corso di Laurea in Economia';  

#### Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di Neuroscienze

SELECT `degrees`.`name` AS nome_corso_magistrale   
FROM `degrees`  
INNER JOIN `departments` ON `degrees`.`department_id` = `departments`.`id`  
WHERE `degrees`.`level` = 'magistrale'  
  AND `departments`.`name` = 'Dipartimento di Neuroscienze'  
ORDER BY `degrees`.`name`;  

#### Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)

SELECT `courses`.`name` AS nome_corso, `teachers`.`name` AS nome_docente, `teachers`.`surname` AS cognome_docente  
FROM `courses`   
INNER JOIN `course_teacher` ON `courses`.`id` = `course_teacher`.`course_id`  
INNER JOIN `teachers` ON `course_teacher`.`teacher_id` = `teachers`.`id`  
WHERE `teachers`.`id` = 44  
ORDER BY `courses`.`name`;  

#### Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e nome

SELECT   
  `students`.`name` AS nome_studente,  
  `students`.`surname` AS cognome_studente,  
  `degrees`.`name` AS corso_di_laurea,  
  `departments`.`name` AS dipartimento  
FROM `students`   
INNER JOIN `degrees` ON `students`.`degree_id` = `degrees`.`id`  
INNER JOIN `departments`  ON `degrees`.`department_id` = `departments`.`id`  
ORDER BY `students`.`surname`, `students`.`name`;  

#### Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti
 
SELECT   
  degrees.name AS nome_corso_laurea,  
  courses.name AS nome_insegnamento,  
  teachers.name AS nome_docente,  
  teachers.surname AS cognome_docente  
FROM degrees  
JOIN courses ON degrees.id = courses.degree_id  
JOIN course_teacher ON courses.id = course_teacher.course_id  
JOIN teachers ON course_teacher.teacher_id = teachers.id  
ORDER BY degrees.name, courses.name, teachers.surname;  

#### Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)

SELECT DISTINCT teachers.name, teachers.surname  
FROM teachers  
JOIN course_teacher ON teachers.id = course_teacher.teacher_id  
JOIN courses ON course_teacher.course_id = courses.id  
JOIN degrees ON courses.degree_id = degrees.id  
JOIN departments ON degrees.department_id = departments.id  
WHERE departments.name = 'Dipartimento di Matematica'  
ORDER BY teachers.surname, teachers.name;  

#### BONUS: Selezionare per ogni studente il numero di tentativi sostenuti per ogni esame, stampando anche il voto massimo. Successivamente, filtrare i tentativi con voto minimo 18.