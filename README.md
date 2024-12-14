# SQL-Triggers
This repository contains SQL scripts to demonstrate the use of triggers in MySQL.

Here we create table named "teachers" and then use before insert ,after insert ,before delete and after delete triggers.

## Table
    CREATE TABLE teachers (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50),
    subject VARCHAR(50),
    experience INT,
    salary DECIMAL(10,2)
    );

    INSERT INTO teachers (name, subject, experience, salary)
    VALUES
    ('Alice', 'Math', 10, 50000),
    ('Bob', 'Science', 8, 45000),
    ('Charlie', 'English', 12, 60000),
    ('David', 'History', 5, 35000),
    ('Emily', 'Geography', 7, 40000),
    ('Frank', 'Computer Science', 15, 70000),
    ('Grace', 'Art', 3, 30000),
    ('Henry', 'Music', 9, 45000);

1. **Before Insert Trigger:** Prevents inserting records with negative salaries.

       DELIMITER $$
       create trigger before_insert_teachers 
       before insert on teachers for each row
       begin
       if new.salary <0 then 
       signal sqlstate '45000' set message_text="salary cannot be negative" ;
       end if;
       end $$
       DELIMITER ;
       INSERT INTO teachers (name, subject, experience, salary) values('jak','art',7,-4.00);
   
2. **After Insert Trigger:** Logs the insertion of new teachers.

       create table teacher_log (teacher_id int primary key,action VARCHAR(255),
       timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
       );
       DELIMITER $$
       create trigger after_insert_teacher
       after insert on teachers  for each row
       begin
       INSERT INTO teacher_log (teacher_id, action, timestamp)
       VALUES (NEW.id, 'New teacher added', NOW());
       end $$
       DELIMITER ;
       INSERT INTO teachers (name, subject, experience, salary) values('jahan','medical',3,5000);
       select * from teachers;
       select * from teacher_log;
   
3. **Before Delete Trigger:** Prevents deleting teachers with more than 10 years of experience.

       DELIMITER $$
       CREATE TRIGGER before_delete_trigger
       BEFORE DELETE ON teachers
       FOR EACH ROW
       BEGIN
       DECLARE old_experience INT;

       SELECT experience INTO old_experience FROM teachers WHERE id = OLD.id;

       IF old_experience > 10 THEN
       SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Cannot delete a row with experience greater than 10 years';
       END IF;
       END $$
       DELIMITER ;
       delete from teachers where id=3;

   
4. **After Delete Trigger:** Logs the deletion of teachers.

       DELIMITER $$
       create trigger AFTER_DELETE_TRIGGER
       after delete on teachers for each row
       begin
       INSERT INTO teacher_log (teacher_id, action, timestamp)
       VALUES (old.id, 'New teacher added', NOW());
       end $$
       delimiter ;
       delete from teachers where id=5;
       select * from teachers;
       select * from teacher_log;

## NOTE:
   SQL Triggers: Your Database's Automatic Taskmasters.
   
   A trigger is a special kind of stored procedure that automatically executes in response to specific events in a database

   syntax: 
           
           DELIMITER
           create trigger (trigger_name)
           (before|after)(insert|update|delete)on table_name
           for each row
           begin
             #set of statements
		       end $$
           delimiter;
