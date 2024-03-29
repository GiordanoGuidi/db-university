## nome repo: db-university
Modellizzare la struttura di un database per memorizzare tutti i dati riguardanti una università:
- sono presenti diversi Dipartimenti (es.: Lettere e Filosofia, Matematica, Ingegneria ecc.);
- ogni Dipartimento offre più Corsi di Laurea (es.: Civiltà e Letterature Classiche, Informatica, Ingegneria Elettronica ecc..)
- ogni Corso di Laurea prevede diversi Corsi (es.: Letteratura Latina, Sistemi Operativi 1, Analisi Matematica 2 ecc.);
- ogni Corso può essere tenuto da diversi Insegnanti;
- ogni Corso prevede più appelli d’Esame;
- ogni Studente è iscritto ad un solo Corso di Laurea;
- ogni Studente può iscriversi a più appelli di Esame;
- per ogni appello d’Esame a cui lo Studente ha partecipato, è necessario memorizzare il voto ottenuto, anche se non sufficiente.
Pensiamo a quali entità (tabelle) creare per il nostro database e cerchiamo poi di stabilirne le relazioni.
Infine, andiamo a definire le colonne e i tipi di dato di ogni tabella.
Esportare il diagramma in jpg e caricarlo nella repo.

## Diagramma

![Questo è il testo dell'alt](Diagramma.jpg)
---

### Esercizio di oggi: DB University - Queries
### nome repo: db-university (stessa di ieri)
Dopo aver creato un nuovo database nel vostro phpMyAdmin importando il file in allegato, eseguite le query che trovate nei PDF, partendo da EX con Select per poi passare a quelle con GROUP.
Cosa  e come consegnare?
Dopo aver testato le vostre query con phpMyAdmin, riportatele in un file .sql e caricatelo nella vostra repo.
Sarebbe consigliabile effettuare un push ad ogni query completata.

### Query with SELECT
<!-- 1. Selezionare tutti gli studenti nati nel 1990 (160) -->
SELECT * FROM `students` WHERE YEAR(`date_of_birth`) = 1990;

<!-- 2. Selezionare tutti i corsi che valgono più di 10 crediti (479) -->
SELECT * FROM `courses` WHERE `cfu` > 10;

<!-- 3. Selezionare tutti gli studenti che hanno più di 30 anni -->
SELECT * FROM `students` WHERE YEAR(`date_of_birth`) < '1994';

<!-- 4. Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di laurea (286) -->
SELECT * FROM `courses` WHERE `year`='1' AND `period`='I semestre';

<!-- 5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del 20/06/2020 (21)-->
SELECT * FROM `exams` WHERE `date`= '2020-06-20' AND (`hour`) > '14:00:00';

<!-- 6. Selezionare tutti i corsi di laurea magistrale (38) -->
SELECT * FROM `degrees`  WHERE `level`= 'magistrale';

<!-- 7. Da quanti dipartimenti è composta l'università? (12) -->
SELECT * FROM `departments`;

<!-- 8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50) -->
SELECT * FROM `teachers` WHERE `phone` IS NULL;

---

### Query with GROUPBY

<!-- 1. Contare quanti iscritti ci sono stati ogni anno -->
SELECT COUNT(`id`),YEAR(`enrolment_date`) FROM `students` GROUP BY YEAR(`enrolment_date`);

<!-- 2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio -->
SELECT COUNT(`id`),`office_number` FROM `teachers` GROUP BY(`office_number`);

<!-- 3. Calcolare la media dei voti di ogni appello d'esame -->
SELECT AVG(`vote`) AS `media_voti`,`exam_id` FROM `exam_student` GROUP BY `exam_id`;

<!-- 4. Contare quanti corsi di laurea ci sono per ogni dipartimento -->
SELECT COUNT(`id`) AS`numero_corsi`,`department_id` FROM `degrees` GROUP BY(`department_id`);

### Query with JOIN

<!-- 1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia -->
SELECT `students`.`name` AS `nome_studente`,`students`.`surname` AS `cognome_studente`,`degrees`.`name`
FROM `students`
JOIN `degrees`
ON `students`.`degree_id`= `degrees`.`id`
WHERE `degrees`.`name`='Corso di Laurea in Economia';

<!-- 2. Selezionare tutti i Corsi di Laurea del Dipartimento di Neuroscienze -->
SELECT `courses`.`name` AS `nome_corso`,`degrees`.`name`AS`nome_corso_di_laurea`,`departments`.`name`AS`nome_dipartimento`
FROM `courses`
JOIN `degrees`
ON `degrees`.`id`=`courses`.`degree_id`
JOIN `departments`
ON `departments`.`id`=`degrees`.`department_id`
WHERE `departments`.`name`= 'Dipartimento di Neuroscienze';

<!-- 3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44) -->
SELECT C.`name` AS`nome_corso`, C.`description` AS`descrizione_corso`,C.`period`
AS`semestre`, C.`year` AS`anno`, T.`name` AS`nome_prof`, T.`surname`AS`cognome_prof`
FROM `courses` AS C
JOIN `course_teacher` AS CT ON C.`id`= CT.`course_id`
JOIN `teachers` AS T ON T.`id`= CT.`teacher_id`
WHERE T.`name`='Fulvio'
AND T.`surname`='Amato';

<!-- 4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento,
 in ordine alfabetico per cognome e nome -->
SELECT `students`.`id`AS`id`,`students`.`name` AS`nome_studente`,`students`.`surname` AS`cognome_studente`,`degrees`.`name`AS`corso_di_laurea`,
`degrees`.`level`AS`livello`,`departments`.`name`AS`nome_dipartimento`
FROM `students`
JOIN `degrees`
ON `degrees`.`id`=`students`.`degree_id`
JOIN `departments`
ON `departments`.`id`=`degrees`.`department_id`
ORDER BY `students`.`surname` ASC;

<!-- 5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti -->
SELECT `degrees`.`name` AS`corso_di_laurea`,`courses`.`name` AS `nome_corso`,`teachers`.`name` AS`nome_prof`,`teachers`.`surname` AS`cognome_prof`
 FROM `degrees`
 JOIN `courses`
 ON `degrees`.`id`=`courses`.`degree_id`
 JOIN `course_teacher`
 ON `courses`.`id`=`course_teacher`.`course_id`
 JOIN `teachers`
 ON `teachers`.`id`=`course_teacher`.`teacher_id`;

<!-- 6. Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54) -->
SELECT DISTINCT `teachers`.`name` AS`nome_prof`,`teachers`.`surname` AS`cognome_prof`,`departments`.`name`AS`dipartimento`
FROM `teachers`
JOIN `course_teacher`
ON `teachers`.`id`=`course_teacher`.`teacher_id`
JOIN `courses`
ON `courses`.`id`= `course_teacher`.`course_id`
JOIN `degrees`
ON `degrees`.`id`=`courses`.`degree_id`
JOIN `departments`
ON `departments`.`id`=`degrees`.`department_id`
WHERE `departments`.`name`= 'Dipartimento di Matematica';

<!-- 7. BONUS: Selezionare per ogni studente quanti tentativi d’esame ha sostenuto per 
superare ciascuno dei suoi esami -->
