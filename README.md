# Consultas jardineria

## 1.4.5 Consultas multitabla (Composición interna)

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
SELECT DISTINCT c.* FROM cliente c
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```
2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.
```sql
SELECT DISTINCT c.* FROM cliente c
LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```
3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.
```sql
SELECT DISTINCT c.* FROM cliente c
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
SELECT DISTINCT p.* FROM producto p
LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
WHERE dp.codigo_producto IS NULL;
```
9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto.
```sql
SELECT DISTINCT p.nombre, p.descripcion, g.imagen FROM producto p
LEFT JOIN gama_producto g ON p.gama = g.gama
LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
WHERE dp.codigo_producto IS NULL;
```
10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.
```sql
    SELECT DISTINCT ofi.* from oficina ofi
    JOIN empleado e ON  ofi.codigo_oficina = e.codigo_oficina
    JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
    JOIN producto pr ON dp.codigo_producto = pr.codigo_producto
    WHERE pr.gama != 'Frutales';
```
11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.
```sql
SELECT DISTINCT c.* FROM cliente c
JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
LEFT JOIN pago pa ON c.codigo_cliente = pa.codigo_cliente
WHERE pa.codigo_cliente IS NULL;
```
12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.
```sql
SELECT e.*  FROM empleado e
LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
LEFT JOIN empleado p2 ON e.codigo_jefe = p2.codigo_empleado
WHERE c.codigo_empleado_rep_ventas IS NULL;
```

## 1.4.4 Consultas sobre una tabla

1. Devuelve un listado con el codigo de oficina y la ciudad donde hay oficinas
```sql
SELECT ofi.codigo_oficina, ofi.ciudad FROM oficina ofi;
```

2. Devuelve un listado con la ciudad y el telefono de las oficinas de españa
```sql
SELECT ofi.ciudad, ofi.telefono FROM oficina ofi
WHERE ofi.pais = "España";
```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un codigo de jefe igual a 7.
```sql
SELECT e.nombre, e.apellido1, e.apellido2, e.email FROM empleado e
WHERE e.codigo_jefe = 7;
```

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa
```sql
SELECT nombre, apellido1, apellido2, email FROM empleado
WHERE codigo_jefe IS NULL;
```
5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas
```sql
SELECT e.nombre, e.apellido1, apellido2, e.puesto FROM empleado e
WHERE e.puesto  != 'Representante Ventas';
```
6. Devuelve un listado con el nombre de los todos los clientes españoles.
```sql
SELECT c.nombre_cliente FROM cliente c;
```
7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.
```sql
SELECT distinct p.estado FROM pedido p;
```
8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:

- Utilizando la función YEAR de MySQL.
- Utilizando la función DATE_FORMAT de MySQL.
- Sin utilizar ninguna de las funciones anteriores.
```sql
SELECT c.codigo_cliente FROM cliente c
JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.fecha_pago BETWEEN '2008-01-01' AND '2008-12-30';
```
9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.
```sql
SELECT p.codigo_pedido, p.codigo_cliente, p.fecha_esperada, p.fecha_entrega FROM pedido p
WHERE p.fecha_entrega > p.fecha_esperada;
```
10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.

- Utilizando la función ADDDATE de MySQL.
- Utilizando la función DATEDIFF de MySQL.
- ¿Sería posible resolver esta consulta utilizando el operador de suma + o resta -?
```sql
--1:
SELECT p.codigo_pedido, p.codigo_cliente, p.fecha_esperada, p.fecha_entrega  FROM pedido p
WHERE fecha_entrega < ADDDATE(fecha_esperada, -2);

--2:
SELECT p.codigo_pedido, p.codigo_cliente, p.fecha_esperada, p.fecha_entrega  FROM pedido p
WHERE DATEDIFF(fecha_esperada, fecha_entrega) >=2;
```
--3
Se dice que no estaria bie hacerla de esa forma para esa forma MySQL ya nos da las funciones anteriormente utilizadas que son para el manejo de fechas de manera mas efectiva y evitar posibles problemas con el manejo de fechas 

pero si es posible hacer la consulta con los operadores logicos para hacer esta consulta se haria de esta manera
```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
WHERE fecha_entrega < fecha_esperada - INTERVAL 2 DAY;
```
usamos INTERVAL DAY para seleccionar los dias a seleccionar la fecha



11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.
```sql
SELECT p.* FROM pedido p
WHERE p.estado = 'Rechazado' AND p.fecha_entrega BETWEEN '2009.01-01' AND '2009-12-31';
```
12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año
```sql
SELECT p.* FROM pedido p WHERE MONTH(p.fecha_entrega) = 01;                                                                             
```
13. Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.
```sql
SELECT p.* FROM pago p WHERE YEAR(p.fecha_pago) = 2008 AND p.forma_pago = 'Paypal';
```
14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.
```sql
SELECT DISTINCT forma_pago FROM pago;
```
15. Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.
```sql
SELECT p.* FROM producto p
JOIN gama_producto gm ON p.gama = gm.gama
WHERE p.gama = 'Ornamentales' AND p.cantidad_en_stock <= 100;  
```
16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.
```sql
  SELECT c.* FROM cliente c
  WHERE c.ciudad = 'Madrid' AND (c.codigo_empleado_rep_ventas = 11 or c.codigo_empleado_rep_ventas = 30);
```
### 1.4.8 Subconsultas

#### 1.4.8.1 Con operadores básicos de comparación

1. Devuelve el nombre del cliente con mayor límite de crédito.
```sql
SELECT nombre_cliente, limite_credito 
FROM cliente  
WHERE limite_credito=(
    select max(limite_credito)from cliente
);
```
2. Devuelve el nombre del producto que tenga el precio de venta más caro.
```sql
SELECT nombre FROM producto 
WHERE precio_venta=(
    select max(precio_venta)from producto 
);
```
3. Devuelve el nombre del producto del que se han vendido más unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de unidades que se han vendido de cada producto a partir de los datos de la tabla `detalle_pedido`)
```sql
SELECT pr.nombre FROM producto pr
JOIN detalle_pedido dp ON pr.codigo_producto = dp.codigo_producto
WHERE dp.cantidad=(
    SELECT max(dp.cantidad) FROM detalle_pedido dp
);
```

4. Los clientes cuyo límite de crédito sea mayor que los pagos que haya realizado. (Sin utilizar `INNER JOIN`).
```sql
SELECT c.nombre_cliente FROM cliente c
WHERE c.limite_credito > (
    SELECT max(p.total) FROM pago p
    where c.codigo_cliente = p.codigo_cliente
);

```


5. Devuelve el producto que más unidades tiene en stock.
```sql
SELECT *
FROM producto
ORDER BY cantidad_en_stock DESC
LIMIT 1;

```
6. Devuelve el producto que menos unidades tiene en stock.
7. Devuelve el nombre, los apellidos y el email de los empleados que están a cargo de **Alberto Soria**.

#### 1.4.8.2 Subconsultas con ALL y ANY

1. Devuelve el nombre del cliente con mayor límite de crédito.
2. Devuelve el nombre del producto que tenga el precio de venta más caro.
3. Devuelve el producto que menos unidades tiene en stock.

#### 1.4.8.3 Subconsultas con IN y NOT IN

1. Devuelve el nombre, apellido1 y cargo de los empleados que no representen a ningún cliente.
2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.
3. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.
4. Devuelve un listado de los productos que nunca han aparecido en un pedido.
5. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.
6. Devuelve las oficinas donde **no trabajan** ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama `Frutales`.
7. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

#### 1.4.8.4 Subconsultas con EXISTS y NOT EXISTS

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.
2. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.
3. Devuelve un listado de los productos que nunca han aparecido en un pedido.
4. Devuelve un listado de los productos que han aparecido en un pedido alguna vez.

