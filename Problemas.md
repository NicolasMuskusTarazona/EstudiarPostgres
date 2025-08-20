# 📘 Actividad de Estudio – PostgreSQL

Este documento reúne los retos y preguntas de estudio para practicar conceptos clave de **PostgreSQL**, incluyendo tipos de datos, constraints, relaciones, funciones de fecha, agregación, DISTINCT, procedimientos almacenados y más.

---

## 🧩 1. Tipos de Datos

### 👉 Reto
Crea una tabla llamada `productos` con al menos 6 columnas usando diferentes tipos de datos:

- `serial` como clave primaria  
- `varchar`  
- `numeric`  
- `boolean`  
- `date` o `timestamp`  
- `jsonb`  

Después de crearla:
1. Inserta 2 productos.  
2. Haz un `SELECT *` para verificar.  

### ✍️ Pregunta de estudio
**¿Cuándo usarías `numeric` en lugar de `real` o `double precision`?**

---

## 🧩 2. Constraints

### 👉 Reto
Crea una tabla `estudiantes` con estas reglas:

- `id` **PK autoincremental**  
- `nombre` obligatorio  
- `edad` mayor a 0 (**CHECK**)  
- `correo` único  

Inserta:
- 2 estudiantes correctos  
- 1 estudiante con `edad = -1` (debe fallar)  
- 1 estudiante con un `correo` repetido (debe fallar)  

### ✍️ Pregunta de estudio
**¿Cuál es la diferencia entre `UNIQUE` y `PRIMARY KEY`?**

---

## 🧩 3. Relaciones

### 👉 Reto
Crea un esquema **país → región → ciudad**.

Inserta:
- Colombia, Santander, Bucaramanga  
- México, Jalisco, Guadalajara  

Haz un SELECT que muestre:  

### ✍️ Pregunta de estudio
**¿Por qué necesitamos `FOREIGN KEY` en vez de solo poner un número en la columna?**

---

## 🧩 4. Funciones de Fecha

### 👉 Reto
Usa las funciones:

```sql
SELECT CURRENT_DATE;
SELECT NOW();
SELECT AGE('2007-01-01');
SELECT EXTRACT(YEAR FROM NOW());
