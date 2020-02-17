# Apuntes SQL

## Conceptos básicos SELECT

**1- IN** permite comprobar si el resultado está en una lista

**Ejemplo:** 

SELECT name, population FROM world</br>
  WHERE name IN ('Sweden', 'Norway', 'Denmark');

**2- BETWEEN** permite consultar un rango (límites incluídos)

**Ejemplo:**

SELECT name, area FROM world</br>
  WHERE area BETWEEN 200000 AND 250000;

**3- < y >** son usados para consultar valores menores y mayores (límites no incluídos)

**Ejemplo:**

SELECT name FROM world</br>
WHERE population > 200000000;

**4- LIKE** es usado para consultar si un nombre tiene incluída determinada palabra o letra. 

**Ejemplo:**

SELECT name FROM world</br>
WHERE name LIKE 'United%';

**5- OR** es usado para consultas donde se tiene que cumplir una u otra condición

**Ejemplo:**

SELECT name, population, area FROM world</br>
WHERE area > 3000000 OR population > 250000000;

**6- XOR** es usado igual que el OR pero excluyendo los resultados que cumplen ambas condiciones

**Ejemplo:**

SELECT name, population, area FROM world</br>
WHERE area > 3000000 XOR population > 250000000;

**7- ROUND** es usado para redondear un valor. El primer valor dentro del paréntesis es aquel que queremos redondear, el segundo el número de decimales que tendrá el resultado

**Ejemplo:**

SELECT name, ROUND(population/1000000,2), ROUND(gdp/1000000000,2) area FROM world</br>
WHERE continent = 'South America';

**8- LENGTH** es usado para la longitud de un string

**Ejemplo:**

SELECT name, capital</br>
  FROM world</br>
 WHERE LENGTH(name) LIKE LENGTH(capital);
 
 **9- LEFT** es usado para extraer los primeros carácteres de un string
 
 **10-<>** significa "no es igual"
 
**Ejemplo:**

SELECT name, capital</br>
FROM world</br>
WHERE LEFT(name,1) LIKE LEFT (capital,1) AND name <> capital;

**11-NOT LIKE** es justo el opuesto de LIKE, sirve para excluir resultados con una palabra o letra en ellos

**12-NOT IN** es el opuesto de IN, sirve para comprobar que el resultado no está en una lista

## SELECT dentro de un SELECT
**1- **El resultado de un **SELECT** puede usarse como valor para otra consulta, de esta forma podemos realizar consultas más complejas

**Ejemplo:**

SELECT name FROM world WHERE continent = </br>
(SELECT continent </br>
FROM world WHERE name = 'Brazil');

**2- ORDER BY** se usa para ordenar los resultados

**Ejemplo:**

SELECT name, continent</br>
FROM world</br>
WHERE continent IN (SELECT continent FROM world WHERE name IN ('Argentina', 'Australia'))</br>
ORDER by name;

**3- CONCAT** se usa para añadir un string a un resultado

**Ejemplo:**

SELECT name, CONCAT(ROUND(100*population/(SELECT population FROM world WHERE name = 'Germany'), 0), '%')</br>
FROM world</br>
WHERE continent = 'Europe';

**4-ALL** permite que >=, >, <, <= tengan efecto sobre una lista

**Ejemplo:**

SELECT name</br>
  FROM world</br>
 WHERE population >= ALL(SELECT population</br>
                           FROM world</br>
                          WHERE population>0);

## SUM y COUNT

**1- SUM** sirve para obtener el total de una consulta

**Ejemplo:**

SELECT SUM(population)</br>
FROM world;

**2- DISTINCT** se usa para evitar que haya filas duplicadas en una consulta

**Ejemplo:**

SELECT DISTINCT continent FROM world;

**3- COUNT** se usa para saber el número de elementos en una consulta

**Ejemplo:**

SELECT COUNT(area)</br>
FROM world</br>
WHERE area > 1000000;

**4- HAVING** sirve para determinar una condición que tienen los elementos

**Ejemplo:**

SELECT continent</br>
  FROM world</br>
 GROUP BY continent</br>
HAVING SUM(population)>100000000;

## JOIN
**1-JOIN** permite usar datos de más de una tabla en una misma consulta

**Ejemplo:**

SELECT player, teamid, stadium, mdate</br>
  FROM game JOIN goal ON (id=matchid)</br>
WHERE teamid = 'GER';

## Usando NULL
**1-NULL** es un valor que se da cuando es desconocido o inapropiado

**2-IS NULL** se usa para obtener los elementos cuyo valor es NULL. **IS NOT NULL** sirve para justo lo contrario

**Ejemplo:**

SELECT code, name FROM party</br>
  WHERE leader IS NULL

**3-INNER JOIN** sirve para dar todos los valores de dos tablas ignorando los NULL

**Ejemplo:**

SELECT teacher.name, dept.name</br>
 FROM teacher INNER JOIN dept</br>
           ON (teacher.dept=dept.id);

**4- LEFT JOIN** es usado para que los datos de la primera tabla de la unión muestre todos los valores aunque implique un null en los datos de la segunda

**Ejemplo:**

SELECT teacher.name, dept.name</br>
 FROM teacher LEFT JOIN dept</br>
           ON (teacher.dept=dept.id);

**5-RIGHT JOIN** funciona a la inversa que el **LEFT JOIN**

**Ejemplo:**

SELECT teacher.name, dept.name</br>
 FROM teacher RIGHT JOIN dept</br>
           ON (teacher.dept=dept.id);

**6-COALESCE** sirve para reemplazar NULL por cualquier otro valor (se especifica dentro del paréntesis, primero lo que se sustituye y segundo por lo que se sustituye)

**Ejemplo:**

SELECT name, party</br>
      ,COALESCE(party,'None') AS aff</br>
  FROM msp WHERE name LIKE 'C%';

**7-CASE** permite devolver diferentes valores dependiendo de las condiciones. Si ninguna condición se cumple y no hay ningún **ELSE**, devuelve NULL

**Ejemplo:**

SELECT name, population</br>
      ,CASE WHEN population<1000000 </br>
            THEN 'small'</br>
            WHEN population<10000000 </br>
            THEN 'medium'</br>
            ELSE 'large'</br>
       END</br>
  FROM bbc;
