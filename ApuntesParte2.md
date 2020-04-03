# Apuntes SQL

## Definición SQL y subjlenguajes
**SQL** es un lenguaje declarativo diseñado para el manejo de bases de datos. Este está formado por 6 sublenguajes:
1. DQL
2. DDL
3. DML
4. TCL
5. DCL
6. SCL

### DQL (Declarative Query Language)
Es el sublenguaje usado a la hora de hacer las consultas. Tiene una gran cantidad de comandos, pero el principal es **SELECT**.

#### Conceptos básicos SELECT

**1- IN** permite comprobar si el resultado está en una lista

**Ejemplo:** 

```
SELECT name, population FROM world
  WHERE name IN ('Sweden', 'Norway', 'Denmark');
 ```

**2- BETWEEN** permite consultar un rango (límites incluídos)

**Ejemplo:**

```
SELECT name, area FROM world
  WHERE area BETWEEN 200000 AND 250000;
  ```

**3- < y >** son usados para consultar valores menores y mayores (límites no incluídos)

**Ejemplo:**

```
SELECT name FROM world
WHERE population > 200000000;
```

**4- LIKE** es usado para consultar si un nombre tiene incluída determinada palabra o letra. 

**Ejemplo:**

```
SELECT name FROM world
WHERE name LIKE 'United%';
```

**5- OR** es usado para consultas donde se tiene que cumplir una u otra condición

**Ejemplo:**

```
SELECT name, population, area FROM world
WHERE area > 3000000 OR population > 250000000;
```

**6- XOR** es usado igual que el OR pero excluyendo los resultados que cumplen ambas condiciones

**Ejemplo:**

```
SELECT name, population, area FROM world
WHERE area > 3000000 XOR population > 250000000;
```

**7- ROUND** es usado para redondear un valor. El primer valor dentro del paréntesis es aquel que queremos redondear, el segundo el número de decimales que tendrá el resultado

**Ejemplo:**

```
SELECT name, ROUND(population/1000000,2), ROUND(gdp/1000000000,2) area FROM world
WHERE continent = 'South America';
```

**8- LENGTH** es usado para la longitud de un string

**Ejemplo:**

```
SELECT name, capital
  FROM world
 WHERE LENGTH(name) LIKE LENGTH(capital);
 ```
 
 **9- LEFT** es usado para extraer los primeros carácteres de un string
 
 **10-<>** significa "no es igual"
 
**Ejemplo:**

```
SELECT name, capital
FROM world
WHERE LEFT(name,1) LIKE LEFT (capital,1) AND name <> capital;
```

**11-NOT LIKE** es justo el opuesto de LIKE, sirve para excluir resultados con una palabra o letra en ellos

**12-NOT IN** es el opuesto de IN, sirve para comprobar que el resultado no está en una lista

#### SELECT dentro de un SELECT
**1-** El resultado de un **SELECT** puede usarse como valor para otra consulta, de esta forma podemos realizar consultas más complejas

**Ejemplo:**

```
SELECT name FROM world WHERE continent = 
(SELECT continent
FROM world WHERE name = 'Brazil');
```

**2- ORDER BY** se usa para ordenar los resultados

**Ejemplo:**

```
SELECT name, continent
FROM world
WHERE continent IN (SELECT continent FROM world WHERE name IN ('Argentina', 'Australia'))
ORDER by name;
```

**3- CONCAT** se usa para añadir un string a un resultado

**Ejemplo:**

```
SELECT name, CONCAT(ROUND(100*population/(SELECT population FROM world WHERE name = 'Germany'), 0), '%')
FROM world
WHERE continent = 'Europe';
```

**4-ALL** permite que >=, >, <, <= tengan efecto sobre una lista

**Ejemplo:**

```
SELECT name
  FROM world
 WHERE population >= ALL(SELECT population
                           FROM world
                          WHERE population>0);
```

#### SUM y COUNT

**1- SUM** sirve para obtener el total de una consulta

**Ejemplo:**

```
SELECT SUM(population)
FROM world;
```

**2- DISTINCT** se usa para evitar que haya filas duplicadas en una consulta

**Ejemplo:**

```
SELECT DISTINCT continent FROM world;
```

**3- COUNT** se usa para saber el número de elementos en una consulta

**Ejemplo:**

```
SELECT COUNT(area)
FROM world
WHERE area > 1000000;
```

**4- HAVING** sirve para determinar una condición que tienen los elementos

**Ejemplo:**

```
SELECT continent
  FROM world
 GROUP BY continent
HAVING SUM(population)>100000000;
```

#### JOIN
**1-JOIN** permite usar datos de más de una tabla en una misma consulta

**Ejemplo:**

```
SELECT player, teamid, stadium, mdate
  FROM game JOIN goal ON (id=matchid)
WHERE teamid = 'GER';
```

#### Usando NULL
**1-NULL** es un valor que se da cuando es desconocido o inapropiado

**2-IS NULL** se usa para obtener los elementos cuyo valor es NULL. **IS NOT NULL** sirve para justo lo contrario

**Ejemplo:**

```
SELECT code, name FROM party
  WHERE leader IS NULL
 ```

**3-INNER JOIN** sirve para dar todos los valores de dos tablas ignorando los NULL

**Ejemplo:**

```
SELECT teacher.name, dept.name
 FROM teacher INNER JOIN dept
           ON (teacher.dept=dept.id);
 ```

**4- LEFT JOIN** es usado para que los datos de la primera tabla de la unión muestre todos los valores aunque implique un null en los datos de la segunda

**Ejemplo:**

```
SELECT teacher.name, dept.name
 FROM teacher LEFT JOIN dept
           ON (teacher.dept=dept.id);
```

**5-RIGHT JOIN** funciona a la inversa que el **LEFT JOIN**

**Ejemplo:**

```
SELECT teacher.name, dept.name
 FROM teacher RIGHT JOIN dept
           ON (teacher.dept=dept.id);
```

**6-COALESCE** sirve para reemplazar NULL por cualquier otro valor (se especifica dentro del paréntesis, primero lo que se sustituye y segundo por lo que se sustituye)

**Ejemplo:**

```
SELECT name, party
      ,COALESCE(party,'None') AS aff
  FROM msp WHERE name LIKE 'C%';
 ```

**7-CASE** permite devolver diferentes valores dependiendo de las condiciones. Si ninguna condición se cumple y no hay ningún **ELSE**, devuelve NULL

**Ejemplo:**

```
SELECT name, population
      ,CASE WHEN population<1000000
            THEN 'small'
            WHEN population<10000000
            THEN 'medium'
            ELSE 'large'
       END
  FROM bbc;
```

### DDL(Data Definition Language )
Se usa para crear, modificar o borrar tablas. Sus comandos principalos son **CREATE**, **ALTER**, **DROP**.

#### CREATE
Este comando se usa para crear schemas, dominios, tablas, etc. La mejor manera de verlo es en un ejemplo:

```
CREATE SCHEMA Investigacion;

CREATE DOMAIN Tipo_Codigo CHAR(5);
CREATE DOMAIN Tipo_DNI    CHAR(9);
CREATE DOMAIN Nome_Valido VARCHAR(30);

--CREATE TABLE Investigacion.Sede();
CREATE TABLE Sede(
    Nome_Sede Nome_Valido,
    Campus Nome_Valido NOT NULL
    CONSTRAINT PK_Sede
        PRIMARY KEY (Nome_Sede)
);
 ```
En el ejemplo también podemos ver el uso de "restricciones" o **CONSTRAINT** para limitar el tipo de dato a usar. Algunos de estos son **NOT NULL** o **PRIMARY KEY** entre muchos otros.

#### ALTER
Es usado para modificar tablas ya creadas. Su función principal es la de añadir o borrar columnas y constraints, aunque tiene otros usos. Ejemplo:
```
CREATE TABLE Profesor (
    DNI Tipo_DNI PRIMARY KEY,
    Nome_Profesor Nome_Valido NOT NULL,
    Titutalicion VARCHAR(20) NOT NULL,
    Experiencia Integer,
    N_Grupo Nome_Valido
    N_Departamento Nome_Valido
    CONSTRAINT FK_Grupo_Profesor
        FOREIGN KEY (N_Grupo, N_Departamento)
        REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
        ON DELETE SET NULL
        ON UPDATE CASCADE
)

--A FK de Profesor esta mal. Facer unha nova con B:N M:R
ALTER TABLE Profesor
DROP CONSTRAINT FK_GrupoProfesor;

ALTER TABLE Profesor
    ADD CONSTRAINT FK_Grupo_Profesor
        FOREIGN KEY (N_Grupo, N_Departamento)
        REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
        ON DELETE SET NULL
        ON UPDATE NO ACTION;
```
#### DROP
Es usado para borrar schemas y tablas. Como se ve en el ejemplo anterior, se puede combinar con ALTER para borrar también columnas o constraints. Ejemplo:
```
DROP TABLE [IF EXISTS] table_name [CASCADE | RESTRICT];
```
Como puede verse, usa varias contraint para evitar el borrar algo que no debemos:
**IF EXISTS** evita que se intente borrar una tabla que no existe, lo cual causaría un error.
**CASCADE** si la tabla a borrar es usada en otras tablas, estas son borradas también.
**RESTRICT** evita que la tabla se borre si alguna otra depende de ella. PostgreSQL la usa por defecto.

### DML (Data Manipulation Language)
Este sublenguaje es usado para manipular los datos de las tablas. Sus principales comandos son **INSERT**, **UPDATE** y **DELETE**

#### INSERT
Usado como su nombre indica para insertar datos en las tablas. Ejemplo:
```
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
```
Si quisieramos poner datos en todas las columnas, podemos omitir el especificar las columnas.

#### UPDATE
Modifica los datos ya existentes en una tabla. Ejemplo:
```
UPDATE Customers
SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
WHERE CustomerID = 1;
```
En el ejemplo se actualiza tanto el nombre como la ciudad para el cliente con el ID 1.

#### DELETE
Borra los datos de una tabla. Ejemplo:
```
DELETE FROM Customers WHERE CustomerName='Alfreds Schmidt';
```
En el ejemplo borra al cliente cuyo nombre es Alfreds Schmidt y con él, todos sus datos.
