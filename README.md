# examen

# examen

## Consultas simples:

select NombreCliente, telefono from Clientes;
______________

select NombreCliente, concat(NombreContacto,ApellidoContacto) as Contacto from Clientes;

______________
Select CodigoPedido, CodigoProducto, Cantidad, PrecioUnidad, Cantidad*PrecioUnidad as totalProducto from DetallePedidos LIMIT 10;
______________

select Nombre, Apellido1 from Empleados where Puesto='Representante Ventas';
______________

## Consultas Avanzadas: 

### Estructura:

SELECT [DISTINCT] select_expr [,select_expr] ... [FROM tabla]
[WHERE filtro]
[GROUP BY expr [, expr].... ] [HAVING filtro_grupos]
[ORDER BY {nombre_columna I expr I posición} [ASC I DESC] , ... ]


#Consulta 1:
#Seleccionar los equipos de la nba cuyos jugadores #pesen de media más de 228 libras
SELECT Nombre_equipo,avg(peso) FROM jugadores GROUP BY Nombre_equipo HAVING avg(peso)>228 ORDER BY avg(peso);

______________

#seleccionar qué equipos de la nba tienen más de 1 jugador español
SELECT Nombre_equipo,count(*) FROM jugadores WHERE procedencia='Spain' GROUP BY Nombre_equipo HAVING count(*)>l;

______________

## Subconsultas:

SELECT nombre FROM jugadores WHERE Nombre_equipo IN (SELECT Nombre FROM equipos WHERE division='SouthWest');

______________

SELECT nombre FROM jugadores WHERE Nombre_equipo IN ('Hornets' ,'Spurs' ,'Rockets' ,'Mavericks' ,'Grizzlies');

______________

#subconsulta ejecutada #1 
SELECT Nombre FROM jugadores WHERE '76ers' = jugadores.Nombre_Equipo AND procedencia='Spain';


## Consultas multitabla:

### Estructura:

SELECT [DISTINCT] select_expr [,select_expr] ... [FROM referencias_tablas]
[WHERE filtro]
[GROUP BY expr [, expr] .... [HAVING filtro_grupos]
[ORDER BY {nombre_columnas I expr I posición} [ASC I DESC] , ... ]


SELECT Empleados.Nombre, COUNT(Pedidos.CodigoPedido) as NumeroOePedidos FROM Clientes, Pedidos, Empleados WHERE Clientes.CodigoCliente=Pedidos.CodigoCliente ANO 12 Empleados CodigoEmpleado = Clientes.CodigoEmpleadoRepVentas GROUP BY Empleados.Nombre ORDER BY NumeroOePedidos;


## INSERT: 
INSERT [INTO] nombre_tabla [(nombre_columna, ... )] VALUES ({expr I DEFAULT}, ... )

### Ejemplo:

#INSERT especificando la lista de columnas INSERT INTO mascotas (Codigo, Nombre, Raza) VALUES (1,'Pequitas','Gato Común Europeo')


## INSERT Y SELECT:
INSERT [INTO] nombre_tabla [(nombre_columna, ... )] SELECT ... FROM ...

### Ejemplo:

#Inserta en una tabla Backup todos los vehículos INSERT INTO BackupVehiculos SELECT * FROM vehiculos;


## UPDATE:

UPDATE nombre_tabla SET nombre_coll=exprl [, nombre_col2=expr2] ... [WHERE filtro]

### Ejemplo:

UPDATE jugadores SET Nombre_equipo='Knicks' WHERE Nombre='Pau Gasol';



## DELETE:

DELETE FROM nombre_tabla [WHERE filtro]

### Ejemplo:

DELETE FROM jugadores WHERE Nombre='Jorge Garbajosa';


## UPDATE y DELETE con subconsultas:

DELETE FROM Empleados WHERE CodigoEmpleado Not in (SELECT CodigoEmpleadoRepVentas FROM Clientes) AND Puesto='Representante Ventas';

DELETE FROM Clientes WHERE CodigoCliente in (SELECT CodigoCliente FROM Clientes WHERE LimiteCredito=O);


## Borrado y modificación de registros con relaciones:

definición_referencia:
REFERENCES nombre_tabla [(nombre_columna, ... )] [ON DELETE opción_referencia]
[ON UPDATE opción_referencia]
opción_referencia:
CASCADE I SET NULL I NO ACTION



## Procedimientos:
CREATE PROCEDURE sp_name ([parameter[,...]])
[characteristic ...] routine_body
CREATE FUNCTION sp_name ([parameter[,...]])
RETURNS type
[characteristic ...] routine_body
parameter:
[ IN | OUT | INOUT ] param_name type
type:
Any valid MySQL data type
characteristic:
LANGUAGE SQL
| [NOT] DETERMINISTIC
| { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
| SQL SECURITY { DEFINER | INVOKER }
| COMMENT 'string'


### Ejemplo:
mysql> delimiter //
mysql> CREATE PROCEDURE simpleproc (OUT param1 INT)
-> BEGIN
-> SELECT COUNT(*) INTO param1 FROM t;
-> END
-> //
Query OK, 0 rows affected (0.00 sec)
mysql> delimiter ;
mysql> CALL simpleproc(@a);

### Otro ejemplo:

mysql> delimiter //
mysql> CREATE PROCEDURE simpleproc (OUT param1 INT)
-> BEGIN
-> SELECT COUNT(*) INTO param1 FROM t;
-> END
-> //
Query OK, 0 rows affected (0.00 sec)
mysql> delimiter ;
mysql> CALL simpleproc(@a);


### Ejemplo grande:

`DELIMITER` $$ -- inicio
`DROP PROCEDURE IF EXISTS sp_productoPorCod$$` -- eliminamos si existe un procedimiento con el mismo nombre
`CREATE PROCEDURE sp_productoPorCod (IN cod INT)` -- creamos el procedimiento con un parámetro de entrada
`BEGIN` -- inicio cuerpo procedimiento almacenado
`DECLARE estadoOfert CHAR(2);` -- declaramos una variable local para almacenar el estado de Oferta.
/* Hacemos una consulta y el resultado lo almacenamos en la variable declarada*/
`SELECT oferta INTO estadoOfert FROM productos WHERE oferta = 'SI' AND codproducto = cod;`
`IF estadoOfert = 'SI' THEN` -- si está en oferta elegimos precio_oferta
`SELECT codproducto, nombreproduc, precio_oferta FROM productos WHERE codproducto = cod;`
`ELSE` -- sino el precio_normal
`SELECT codproducto, nombreproduc, precio_normal FROM productos WHERE codproducto = cod;`
`END IF;`
`END $$` -- fin de cuerpo del procedimiento almacenado
`DELIMITER ;` -- fin
`call sp_productoPorCod(2);` -- llamamos al procedimiento


## ALTER PROCEDURE y ALTER FUNCTION

ALTER {PROCEDURE | FUNCTION} sp_name [characteristic ...]
characteristic:
{ CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
| SQL SECURITY { DEFINER | INVOKER }
| COMMENT 'string'


## DROP PROCEDURE y DROP FUNCTION

DROP {PROCEDURE | FUNCTION} [IF EXISTS] sp_name

## SHOW CREATE PROCEDURE y SHOW CREATE FUNCTION

SHOW CREATE {PROCEDURE | FUNCTION} sp_nam

## SHOW PROCEDURE STATUS y SHOW FUNCTION STATUS

SHOW {PROCEDURE | FUNCTION} STATUS [LIKE 'pattern']



_______________________________________________________


#1. Hacer un procedimiento que reciba como parámetro un código de empleado y
#devuelva su nombre.

delimiter //
drop procedure if exists act1//
create procedure act1 (in cod1 int, out nomb1 varchar(50))
begin
	select nombre into nomb1 from empleados where CodigoEmpleado=cod1;
end
//

delimiter ;
call act1(2,@jesucristo);

select @jesucristo as nombre;
select * from empleados;

#2. Hacer un procedimiento que permita borrar un empleado cuyo número se pasará en la
#llamada.

delimiter //
drop procedure if exists act2//
create procedure act2 (cod2 int)
	begin
		delete from empleados where CodigoEmpleado=cod2;
    end
//
delimiter ;
start transaction;
call act2(6);
rollback;



#3. Hacer un procedimiento que modifique la localidad de un CLIENTE. El procedimiento
#recibirá como parámetros el número del empleado y la localidad nueva.



delimiter //

drop procedure if exists act3//
create procedure act3(in cod1 int,in loc1 varchar(50))
begin
    update clientes
    set LineaDireccion1=loc1
    where CodigoCliente=cod1;
end
//
delimiter ;
		#comprobaciones


start transaction;

call act3(1,'C/Rusia');


rollback;

#4. Crear un procedimiento que calcule la cantidad de productos que tenemos en la tabla
#productos, y guarde su valor en una variable local, y luego la visualice.

delimiter //

drop procedure if exists act4//
create procedure act4 ()
begin
	declare var4 int;
	select count(*) into var4 from productos;
    select var4;
end
//
delimiter ;

call act4();


#5.-Crear un procedimiento que visualice los productos que empiecen por una letra que se
#pasa desde la llamada. Uso call buscar_producto(‘f%’); ¿Qué pasaría si no pasamos él %?,
#¿Y cómo lo podríamos solucionar? Pista: usar función concat()

delimiter //

drop procedure if exists act5 //

create procedure act5 (in var5 varchar(70))
begin
 select nombre from productos where nombre like var5;
end
//
delimiter ;

call act5('f%');

# Si no ponemos F% no saldrian los que empiezan, sino solo los que se llaman f, se puede solucionar asi:

delimiter //

drop procedure if exists act5 //

create procedure act5 (in var5 varchar(70))
begin
 select nombre from productos where nombre like concat(var5,'%');
end
//
delimiter ;

call act5('h');



#6.-Modificar el procedimiento anterior, y llámalo con otro nombre para que también
#devuelva el total de productos encontrados, para ello usar un parámetro out.

delimiter //

drop procedure if exists act6 //

create procedure act6 (in var6 varchar(70), out total6 int)
begin
 select nombre from productos where nombre like concat(var6,'%');
 select count(*) into total6 from productos where nombre like concat(var6,'%');
end
//
delimiter ;

call act6('h',@total6);

select @total6;




#7.-Crear un procedimiento almacenado para que liste los clientes ordenados por
#apellidos y nombre. (No hay que pasar ningún parámetro).


delimiter //

drop procedure if exists act7//

create procedure act7 ()
begin
select * from clientes order by apellidocontacto, nombrecontacto;
end
//

delimiter ;

call act7 ();







