# PostgreSQL con Docker

# Creacion del Contenedor
```bash
docker run -d --name postgres_container -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=estudiar -p 5433:5432 -v pgdata:/var/lib/postgresql/data --restart=unless-stopped postgres:15
```

## Conectar al contenedor de docker
```bash
docker exec -it postgres_container bash
```

## Conectar con PostgreSQL bajo Consola
```bash
psql --host=localhost --username=admin -d estudiar --password

psql -h localhost -U admin -d estudiar -W
```

# Tablas
### Tipos de datos

- 1. Productos
```SQL
CREATE TABLE productos(
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(70),
    precio NUMERIC,
    presente BOOLEAN,
    fecha_implementacion DATE,
    fecha_compra TIMESTAMP,
    datos jsonb
);
--> el id es tipo de dato sieral es autoincrement asi que no es necesario  
--> haserle el insert 
INSERT INTO productos(nombre,precio,presente,fecha_implementacion,fecha_compra,datos)VALUES
('a',10.00,true,'2025-08-01','2024-07-09 14:30:00','{"key": "value"}'),
('b',15.00,false,'2023-08-01','2025-07-09 12:30:00','{"key": "value"}');

SELECT * FROM productos;
```

### Constraints

- 2. Estudiantes
```SQL
CREATE TABLE estudiantes(
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    edad INTEGER CHECK (edad > 0),
    correo VARCHAR(100) UNIQUE
);

INSERT INTO estudiantes(nombre,edad,correo)VALUES
('Nicolas Muskus Tarazona',17,'nicolasmuskus1@gmail.com'),
('Danilo Muskus Tarazona',17,'danilomuskus1@gmail.com');
--> Estudiante con edad -1 debe fallar
INSERT INTO estudiantes(nombre,edad,correo)VALUES
('a',-1,'a@gmail.com');
--> Estudiante con correo repetido debe fallar 
INSERT INTO estudiantes(nombre,edad,correo)VALUES
('Nicolas del futuro',1000,'nicolasmuskus1@gmail.com');
```
### Relaciones

- 3. Pais, Region, Ciudad
```SQL
DROP TABLE IF EXISTS Ciudad;
DROP TABLE IF EXISTS Region;
DROP TABLE IF EXISTS Pais;

CREATE TABLE Pais(
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(150) UNIQUE NOT NULL
);

CREATE TABLE Region(
    id_region SERIAL NOT NULL PRIMARY KEY,
    id_pais INT NOT NULL,
    nombre VARCHAR(150) UNIQUE NOT NULL,
    FOREIGN KEY (id_pais)REFERENCES Pais(id) ON DELETE CASCADE
);

CREATE TABLE Ciudad(
    id_ciudad SERIAL NOT NULL,
    id_region INT NOT NULL,
    nombre VARCHAR(150) UNIQUE NOT NULL,
    PRIMARY KEY(id_ciudad),
    FOREIGN KEY (id_region)REFERENCES Region(id_region) ON DELETE CASCADE
);

INSERT INTO Pais(nombre)VALUES
('Colombia');
INSERT INTO Region(id_region,id_pais,nombre)VALUES
(1,1,'Santander');
INSERT INTO Ciudad(id_ciudad,id_region,nombre)VALUES
(1,1,'Bucaramanga');
INSERT INTO Ciudad(id_ciudad,id_region,nombre)VALUES
(2,1,'Giron');
INSERT INTO Ciudad(id_ciudad,id_region,nombre)VALUES
(3,1,'Floridablanca');


--> Indices 

CREATE INDEX idx_pais ON Pais(nombre);
CREATE INDEX idx_ciudad ON Ciudad(nombre);
CREATE INDEX idx_region ON Region(nombre);

```

### Funciones con Fecha
```SQL
SELECT CURRENT_DATE; --> FECHA DE HOY
SELECT NOW(); --> FECHA Y HORA EXACTA DEL MOMENTO
SELECT AGE(DATE '2008-02-15'); --> SACAR SU EDAD
SELECT EXTRACT(YEAR FROM NOW()); --> EXTRAER EL AÑO

```

### Agregación y Group By
```SQL
ALTER TABLE Ciudad ADD COLUMN poblacion INT; 
UPDATE Ciudad SET poblacion = 600000 WHERE nombre = 'Bucaramanga';
UPDATE Ciudad SET poblacion = 1500000 WHERE nombre = 'Giron';
--> El BETWEEN SE USA EN WHERE NO EN SET
UPDATE Ciudad SET poblacion = BETWEEN 4500 AND 131212 WHERE nombre = 'Floridablanca';


SELECT COUNT(*) FROM Ciudad;
SELECT MAX(poblacion) FROM Ciudad;
SELECT MIN(poblacion) FROM Ciudad;
SELECT AVG(poblacion) FROM Ciudad;

SELECT Ciudad, COUNT(*) AS total_ciudad,
       MAX(poblacion) AS poblacionMaxima,
       MIN(poblacion) AS poblacionMinima,
       ROUND(AVG(poblacion), 1) AS poblacionPromedio
FROM Ciudad
GROUP BY Ciudad;

```

### Distinct y Between
```SQL
SELECT DISTINCT nombre FROM Pais;
SELECT DISTINCT nombre FROM Ciudad;

---> ESTA MAL el WHERE siempre va después de FROM.
SELECT COUNT(*) WHERE poblacion BETWEEN 700000 AND 2000000 FROM Ciudad;
---> forma correcta 
SELECT nombre AS ciudad, poblacion
FROM Ciudad
WHERE poblacion BETWEEN 700000 AND 2000000;


CREATE OR REPLACE PROCEDURE suma_poblacion_pais(p_id_pais INT)
LANGUAGE plpgsql
AS $$
DECLARE
    total_poblacion BIGINT; -- Variable para guardar la suma de la población
BEGIN
    SELECT SUM(Ciudad.poblacion)
    INTO total_poblacion
    FROM Ciudad
    JOIN Region ON Ciudad.id_region = Region.id_region
    JOIN Pais ON Region.id_pais = Pais.id
    WHERE Pais.id = p_id_pais;

    -- Mostrar resultado en consola
    RAISE NOTICE 'La población total del país con id % es: %', p_id_pais, COALESCE(total_poblacion,0);
END;
$$;

SELECT 
    Ciudad.nombre AS ciudad,
    Region.nombre AS Region,
    Pais.nombre AS Pais --> Aqui no tiene que estar ninguna coma
FROM Ciudad --> La ultima tabla que conecta con la principal
JOIN Region ON Ciudad.id_region = Region.id_region --> tabla intermediara
JOIN Pais ON Region.id_pais = Pais.id;  --> tabla principal

SELECT 
    Ciudad.nombre AS ciudad,
    Region.nombre AS Region
FROM Ciudad --> La ultima tabla que conecta con la principal
JOIN Region ON Ciudad.id_region = Region.id_region; --> tabla intermediara
```