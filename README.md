# examen

## Consultas simples:

select NombreCliente, telefono from Clientes;


select NombreCliente, concat(NombreContacto,ApellidoContacto) as Contacto from Clientes;


Select CodigoPedido, CodigoProducto, Cantidad, PrecioUnidad, Cantidad*PrecioUnidad as totalProducto from DetallePedidos LIMIT 10;


select Nombre, Apellido1 from Empleados where Puesto='Representante Ventas';


## Consultas Avanzadas: 


