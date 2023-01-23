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

Estructura:
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

Estructura:

SELECT [DISTINCT] select_expr [,select_expr] ... [FROM referencias_tablas]
[WHERE filtro]
[GROUP BY expr [, expr] .... [HAVING filtro_grupos]
[ORDER BY {nombre_columnas I expr I posición} [ASC I DESC] , ... ]


SELECT Empleados.Nombre, COUNT(Pedidos.CodigoPedido) as NumeroOePedidos FROM Clientes, Pedidos, Empleados WHERE Clientes.CodigoCliente=Pedidos.CodigoCliente ANO 12 Empleados CodigoEmpleado = Clientes.CodigoEmpleadoRepVentas GROUP BY Empleados.Nombre ORDER BY NumeroOePedidos;




