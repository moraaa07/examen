# examen


---------------

_______________

Trigger : para insertar 

CREATE TRIGGER Inserta_auditoria_clientes AFTER INSERT ON clientes
FOR EACH ROW

begin set 

INSERT INTO auditoria_clientes(nombre_nuevo, seccion_nueva, usuario,
modificado, proceso, Id_Cliente)
VALUES (NEW.nombre, NEW.seccion, CURRENT_USER(), NOW(),
NEW.Accion, NEW.id_cliente );

--------------

______________

Trigger: para modificar

CREATE TRIGGER Modifica_auditoria_clientes BEFORE UPDATE ON clientes
FOR EACH ROW
INSERT INTO auditoria_clientes(nombre_anterior, seccion_anterior,
nombre_nuevo, seccion_nueva, usuario, modificado, Id_Cliente)
VALUES (OLD.nombre, OLD.seccion, NEW.nombre, NEW.seccion,
CURRENT_USER(), NOW(), NEW.id_cliente);

----------------
________________

Trigger : para eliminar

CREATE TRIGGER Elimina_auditoria_clientes AFTER DELETE ON clientes
FOR EACH ROW
INSERT INTO auditoria_clientes(nombre_anterior, seccion_anterior,
usuario, modificado, Id_Cliente)
VALUES (OLD.nombre, OLD.seccion, CURRENT_USER(), NOW(),
OLD.id_cliente);


----------------
________________

Insert:

INSERT INTO clientes (nombre, seccion) VALUES
('Miguel','informatica'),
('Rosa','comida'),
('Maria','ropa'),
('Albert','informatica'),
('Jordi','comida');


------------------
__________________




### Se pide crear un disparador de nombre calc_valorventa asociado a la 
tabla productos para que antes de insertar un 
nuevo producto calcule el valor de venta con la fórmula siguiente


DELIMITER $$ 
Drop trigger if exists calc_valorventa
$$
create trigger calc_valorventa before insert
on productos for each row
begin
set new.Valorventa = new.Costo+ new.Costo*new.porgana/100;
end
$$
DELIMITER;


### La “idea” es crear un trigger que antes de actualizar un producto vuelva a calcular el valor de venta.

DELIMITER $$ 
drop trigger if exists act_valorventa
$$
create trigger act_valorventa before update
on productos for each row
begin
set new.Valorventa = new.Costo+ new.Costo*new.porgana/100;
end
$$ 
DELIMITER;

### Preparar un disparador, para controlar que si  no se introduce porcentaje  
de venta en una inserción, es decir es NULL ó 0, se aplique por defecto un  20%.
Este disparador se debe ejecutar antes que el del ejercicio 1, así que 
se debe usar la opción precedes para cambiar el orden de los disparadores.

DELIMITER $$
drop trigger if exists porct_valorventa
$$
create trigger porct_valorventa before insert
on productos for each row
precedes calc_valorventa
begin
if new.porgana='0' or new.porgana is null then 
set new.porgana='20';
end if;
end
$$
DELIMITER ;

### Crear una tabla nueva de nombre borrados_prod, con los mismos campos que productos 
más un campo fecha y otro campo usuario.
A continuación crear un disparador que se active antes del delete de un producto. 
El disparador insertara todos los datos old del procucto a borrar en la tabla nueva. 
Para la fecha usamos curdate() y para el usuario user()
Hacer un borrado de un producto y hacer comprobaciones en la nueva tabla.

DELIMITER $$ 

drop trigger if exists del_valorventa
$$

create trigger del_valorventa before delete
on productos for each row
begin 
insert into borrados_prod values (old.codigo, old.nombre, old.porgana, old.Costo, old.Valorventa, old.cantidad,
curdate(), user());
end
$$
DELIMITER ;


Disparadores (triggers)
1. Rol

Los disparadores o triggers son objetos cuyo objetivo es ejecutar el código en respuesta a un evento que ocurre en 
una  tabla.  Los  eventos  pueden  ser  de  tres  tipos:  INSERT,  UPDATE  o  DELETE  (o  similares,  por  ejemplo:  la 
instrucción  REPLACE  equivalente  a  INSERT,  o  INSERT  y  DELETE).  La  orden  de  activación  se  define  antes 
(BEFORE) o después (AFTER) del evento. Por ejemplo, el DBA seleccionará BEFORE para un test de comprobación 
de datos y AFTER para el registro


2. Sintaxis

La sintaxis que se debe observar durante la creación de un trigger es:


CREATE
[DEFINER = { user | CURRENT_USER }]
TRIGGER nombre_del_disparador momento_de_activación
acción_ejecutada
ON nombre_de_tabla FOR EACH ROW cuerpo_del_disparador


Con  momento_de_activación  que  vale  BEFORE  o  AFTER  y  acción_ejecutada  que  toma  los  valores 
INSERT, UPDATE o DELETE.

En  el  cuerpo  del  disparador,  los  alias  OLD  y  NEW  están  disponibles  en  función  del  contexto 
(INSERT/UPATE/DELETE).  Permiten  acceder,  de  forma  respectiva,  al  valor  de  una  columna  de  la  tabla  que 
contiene el disparador, antes y después de la modificación:


l Durante un UPDATE, OLD.Columna hace referencia al valor de la columna antes de la modificación, mientras que 
NEW.Columna, al valor después de su modificación.
l Durante un INSERT, solo existe NEW.Columna.
l Durante un DELETE, solo existe OLD.Columna.
CREATE TABLE City (
 id int(11) NOT NULL DEFAULT ’0’,
 name char(35) NOT NULL DEFAULT ’’
) ENGINE=InnoDB




CREATE TABLE City_del (
 id int(11) NOT NULL DEFAULT ’0’,
 name char(35) NOT NULL DEFAULT ’’
) ENGINE=InnoDB

mysql> SELECT count(*) FROM City;
+----------+
| count(*) |
+----------+


En  caso  de  fallo  de  un  disparador,  si  las  tablas  implicadas  son  transaccionales  (como  con InnoDB),  todas  las 
modificaciones se anularán. En caso contrario (como con MyISAM), las operaciones no continuarán, pero todas las 
modificaciones hechas hasta el fallo del trigger se mantienen.
Ejemplo de un trigger que protege algunos registros insertados en la tabla journal.
| 4079 |
+----------+

mysql> SELECT count(*) FROM City_del;
+----------+
| count(*) |
+----------+
| 0 |
+----------+


DELIMITER //

CREATE TRIGGER papelera BEFORE DELETE ON City FOR EACH ROW
BEGIN
 INSERT INTO City_del (id, name) VALUES (OLD.id, OLD.name);
END //
DELIMITER ;
mysql> DELETE FROM City WHERE id = 1;
Query OK, 1 row affected (0,15 sec)
mysql> SELECT * FROM City_del;
+----+-------+
| id | name |
+----+-------+
| 1 | Kabul |
+----+-------+
mysql> CREATE TABLE journal (
 ts timestamp,
 server
enum(’host01’,’host02’,’host03’,’host04’,’host05’,’host06’,’host07’,
’host08’,’host09’,’host10’,’host11’,’host12’) DEFAULT NULL,
 program
enum(’web’,’bd’,’appli1’,’appli2’,’appli3’,’appli4’,’appli5’)
DEFAULT NULL,
 msg varchar(500) DEFAULT NULL
) ENGINE=InnoDB;

mysql> CREATE TABLE logs_history (
 ts timestamp,
 server
enum(’host01’,’host02’,’host03’,’host04’,’host05’,’host06’,
’host07’,



Es posible utilizar en el cuerpo de los disparadores las mismas funciones que en una rutina almacenada para, como 
en el ejemplo, filtrar algunos elementos para el histórico de modificaciones. No existe ninguna instrucción para hacer 
fallar  un  disparador.  Si  debemos  invalidar  los  datos,  podemos  hacerlo  añadiendo  una  columna  que  servirá  para 
informar  al  inicio  del  trigger  los  registros  que  se  deben  excluir.  Luego,  habrá  que  pensar  de  forma  periódica  en 
purgar estos registros o utilizar una tarea programada.
De  manera  general,  las  restricciones  que  se  aplican  a  las  rutinas  almacenadas  se  aplican  también  a  los 
disparadores.
Una importante limitación  se eliminó  con MySQL 5.7. Antes, no era posible  crear más de un disparador para una 
’host08’,’host09’,’host10’,’host11’,’host12’) DEFAULT NULL,
 msg varchar(500) DEFAULT NULL
) ENGINE=InnoDB;


mysql> DELIMITER //

mysql> CREATE TRIGGER history_log BEFORE INSERT ON journal FOR
EACH ROW
BEGIN
 IF (SELECT FIELD(NEW.program,’web’,’bd’,’appli3’)) THEN
 INSERT INTO logs_history(server,msg) VALUES
(NEW.`server`,NEW.msg);
 END IF;
END //

mysql> DELIMITER ;


mysql> INSERT INTO journal (server,program,msg) VALUES
(’host11’,’appli3’,’message 4’);
Query OK, 1 row affected (0,04 sec)

mysql> SELECT * FROM logs_history;
+---------------------+--------+-----------+
| ts | server | msg |
+---------------------+--------+-----------+
| 2012-11-07 11:56:11 | host11 | message 4 |
+---------------------+--------+-----------+

mysql> INSERT INTO journal (server,program,msg) VALUES
(’host11’,’appli2’,’message 3’);
Query OK, 1 row affected (0,04 sec)

mysql> SELECT * FROM logs_history;
+---------------------+--------+-----------+
| ts | server | msg |
+---------------------+--------+-----------+
| 2012-11-07 11:56:11 | host11 | message 4 |
+---------------------+--------+-----------+
3. Restricciones
© Éditions ENI – Todos los derechos reservados – Copia personal de MARIA LUZ LORENZO GARCIA - 3 -

misma acción y en la misma posición (BEFORE o AFTER). Con MySQL 5.7, también podemos especificar el orden de 
ejecución de los disparadores si varios están definidos para la misma acción.
El disparador se ejecuta para cada registro y no se puede activar una sola vez cuando se modifican varias filas (un 
concepto de disparador global como con otros SGBD). Tenga cuidado con las escrituras que modifican muchas filas 
en una tabla con un disparador: el riesgo de problemas de rendimiento es alto.
No es posible llamar a un procedimiento que devuelve registros al disparador.
No está permitido poner disparadores en las tablas del sistema del esquema mysql.
Los disparadores no son ejecutados por las acciones de claves externas.


