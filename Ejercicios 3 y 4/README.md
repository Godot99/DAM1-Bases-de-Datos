# Creación BBDD usando MariaDB - Tarea 3
El método que usaremos para crear nuestras bases de datos es mediantes el uso de scripts por ser el más sencillo y fácil de usar.
Para ello, el primer paso es crear nuestros scripts. Lo que tenemos que hacer es copiar el código de nuestra base de datos en un editor
de texto y guardarlo como un archivo .sql. Para este ejercicio usaremos las soluciones dadas en clase.

**Nota:** Para que no nos de un error a la hora de usar el script, tenemos que adaptar el código a la sintaxis de MySQL, por ejemplo
no usar dominios ya que no los admite.

**Proxectos de investigación**
```
DROP SCHEMA IF EXISTS Proxectos_De_Investigacion;
CREATE SCHEMA Proxectos_De_Investigacion;
USE Proxectos_De_Investigacion;

CREATE TABLE Sede (
  Nome_Sede VARCHAR(30),
  Campus    VARCHAR(30)  NOT NULL,
  CONSTRAINT PK_Sede
    PRIMARY KEY (Nome_Sede)
);

CREATE TABLE Departamento (
  Nome_Departamento VARCHAR(30)  PRIMARY KEY,
  Teléfono          CHAR(9)      NOT NULL,
  Director          CHAR(9)
);

CREATE TABLE Ubicación (
  Nome_Sede         VARCHAR(30),
  Nome_Departamento VARCHAR(30),
  CONSTRAINT PK_Ubicación
    PRIMARY KEY (Nome_Sede, Nome_Departamento),
  CONSTRAINT FK_Sede
    FOREIGN KEY (Nome_Sede)
    REFERENCES Sede (Nome_Sede)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  CONSTRAINT FK_Departamento
    FOREIGN KEY (Nome_Departamento)
    REFERENCES Departamento (Nome_Departamento)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

CREATE TABLE Grupo (
  Nome_Grupo        VARCHAR(30),
  Nome_Departamento VARCHAR(30),
  Área              VARCHAR(30) NOT NULL,
  Lider             CHAR(9),
   PRIMARY KEY (Nome_Grupo, Nome_Departamento),
   FOREIGN KEY (Nome_Departamento) 
    REFERENCES Departamento (Nome_Departamento)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

CREATE TABLE Profesor (
  DNI            CHAR(9)    PRIMARY KEY,
  Nome_Profesor  VARCHAR(30) NOT NULL, 
  Titulación     VARCHAR(20) NOT NULL,
  Experiencia    Integer,
  N_Grupo        VARCHAR(30),
  N_Departamento VARCHAR(30),
  CONSTRAINT FK_Grupo_Profesor
    FOREIGN KEY      (N_Grupo,    N_Departamento)
    REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
    ON DELETE SET NULL
    ON UPDATE Cascade
);

CREATE TABLE Proxecto (
  Código_Proxecto CHAR(5)  PRIMARY KEY,
  Nome_Proxecto   VARCHAR(30)  NOT NULL,
  Orzamento       NUMERIC        NOT NULL,
  Data_Inicio     DATE         NOT NULL,
  Data_Fin        DATE,
  N_Gr            VARCHAR(30),
  N_Dep           VARCHAR(30),
  UNIQUE (Nome_Proxecto),
  CONSTRAINT Check_Dates
    CHECK (Data_Inicio < Data_Fin),
  CONSTRAINT FK_Proxecto_Grupo
    FOREIGN KEY (N_Gr, N_Dep)
    REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
    ON DELETE SET NULL
    ON UPDATE CASCADE
);

ALTER TABLE Departamento
  ADD CONSTRAINT FK_Profesor_Departamento
    FOREIGN KEY (Director)
    REFERENCES Profesor (DNI)
    ON DELETE SET NULL
    ON UPDATE CASCADE;

ALTER TABLE Grupo
  ADD CONSTRAINT FK_Profesor_Grupo
    FOREIGN KEY (Lider)
    REFERENCES Profesor (DNI)
    ON DELETE SET NULL
    ON UPDATE CASCADE;

CREATE TABLE Participa (
  DNI             CHAR(9),
  Código_Proxecto CHAR(5),
  Data_Inicio     DATE        NOT NULL,
  Data_Cese       DATE,
  Dedicación      INTEGER     NOT NULL,
  PRIMARY KEY (DNI, Código_Proxecto),
  CHECK (Data_Inicio < Data_Cese), 
  FOREIGN KEY (DNI)
    REFERENCES Profesor (DNI)
    ON DELETE NO ACTION
    ON UPDATE CASCADE,
  FOREIGN KEY (Código_Proxecto)
    REFERENCES Proxecto (Código_Proxecto)
    ON DELETE NO ACTION
    ON UPDATE CASCADE
);

CREATE TABLE Programa (
  Nome_Programa VARCHAR(30) PRIMARY KEY
); 

CREATE TABLE Financia (
  Nome_Programa        VARCHAR(30),
  Código_Proxecto      CHAR(5),
  Número_Programa      CHAR(5) NOT NULL,
  Cantidade_Financiada NUMERIC       NOT NULL,
  PRIMARY KEY (Nome_Programa, Código_Proxecto)
);

ALTER TABLE Financia
  ADD CONSTRAINT FK_Programa_Financia
  FOREIGN KEY (Nome_Programa)
    REFERENCES Programa(Nome_Programa)
    ON DELETE CASCADE
    ON UPDATE CASCADE;

ALTER TABLE Financia
  ADD CONSTRAINT FK_Proxecto_Financia
  FOREIGN KEY (Código_Proxecto)
    REFERENCES Proxecto (Código_Proxecto)
    ON DELETE CASCADE
    ON UPDATE CASCADE;
```

**Naves Espaciais**
```
DROP SCHEMA IF EXISTS Naves_Espaciais;
CREATE SCHEMA Naves_Espaciais;
USE Naves_Espaciais;

CREATE TABLE Servizo (
  Clave_Servizo CHAR(5),
  Nome_Servizo  VARCHAR(40),
  PRIMARY KEY (Clave_Servizo, Nome_Servizo)
);

CREATE TABLE Dependencia (
  Código_Dependencia CHAR(5),
  Nome_Dependencia   VARCHAR(40) NOT NULL,
  Función            VARCHAR(20),
  Localización       VARCHAR(20),
  Clave_Servizo      CHAR(5) NOT NULL,
  Nome_Servizo       VARCHAR(40) NOT NULL,
  PRIMARY KEY (Código_Dependencia),
  UNIQUE (Nome_Dependencia),
  FOREIGN KEY (Clave_Servizo, Nome_Servizo)
    REFERENCES Servizo (Clave_Servizo, Nome_Servizo)
    ON DELETE Cascade
    ON UPDATE Cascade
);

CREATE TABLE Cámara (
  Código_Dependencia CHAR(5),
  Categoría          VARCHAR(40) NOT NULL,
  Capacidade         INTEGER     NOT NULL,
  PRIMARY KEY (Código_Dependencia),
  FOREIGN KEY (Código_Dependencia)
    REFERENCES Dependencia (Código_Dependencia)
    ON DELETE Cascade
    ON UPDATE Cascade
);

CREATE TABLE Tripulación (
  Código_Tripulación CHAR(5) PRIMARY KEY,
  Nome_Tripulación   VARCHAR(40),
  Categoría          CHAR(20)    NOT NULL,
  Antigüidade        INTEGER     DEFAULT 0,
  Procedencia        CHAR(20),
  Adm                CHAR(20)    NOT NULL,
  Código_Dependencia CHAR(5) NOT NULL,
  Código_Cámara      CHAR(5) NOT NULL,
  FOREIGN KEY (Código_Cámara)
    REFERENCES Cámara (Código_Dependencia)
    ON UPDATE Cascade
    ON DELETE Cascade
);

ALTER TABLE Tripulación
  ADD FOREIGN KEY (Código_Dependencia)
    REFERENCES Dependencia (Código_Dependencia)
    ON UPDATE Cascade
    ON DELETE Cascade;

CREATE TABLE Planeta (
  Código_Planeta CHAR(5)          PRIMARY KEY,
  Nome_Planeta   VARCHAR(40) NOT NULL UNIQUE,
  Galaxia        CHAR(15)    NOT NULL,
  Coordenadas    CHAR(15)    NOT NULL,
  UNIQUE(Coordenadas)
 );

CREATE TABLE Visita (
  Código_Tripulación CHAR(5),
  Código_Planeta     CHAR(5),
  Data_Visita        DATE,
  Tempo              INTEGER      NOT NULL,
  PRIMARY KEY (Código_Tripulación, Código_Planeta, Data_Visita),
  FOREIGN KEY (Código_Tripulación)
    REFERENCES Tripulación (Código_Tripulación)
    ON UPDATE CASCADE
    ON DELETE CASCADE,
  FOREIGN KEY (Código_Planeta)
    REFERENCES Planeta (Código_Planeta)
    ON UPDATE CASCADE
    ON DELETE CASCADE 
);

CREATE TABLE Habita (
  Código_Planeta    CHAR(5),
  Nome_Raza         VARCHAR(40),
  Poboación_Parcial INTEGER     NOT NULL,
  PRIMARY KEY (Código_Planeta, Nome_Raza),
  FOREIGN KEY (Código_Planeta)
    REFERENCES Planeta (Código_Planeta)
    ON UPDATE Cascade
    ON DELETE Cascade
);

CREATE TABLE Raza (
  Nome_Raza       VARCHAR(40)  PRIMARY KEY,
  Altura          INTEGER      NOT NULL,  -- cm
  Anchura         INTEGER      NOT NULL,  -- cm
  Peso            INTEGER      NOT NULL,  -- g
  Poboación_Total INTEGER      NOT NULL
);

ALTER TABLE Habita
  ADD CONSTRAINT FK_Raza
    FOREIGN KEY (Nome_Raza)
      REFERENCES Raza (Nome_Raza)
      ON UPDATE CASCADE
      ON DELETE CASCADE;

ALTER TABLE Cámara
  ADD CONSTRAINT Capacidade_maior_de_cero
    CHECK (capacidade > 0);
```

Una vez creados nuestros scripts, los ejecutamos con el siguiente comando.
```
sudo mysql < <RutaDelArchivo.sql>
```
![imagen](https://github.com/Godot99/DAM1-Bases-de-Datos/blob/master/Imagenes/BD1.PNG)

# Comprobación de nuestras BBDD - Tarea 4
Para comprobar que nuestras bases de datos han sido creadas correctamente, primero debemos iniciar MariaDB.
```
sudo mysql -u root -p
```
![imagen](https://github.com/Godot99/DAM1-Bases-de-Datos/blob/master/Imagenes/BD2.PNG)

Una vez iniciado, para ver todas nuestras bases de datos creadas usamos este comando.
```
SHOW DATABASES
```
![imagen](https://github.com/Godot99/DAM1-Bases-de-Datos/blob/master/Imagenes/BD3.PNG)

Seleccionamos la base de datos que queremos ver.
```
USE <NombreDeLaBase>
```
![imagen](https://github.com/Godot99/DAM1-Bases-de-Datos/blob/master/Imagenes/BD4.PNG)

Para ver sus tablas usamos:
```
SHOW TABLES
```
![imagen](https://github.com/Godot99/DAM1-Bases-de-Datos/blob/master/Imagenes/BD5.PNG)

Finalmente, para ver las características de la tabla:
```
DESCRIBE <NombreDeLaTabla>
```
![imagen](https://github.com/Godot99/DAM1-Bases-de-Datos/blob/master/Imagenes/BD6.PNG)
