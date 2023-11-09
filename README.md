# Consultas jardineria

1.4.5 Consultas multitabla (Composición interna)

Resuelva todas las consultas utilizando la sintaxis de SQL1 y SQL2. Las consultas con sintaxis de SQL2 se deben resolver con INNER JOIN y NATURAL JOIN.

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.
```sql
SELECT 
    c.nombre_cliente AS nombre_cliente,
    CONCAT(e.nombre, ' ', e.apellido1, ' ', e.apellido2) AS nombre_representante_ventas
FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado;
```

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.
```sql
SELECT DISTINCT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante FROM cliente c, empleado e, pago p
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND p.codigo_cliente = c.codigo_cliente;
```
3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.
```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```
4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
```sql
SELECT DISTINCT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante, ofi.ciudad FROM cliente c
JOIN pago p ON c.codigo_cliente = p.codigo_cliente
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina;
```
5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante, ofi.ciudad FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```
6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.
```sql
SELECT DISTINCT CONCAT(ofi.linea_direccion1 ,' ',ofi.linea_direccion2) FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
WHERE c.ciudad = 'Fuenlabrada';

arreglar mostrar 2 direcciones
```
7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante, ofi.ciudad as CiudadRepresentante FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina;
```
8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.
```sql
SELECT e1.nombre as Empleado, CONCAT(e2.nombre, ' ', e2.apellido1) as Jefe FROM empleado e1
LEFT JOIN empleado e2 ON e1.codigo_jefe = e2.codigo_empleado;

```
9. Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.
```sql
SELECT e1.nombre as Empleado, CONCAT(e2.nombre, ' ', e2.apellido1) as Jefe, CONCAT(e3.nombre, ' ', e3.apellido1) as JefeDelJefe FROM empleado e1
LEFT JOIN empleado e2 ON e1.codigo_jefe = e2.codigo_empleado
LEFT JOIN empleado e3 ON e2.codigo_jefe = e3.codigo_empleado;

```
10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.
```sql
SELECT DISTINCT c.nombre_cliente FROM cliente c
LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
WHERE p.fecha_entrega > p.fecha_esperada;

```
11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.
```sql
SELECT c.codigo_cliente, c.nombre_cliente, GROUP_CONCAT(DISTINCT pro.gama SEPARATOR ', ') AS gamas_producto 
FROM cliente c
JOIN pedido pe ON pe.codigo_cliente = c.codigo_cliente
JOIN detalle_pedido dp ON dp.codigo_pedido = pe.codigo_pedido
JOIN pago pag ON pag.codigo_cliente = c.codigo_cliente
JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
GROUP BY c.codigo_cliente
ORDER BY c.codigo_cliente;
```

# 1.4.6 Consultas multitabla (Composicion externa)

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.
```sql
SELECT DISTINCT c.nombre_cliente FROM cliente c
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```
2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.
```sql
SELECT DISTINCT c.nombre_cliente FROM cliente c
LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```
3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.
```sql
SELECT DISTINCT c.nombre_cliente FROM cliente c
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
WHERE p.codigo_cliente IS NULL AND pe.codigo_cliente IS NULL;
```
4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.
```sql
SELECT e.nombre FROM empleado e
LEFT JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
WHERE e.nombre IS NULL;
```
5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.
```sql
SELECT DISTINCT e.nombre as no_tiene_cliente FROM empleado e
LEFT JOIN cliente c ON c.codigo_empleado_rep_ventas = e.codigo_empleado;
```
6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la
oficina donde trabajan
```sql
SELECT e.nombre as no_tiene_cliente, ofi.* FROM empleado e
LEFT JOIN cliente c ON c.codigo_empleado_rep_ventas = e.codigo_empleado
LEFT JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
WHERE c.codigo_empleado_rep_ventas IS NULL;
```
7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado.
```sql
SELECT e.nombre FROM empleado e
LEFT JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
LEFT JOIN cliente c ON c.codigo_empleado_rep_ventas = e.codigo_empleado
WHERE e.codigo_oficina IS NULL AND c.codigo_empleado_rep_ventas IS NULL;
```
8. Devuelve un listado de los productos que nunca han aparecido en un pedido.
```sql
SELECT DISTINCT p.nombre FROM producto p
LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
LEFT JOIN pedido pe ON dp.codigo_pedido = pe.codigo_pedido
WHERE dp.codigo_producto IS NULL

```
9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto.
```sql

```
10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.
```sql

```
11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.
```sql

```
12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.
```sql

```