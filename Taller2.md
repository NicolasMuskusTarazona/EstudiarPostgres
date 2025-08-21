# PostgreSQL con Docker

# Creacion del Contenedor
```bash
docker run -d --name postgres_container -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=empresa -p 5433:5432 -v pgdata:/var/lib/postgresql/data --restart=unless-stopped postgres:15
```

## Conectar al contenedor de docker
```bash
docker exec -it postgres_container bash
```

## Conectar con PostgreSQL bajo Consola
```bash
psql --host=localhost --username=admin -d empresa --password

psql -h localhost -U admin -d empresa -W
```

## Tablas

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
    poblacion INT,
    PRIMARY KEY(id_ciudad),
    FOREIGN KEY (id_region)REFERENCES Region(id_region) ON DELETE CASCADE
);

-- Paises
INSERT INTO Pais(nombre)VALUES
('Colombia');
INSERT INTO Pais(nombre)VALUES
('Mexico');

-- Regiones

-- Colombia
INSERT INTO Region(id_pais,nombre)VALUES
(1,'Santander');
INSERT INTO Region(id_pais,nombre)VALUES
(1,'Cundinamarca');

-- Mexico
INSERT INTO Region(id_pais,nombre)VALUES
(2,'Jalisco');
INSERT INTO Region(id_pais,nombre)VALUES
(2,'CDMX');

-- Ciudades

-- Colombia
INSERT INTO Ciudad(id_region,nombre,poblacion)VALUES
(1,'Bucaramanga',600000);
INSERT INTO Ciudad(id_region,nombre,poblacion)VALUES
(2,'Bogotá',8000000);

--Mexico
INSERT INTO Ciudad(id_region,nombre,poblacion)VALUES
(3,'Guadalajara',1500000);
INSERT INTO Ciudad(id_region,nombre,poblacion)VALUES
(4,'Ciudad de México',9000000);


-- Indices
CREATE INDEX idx_pais ON Pais(nombre);
CREATE INDEX idx_ciudad ON Ciudad(nombre);
CREATE INDEX idx_region ON Region(nombre);
```


## Consultas básicas

- 1. Muestra todas las ciudades con su región y país.
```SQL
SELECT 
    Ciudad.nombre AS ciudad,
    Region.nombre AS Region,
    Pais.nombre AS Pais
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais ON Region.id_pais = Pais.id;
```

- 2. Muestra solo las ciudades de Colombia.
```SQL
-- TENGO QUE HACER LA RELACION DE TODAS MANERAS PERO UNICAMENTE MOSTRAS LOS 2 VALORES
SELECT 
    Ciudad.nombre AS ciudad,
    Pais.nombre AS pais
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais ON Region.id_pais = Pais.id
WHERE Pais.id = 1;
```
- 3. Cuenta cuántas ciudades tiene cada país.
```SQL
SELECT 
    Pais.nombre AS pais,
    COUNT(Ciudad.id_ciudad) AS total_ciudades
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais ON Region.id_pais = Pais.id
GROUP BY Pais.nombre
ORDER BY total_ciudades DESC;
```

## Consultas con condiciones

- 1. Muestra las ciudades con población entre 1 millón y 9 millones.
```SQL
SELECT 
    Ciudad.nombre AS ciudad,
    Pais.nombre AS pais,
    Ciudad.poblacion AS Poblacion
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais ON Region.id_pais = Pais.id
WHERE Ciudad.poblacion BETWEEN 1000000 AND 9000000;
```
- 2. Muestra la ciudad más poblada de cada país.
```SQL
SELECT 
    MAX(Ciudad.poblacion) AS ciudad_mas_poblada,
    Pais.nombre AS pais
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais ON Region.id_pais = Pais.id
GROUP BY Pais.nombre;
```

- 3. Calcula el promedio de población por país.
```SQL
SELECT 
    AVG(Ciudad.poblacion) AS ciudad_mas_poblada,
    Pais.nombre AS pais
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais ON Region.id_pais = Pais.id
GROUP BY Pais.nombre;
```


## Vista 
Es basicamente una consulta guardada como si fuera una tabla.
```SQL
CREATE OR REPLACE VIEW vista_ciudades AS
SELECT 
    Ciudad.nombre   AS ciudad,
    Region.nombre   AS region,
    Pais.nombre     AS pais,
    Ciudad.poblacion AS poblacion
FROM Ciudad
JOIN Region ON Ciudad.id_region = Region.id_region
JOIN Pais   ON Region.id_pais   = Pais.id;
```
# usarla
```SQL
SELECT * FROM vista_ciudades;
```