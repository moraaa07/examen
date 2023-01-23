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











