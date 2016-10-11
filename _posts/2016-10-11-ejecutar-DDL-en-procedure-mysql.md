---
layout: post
title: Ejecutar comandos DDL de MySQL en un procedimiento almacenado
date: '2016-10-11'
author: Miguel González
tags:
- mysql
- programacion
---

Se me ha dado el caso hacendo unos scripts para poner en producción de SQL, que he necesitado crear un TRIGGER en distintas bases de datos que tienen la misma estructura. Para otras consultas DML de UPDATE, SELECT, ... había sido posible facilmente haciendo un PREPARE de las consultas y después un EXECUTE. El problema está cuando las consultas a ejecutar son consultas de definición. En este caso no es posible ejecutarla dentro de un procedimiento almacenado. 

Para solventar este problema, he ido generando estas consultas en una cadena que después he almacenado en un fichero y después ejecutado a través de SOURCE, quedando el código en algo parecido a lo siguiente:

```
DELIMITER $$
CREATE PROCEDURE `update_db`()
      BEGIN
            DECLARE TriggerExists INTEGER;
            DECLARE done BOOL DEFAULT FALSE;
            DECLARE curs CURSOR FOR SELECT ...; -- Select de distintos esquemas a actualizar
            DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

            SET @triggers := 'DELIMITER $$ ';
            OPEN curs;
            read_loop: LOOP

                  FETCH curs INTO schema_name;
                  IF done THEN
                        LEAVE read_loop;
                  END IF;

                  SELECT COUNT(1) INTO TriggerExists
                  FROM INFORMATION_SCHEMA.TRIGGERS
                  WHERE EVENT_OBJECT_SCHEMA = tienda_name
                        AND   TRIGGER_NAME = 'after_insert_planningcollaborator';
                  IF TriggerExists = 0 THEN

                    SET @st = CONCAT('CREATE TRIGGER ', schema_name , '.nombre_trigger AFTER INSERT ON tabla_trigger FOR EACH ROW BEGIN [... comandos del trigger] END$$ ');
                    SET @triggers := CONCAT_WS('\n\r', @triggers, @st);

                  END IF;
            END LOOP;
            CLOSE curs;
            
            -- Guardamos el resultado en un archivo
            SELECT @triggers INTO OUTFILE '/tmp/triggers_list.sql';
    END$$

DELIMITER ;

CALL update_all_dbs();

DROP procedure IF EXISTS `update_db`;
-- Ejecutamos todas las consultas
SOURCE /tmp/triggers_list.sql;            
```

De esta manera se puede hacer el pequeño truco para ejecutar CREATE TRIGGER dentro de un procedimiento almacenado.