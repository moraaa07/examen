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

/*1. Crear un trigger de nombre, nombrealumn@_validar, 
para que se dispare antes de un evento
de insercion en
la tabla persona. */ 

/* a) Prepararlo para que el nuevo valor del campo nombre, se convierta a 
mayúsculas (usar la función UPPER()). 

b) También, que valide el nuevo valor del atributo edad, donde si se 
inserta un O se asigne un NULL a la nueva edad, y en caso contrario la edad 
normal a insertar. */

/* C) A continuación, para tener un registro de las transacciones, 
aplicar una inserción en la tabla nuevosDatos con los valores nuevos 
de los campos código, current date, y valor i (para indicar que es una inserción) */

DELIMITER //

use `examentrigger`//

create definer = current_user trigger `examentrigger`.`persona_before_insert` 
before insert on `persona` for each row 

begin

set new.nombre = upper(new.nombre);

    if new.edad = 0 then

		set new.edad = null;

	end if;

insert into nuevosDatos (codigo, cuando, tipo) values (new.codigo, current_date(), 'i');


end //

DELIMITER ;

/*2. Crear un trigger, de nombre alumno borrado, para que antes de cada borrado, 
se anote en la tabla de "borrados" (como si fuese una copia de seguridad) 
los datos que se van a borrar de la tabla persona*/

delimiter //

create trigger mora_delete before delete on persona for each row
begin 

insert into borrados (codigo, nombre, edad, fecha) values (old.codigo, old.nombre, old.edad, current_date());

end // 

delimiter ;

delete from persona where codigo = '1';

delete from persona where codigo = 'p';

delimiter //

/*3. Crear un trigger, de nombre alumno_actualiza, que antes de actualizar 
en la tabla persona, inserte en la tabla nuevosDatos el código nuevo, 
la fecha del sistema y el tipo A, para indicar que se ha hecho una actualización.*/

create trigger mora_update before update on persona for each row
begin 
insert into nuevosDatos (codigo, cuando, tipo) values (old.codigo, current_date(),'A');

end // 

delimiter ;

_________________________________________

PRÁCTICA USUARIOS Y PRIVILEGIOS MYSQL
1. Ejecuta MySQL Workbench y activar la base de datos 'Jardinería'. En caso de que no dispongas
de ella, puedes generarla con el script SQL que hay en el moodle. Abrir una nueva ventana de
consulta y realizar las siguientes acciones mediante órdenes SQL.
Crear new query window
2. Crear un usuario llamado “alumno” que tenga acceso a las tablas CLIENTES, EMPLEADOS,
PRODUCTOS desde cualquier lugar.
CREATE USER alumno IDENTIFIED BY ‘alum’;
GRANT SELECT ON jardineria.empleados TO alumno;
GRANT SELECT ON jardineria.clientes TO alumno;
GRANT SELECT ON jardineria.productos TO alumno;
3. Crear un usuario llamado “profesor” que tenga permiso de lectura a toda la base de datos
desde localhost.
CREATE USER profesor@localhost IDENTIFIED BY ‘profe’;
GRANT SELECT ON jardineria.* TO profesor@localhost;
4. Comprueba la creación y las características de estos nuevos usuarios consultando la tabla
mysql.user, db y tables_priv
SELECT * FROM mysql.user;
SELECT * FROM mysql.db;
SELECT * FROM mysql.tables_priv;
5. Establecer nuevas conexiones a la instancia de BD con los usuarios 'alumno' y 'profesor' y
comprobar sus niveles de acceso respectivo sobre las diferentes tablas de la BD 'jardineria'.
Comprobaciomes desde SQL Workbench y consultas SQL
6. Modifica la contraseña de 'alumno'. Comprobar la conexión con la nueva contraseña.
UPDATE mysql.user SET password=PASSWORD(‘nuevaclave’) WHERE user=’alumno’;
(versions anteriores a 5.7)
ALTER USER ‘alumno’ IDENTIFIED BY ‘clavenueva’;
7. Modifica el host desde donde pueda acceder el alumno a una dirección IP determinada (del
propio servidor o un cliente). Soluciona los problemas que pueda presentar esta modificación.
UPDATE mysql.user SET host=’192.168.1.100’ WHERE user=’alumno’;
Modificar preferencias Workbench para que sea posible la actualización.
8. Comprueba que el usuario alumno puede conectar sólo desde la máquina con la IP asignada.
Comprobación de conexión desde el Workbench
9. Vuelve a la ventana de root y concede derechos de consulta al usuario alumno sobre la tabla
PEDIDOS. Comprueba, mediante una nueva conexión del usuario 'alumno' que tiene este
nuevo acceso.
GRANT SELECT ON jardineria.pedidos TO alumno@192.168.1.100;
10. Crear un usuario llamado “profesor_cli” con los privilegios de 'profesor' y, también, los
privilegios de inserción y borrado en la tabla CLIENTES.
CREATE USER professor_cli IDENTIFIED BY ‘profcli’;
GRANT SELECT ON jardineria.* to professor_cli;
GRANT INSERT, DELETE ON jardineria.clientes to professor_cli;
11. Conecta como 'profesor_cli' y comprueba sus privilegios sobre las diferentes tablas de
'jardineria'.
Conectar en Workbench, comprobar y realizar consultas SQL.
12. Crear un usuario llamado “administrador” que tenga todos los privilegios a todas las bases de
datos de nuestro servidor mysql. Este administrador no tendrá la posibilidad de dar
privilegios.
GRANT ALL PRIVILEGES ON *.* to administrador IDENTIFIED BY ‘admin’;
13. Crear un usuario llamado “superadmin” con los privilegios anteriores y con posibilidad de
conceder privilegios a otros usuarios.
GRANT ALL PRIVILEGES ON *.* to superadmin IDENTIFIED BY ‘supadmin’ WITH GRANT OPTION;
14. Quitar los privilegios al usuario “profesor_cli” para actualizar la tabla CLIENTES.
REVOKE INSERT, DELETE ON jardineria.clientes FROM professor_cli;
15. Eliminar todos los privilegios al usuario 'profesor'.
REVOKE SELECT ON jardineria.* FROM profesor@localhost;
16. Actualizar los privilegios.
FLUSH PRIVILEGES;
17. Muestra los privilegios de usuario 'profesor'.
SHOW GRANTS FOR profesor@localhost;
18. Consulta todos los usuarios existentes.
SELECT * FROM mysql.user;
