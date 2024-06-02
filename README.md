# TAREA 11
##### EJERCICIOS PROPUESTOS DE CONSULTAS
**1**)¿Cuáles son los identificadores y nombres de todos los proyectos existentes en la empresa?
> 
SELECT IDProyecto,NombreProyecto
FROm Proyecto;

**2**)¿Cuáles son los proyectos que se desarrollan en 'CHICAGO'?
> 
SELECT IDProyecto, NombreProyecto
FROM Proyecto
WHERE Ubicacion = 'CHICAGO';

**3**)¿Cuáles son los proyectos que pertenecen al departamento con identificador 2?
> 
SELECT IDProyecto, NombreProyecto
FROM Proyecto
WHERE IDDepartamento = 2;

**4**)¿Cuáles son los nombres y ubicaciones de los proyectos junto con los nombres de sus departamentos asociados?
> 
SELECT e.NombreProyecto, e.ubicacion, d.IDDepartamento
FROM proyecto e
JOIN Departamento d ON e.IDDepartamento = d.IDDepartamento;

**5**)¿Qué empleados están asignados al proyecto identificado con el número 4, y cuáles son sus nombres?
> 
SELECT e.IDEmpleado, e.NombreEmpleado, p.IDProyecto
FROM Empleado e
JOIN EmpleadoProyecto pe ON e.IDEmpleado = pe.IDEmpleado
JOIN Proyecto p ON pe.IDProyecto = p.IDProyecto
WHERE p.IDProyecto = 4;

**6**)¿En qué proyectos está participando el empleado con el identificador 4, y cuáles son los nombres de esos proyectos?
> 
SELECT p.NombreProyecto
FROM EmpleadoProyecto ep
JOIN Proyecto P ON ep.IDProyecto = p.IDProyecto
WHERE ep.IDProyecto = 4;

**7**)¿Cuántas horas han trabajado en total los empleados en el proyecto con identificador 2?
> 
SELECT SUM(HorasTrabajadas) AS TotalHorasTrabajadas
FROM EmpleadoProyecto
WHERE IDEmpleado IN (
SELECT IDEmpleado
FROM EmpleadoProyecto
WHERE IDProyecto = 2);

**8**)¿Cuáles son los empleados que han trabajado más de 10 horas en el proyecto con identificador 2?
> 
SELECT e.IDEmpleado, e.NombreEmpleado
FROM Empleado e
JOIN (
SELECT IDEmpleado, SUM(HorasTrabajadas) AS HorasTrabajadas
FROM EmpleadoProyecto
WHERE IDProyecto = 2
GROUP BY IDEmpleado
HAVING SUM(HorasTrabajadas) > 10) AS t ON e.IDEmpleado = t.IDEmpleado;

**9**)¿Cuál es el total de horas trabajadas por cada empleado en todos los proyectos?
> 
SELECT e.IDEmpleado, e.NombreEmpleado, SUM(t.HorasTrabajadas) AS HorasTrabajadas
FROM Empleado e
JOIN EmpleadoProyecto t ON e.IDEmpleado = t.IDEmpleado
GROUP BY e.IDEmpleado, e.NombreEmpleado;

**10**)¿Cuáles son los empleados que trabajan en más de un proyecto?
> 
SELECT e.IDEmpleado, e.NombreEmpleado
FROM Empleado e
JOIN EmpleadoProyecto pe ON e.IDEmpleado = pe.IDEmpleado
GROUP BY e.IDEmpleado, e.NombreEmpleado
HAVING COUNT(DISTINCT pe.IDProyecto) > 1;

**11**)¿Cuáles son los empleados que han trabajado más de 30 horas en total en todos los proyectos?
> 
SELECT e.IDEmpleado, e.NombreEmpleado, SUM(ht.HorasTrabajadas) AS HorasTrabajadas
FROM Empleado e
JOIN EmpleadoProyecto ht ON e.IDEmpleado = ht.IDEmpleado
GROUP BY e.IDEmpleado, e.NombreEmpleado
HAVING SUM(ht.HorasTrabajadas) > 30;
**12**¿Cuál es el promedio de horas trabajadas por proyecto?
> 
SELECT AVG(ht.HorasTrabajadas) AS HorasTrabajadas
FROM EmpleadoProyecto ht
JOIN Proyecto p ON ht.IDProyecto = p.IDProyecto
GROUP BY p.IDProyecto;


### EJERCICOS PROPUESTOS AVANZADOS
**13**)¿Cuáles son los empleados que trabajan en proyectos ubicados en 'CHICAGO' y que tienen un salario (con o sin comisión) superior a 2000?
> 
SELECT e.IDEmpleado, e.NombreEmpleado, Ubicacion, e.Salario, e.Comision
FROM Empleado e
JOIN Empleado a ON e.IDEmpleado = a.IDEmpleado
JOIN Proyecto p ON a.IDEmpleado = p.IDProyecto
WHERE p.Ubicacion = 'CHICAGO' AND (e.Salario + COALESCE(e.Comision, 0)) > 2000;

**14**)¿Cuáles son los empleados que tienen un jefe, están asignados a más de un proyecto, y han trabajado más de 15 horas en total en todos los proyectos combinados?
> 
SELECT e.IDEmpleado, e.NombreEmpleado
FROM Empleado e
JOIN(
SELECT IDEmpleado
FROM EmpleadoProyecto
GROUP BY IDEmpleado
HAVING COUNT(DISTINCT IDProyecto) > 1
AND SUM(HorasTrabajadas) > 15)
pe ON e.IDEmpleado = pe.IDEmpleado
WHERE e.IDJefe IS NOT NULL;

**15**)¿Cuáles son los empleados que no reciben comisión y trabajan en departamentos ubicados en 'DALLAS' o 'NEW YORK'?
> 
SELECT E.IDEmpleado, E.NombreEmpleado, D.NombreDepartamento, D.Ubicacion
FROM Empleado E
JOIN Departamento D ON E.IDDepartamento = D.IDDepartamento
WHERE  E.Comision IS NULL AND (D.Ubicacion = 'NEW YORK' OR D.Ubicacion = 'DALLAS');
