# RetailPro – Análisis de ventas (Ventas_Tech_DB)

Proyecto de análisis de datos (curso de Power BI / DAX / SQL) sobre la base **Ventas_Tech_DB** de **RetailPro**, una empresa minorista de tecnología. Se usan consultas SQL con `JOIN` y `UNION ALL` para explorar las ventas y luego se arma un dashboard en Power BI.

Repositorio: https://github.com/espinozarosana/Ejercicio3.git

---

## 1. Descripción del negocio

RetailPro vende productos de tecnología, organizados en categorías, a clientes de distintas ciudades, por dos canales: **Online** y **Presencial**. La gerencia detectó una **caída en las ventas y en la rentabilidad** y necesita entender qué la explica.

**Pregunta diagnóstica:**
> ¿Qué factores están causando la caída de ventas y rentabilidad, y qué categorías o regiones la explican?

**KPIs principales:** Total Ventas, Rentabilidad %, Ticket promedio y Crecimiento de ventas %.

### Modelo de datos (Ventas_Tech_DB)

| Tabla | Campos usados en las consultas |
|---|---|
| `ventas` | id_venta, fecha_venta, id_cliente, id_producto, cantidad, precio_unitario, origen |
| `clientes` | id_cliente, nombre, email, ciudad, fecha_registro |
| `productos` | id_producto, nombre_producto, precio, id_categoria |
| `categorias` | id_categoria, nombre_categoria |

Relaciones: `ventas.id_cliente → clientes`, `ventas.id_producto → productos`, `productos.id_categoria → categorias`.

---

## 2. Herramientas utilizadas

| Herramienta | Uso |
|---|---|
| **MySQL** | Base de datos y consultas SQL |
| **Power BI** | Modelado, medidas DAX y dashboard |
| **Excel** | Revisión y limpieza inicial de los datos fuente |

---

## 3. Estructura de carpetas

```
Ejercicio3/
├── README.md
├── data/                          # Archivos fuente (CSV / Excel)
├── sql/
│   └── m5_consultas_joins.sql     # Consultas JOIN y UNION ALL
├── powerbi/                       # Informe de Power BI (.pbix)
└── docs/                          # Brief y boceto del dashboard
```

> Ajustá las carpetas a lo que realmente tenga tu repositorio.

---

## 4. Contenido del script `m5_consultas_joins.sql`

| # | Consulta | Técnica | Qué devuelve |
|---|---|---|---|
| 1 | Vista de tablas del proyecto | `INNER JOIN` (ventas, clientes, productos, categorias) | Detalle de cada venta con cliente, producto, categoría y total (`cantidad * precio_unitario`) |
| 2 | Clientes sin ventas | `LEFT JOIN` + `IS NULL` | Clientes que nunca compraron |
| 3 | Productos sin ventas | `INNER JOIN` + `LEFT JOIN` + `IS NULL` | Productos que nunca se vendieron, con su categoría y precio |
| 4 | Consolidado por canal | `UNION ALL` + `GROUP BY` | Total de ventas de Online vs. Presencial |

---

## 5. Guía paso a paso

### Requisitos
- MySQL Server 8.x y MySQL Workbench
- Power BI Desktop (Windows)
- Excel
- Git

### Paso 1 – Clonar el repositorio

```bash
git clone https://github.com/espinozarosana/Ejercicio3.git
cd Ejercicio3
```

### Paso 2 – Revisar los datos en Excel
Abrí los archivos fuente, verificá que no haya filas vacías, fechas mal formateadas o duplicados, y guardalos como **CSV (UTF-8)**.

### Paso 3 – Crear la base y cargar los datos
1. Abrí MySQL Workbench y conectate a `localhost` (puerto `3306`).
2. Creá la base y seleccionala:

```sql
CREATE DATABASE IF NOT EXISTS Ventas_Tech_DB;
USE Ventas_Tech_DB;
```

3. Creá las tablas `categorias`, `clientes`, `productos` y `ventas` y cargá los datos (con `LOAD DATA INFILE` o con *Table Data Import Wizard*).

### Paso 4 – Verificar la carga

```sql
SELECT COUNT(*) FROM clientes;
SELECT COUNT(*) FROM productos;
SELECT COUNT(*) FROM categorias;
SELECT COUNT(*) FROM ventas;
```

Si alguna tabla devuelve `0`, revisá la ruta o el formato del CSV.

### Paso 5 – Ejecutar las consultas
En Workbench: `File > Open SQL Script`, elegí `sql/m5_consultas_joins.sql` y ejecutá cada consulta por separado (seleccioná el bloque y presioná ⚡), o por consola:

```bash
mysql -u root -p Ventas_Tech_DB < sql/m5_consultas_joins.sql
```

### Paso 6 – Conectar Power BI
1. `Obtener datos > Base de datos MySQL`.
2. Servidor: `localhost` · Base de datos: `Ventas_Tech_DB`.
3. Seleccioná las 4 tablas y hacé clic en **Cargar**.
4. En la vista **Modelo**, verificá las relaciones descriptas arriba y creá las medidas DAX de los KPIs.

> Si no tenés el conector de MySQL instalado, exportá las tablas a CSV y cargalas con `Obtener datos > Texto/CSV`.

### Si usás PostgreSQL

| MySQL | PostgreSQL |
|---|---|
| `AUTO_INCREMENT` | `SERIAL` / `GENERATED ALWAYS AS IDENTITY` |
| `LOAD DATA INFILE` | `\copy tabla FROM 'archivo.csv' CSV HEADER` |
| `` `nombre` `` | `"nombre"` |
| `USE Ventas_Tech_DB;` | `\c ventas_tech_db` |

Las consultas de `m5_consultas_joins.sql` usan SQL estándar y funcionan igual en ambos motores.

---

## 6. Dashboard

Layout en Z con filtros (fecha, región, categoría, producto, segmento de cliente), 4 KPIs, gráficos de tendencia, ventas por región y por categoría, y una tabla de detalle por producto.

*(Agregá acá las capturas del dashboard.)*

---

## 7. Autor

Proyecto realizado como práctica del curso de Análisis de Datos con Power BI.

- GitHub: [@espinozarosana](https://github.com/espinozarosana)
