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
