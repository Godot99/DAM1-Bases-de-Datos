# Apuntes SQL - Parte 2

## Definición SQL y subjlenguajes
**SQL** es un lenguaje declarativo diseñado para el manejo de bases de datos. Este está formado por 6 sublenguajes:
1. DQL
2. DDL
3. DML
4. TCL
5. DCL
6. SCL

###DQL (Declarative Query Language)
Es el sublenguaje usado a la hora de hacer las consultas, su principal comando es **SELECT**. Se encuentra explicado en la primera parte de estos apuntes realizada en el primer trimestre.

###DDL(Data Definition Language )
Se usa para crear, modificar o borrar tablas. Sus comandos principalos son **CREATE**, **ALTER**, **DROP**.

####CREATE
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

####ALTER
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
####DROP
Es usado para borrar schemas y tablas. Como se ve en el ejemplo anterior, se puede combinar con ALTER para borrar también columnas o constraints. Ejemplo:
```
DROP TABLE [IF EXISTS] table_name [CASCADE | RESTRICT];
```
Como puede verse, usa varias contraint para evitar el borrar algo que no debemos:
**IF EXISTS** evita que se intente borrar una tabla que no existe, lo cual causaría un error.
**CASCADE** si la tabla a borrar es usada en otras tablas, estas son borradas también.
**RESTRICT** evita que la tabla se borre si alguna otra depende de ella. PostgreSQL la usa por defecto.

###DML (Data Manipulation Language)
Este sublenguaje es usado para manipular los datos de las tablas. Sus principales comandos son **INSERT**, **UPDATE** y **DELETE**

####INSERT
Usado como su nombre indica para insertar datos en las tablas. Ejemplo:
```
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
```
Si quisieramos poner datos en todas las columnas, podemos omitir el especificar las columnas.

####UPDATE
Modifica los datos ya existentes en una tabla. Ejemplo:
```
UPDATE Customers
SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
WHERE CustomerID = 1;
```
En el ejemplo se actualiza tanto el nombre como la ciudad para el cliente con el ID 1.

####DELETE
Borra los datos de una tabla. Ejemplo:
```
DELETE FROM Customers WHERE CustomerName='Alfreds Schmidt';
```
En el ejemplo borra al cliente cuyo nombre es Alfreds Schmidt y con él, todos sus datos.
