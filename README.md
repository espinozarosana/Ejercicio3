# RetailPro – Ejercicio 9: Ventas Tech

Proyecto de análisis de datos para **RetailPro**, hecho como parte de mi formación en análisis de datos (SQL + Power BI).
Repositorio: <https://github.com/espinozarosana/Ejercicio3>

---

## 1. Descripción del negocio

**RetailPro** es un negocio de venta minorista de **productos tecnológicos**: laptops, monitores, periféricos, audio y almacenamiento. Vende a clientes de distintas ciudades de Argentina (Buenos Aires, Córdoba, Rosario, Mendoza y Tucumán).

El proyecto organiza la información comercial en una base de datos relacional llamada **`Ventas_Tech_DB`** para poder responder preguntas como:

- ¿Cuánto se vendió en total y por período?
- ¿Qué productos y categorías generan más ingresos?
- ¿Qué clientes y ciudades compran más?
- ¿Cuál es el stock disponible de cada producto?

Los resultados se pueden luego analizar y visualizar en un dashboard de Power BI.

### Modelo de datos

La base tiene **4 tablas** relacionadas:

| Tabla | Descripción | Clave primaria | Relaciones |
|---|---|---|---|
| `categorias` | Categorías de producto (Computación, Accesorios, Audio, Almacenamiento) | `id_categoria` | – |
| `clientes` | Datos del cliente: nombre, email, ciudad, fecha de registro | `id_cliente` | – |
| `productos` | Catálogo con precio, stock y estado (activo) | `id_producto` | `id_categoria` → `categorias` |
| `ventas` | Cada venta: cliente, producto, cantidad, precio unitario y fecha | `id_venta` | `id_cliente` → `clientes`, `id_producto` → `productos` |

```
categorias 1 ───< productos 1 ───< ventas >─── 1 clientes
```

El script carga datos de ejemplo: **4 categorías, 5 clientes, 6 productos y 10 ventas** (marzo de 2024).

---

## 2. Herramientas utilizadas

| Herramienta | Uso en el proyecto |
|---|---|
| **MySQL / PostgreSQL** | Crear la base de datos, las tablas y cargar los datos (`EJ3.sql`) |
| **Excel** | Revisar y exportar resultados de las consultas |
| **Power BI** | Modelado, medidas DAX y dashboard de KPIs |
| **Git / GitHub** | Control de versiones y publicación del proyecto |

> El script usa sintaxis estándar y funciona tanto en **PostgreSQL** como en **MySQL**. Esta guía usa PostgreSQL y agrega la variante para MySQL donde cambia.

---

## 3. Estructura de carpetas

```
Ejercicio3/
├── README.md     ← este archivo
└── EJ3.sql       ← script: crea la base, las tablas, inserta datos y valida
```

Si más adelante agregás el dashboard o capturas, podés sumar:

```
├── powerbi/      ← archivo .pbix
└── docs/         ← capturas y boceto del dashboard
```

---

## 4. Qué hace el script `EJ3.sql`

1. Crea la base de datos `Ventas_Tech_DB`.
2. Crea las tablas `categorias`, `clientes`, `productos` y `ventas` (con claves primarias, foráneas y restricciones `NOT NULL` / `UNIQUE`).
3. Inserta los datos de ejemplo, en este orden (importante por las claves foráneas): categorías → clientes → productos → ventas.
4. Valida la carga con un `SELECT * FROM ...` de cada tabla.

---

## 5. Guía paso a paso para ejecutar el script

### Requisitos previos

- PostgreSQL (13 o superior) con **pgAdmin** o la terminal `psql` — o bien MySQL.
- Git instalado.
- (Opcional) Power BI Desktop y Excel.

### Paso 1 – Clonar el repositorio

```bash
git clone https://github.com/espinozarosana/Ejercicio3.git
cd Ejercicio3
```

### Paso 2 – Crear la base de datos

El script empieza con `CREATE DATABASE Ventas_Tech_DB;`, pero ese comando **no cambia automáticamente** a la nueva base. Si ejecutás todo de corrido, las tablas pueden quedar en otra base. Por eso conviene crearla primero y ejecutar el script ya conectada a ella.

**PostgreSQL** (desde `psql`):

```sql
CREATE DATABASE ventas_tech_db;
```

**MySQL:**

```sql
CREATE DATABASE Ventas_Tech_DB;
```

> Después, en `EJ3.sql`, poné `--` delante de la primera línea (`-- CREATE DATABASE Ventas_Tech_DB;`) para que no dé error de "ya existe".

### Paso 3 – Ejecutar `EJ3.sql`

**PostgreSQL – terminal:**

```bash
psql -U postgres -d ventas_tech_db -f EJ3.sql
```

**PostgreSQL – pgAdmin:**

1. Conectate a la base `ventas_tech_db`.
2. Abrí *Query Tool* → abrí el archivo `EJ3.sql`.
3. Presioná **F5** para ejecutarlo.

**MySQL – terminal:**

```bash
mysql -u root -p Ventas_Tech_DB < EJ3.sql
```

### Paso 4 – Verificar que todo se cargó

El final del script ya muestra el contenido de las 4 tablas. También podés comprobar:

```sql
SELECT COUNT(*) FROM ventas;          -- debería dar 10
SELECT COUNT(*) FROM productos;       -- debería dar 6
```

### Paso 5 – Probar una consulta de análisis

Ventas totales por producto:

```sql
SELECT p.nombre_producto,
       SUM(v.cantidad)                   AS unidades_vendidas,
       SUM(v.cantidad * v.precio_unitario) AS ingreso_total
FROM ventas v
JOIN productos p ON p.id_producto = v.id_producto
GROUP BY p.nombre_producto
ORDER BY ingreso_total DESC;
```

Ingreso total de todas las ventas (con los datos de ejemplo da **6444.00**):

```sql
SELECT SUM(cantidad * precio_unitario) AS ingreso_total FROM ventas;
```

### Paso 6 – Conectar con Power BI (opcional)

1. En Power BI Desktop: *Obtener datos* → *Base de datos PostgreSQL* (o *MySQL*).
2. Servidor: `localhost` · Base de datos: `ventas_tech_db`.
3. Seleccioná las 4 tablas y cargalas.
4. Revisá las relaciones en la vista *Modelo* y creá medidas DAX (ventas totales, ticket promedio, etc.).

---

## 6. Problemas frecuentes

| Problema | Posible solución |
|---|---|
| `database "Ventas_Tech_DB" already exists` | Ya la creaste en el Paso 2: comentá la primera línea del script. |
| `No database selected` (MySQL) | Ejecutá con el nombre de la base: `mysql -u root -p Ventas_Tech_DB < EJ3.sql`, o agregá `USE Ventas_Tech_DB;`. |
| `relation "categorias" already exists` | Ya corriste el script antes. Borrá las tablas (o la base) y ejecutalo de nuevo. |
| Error de clave foránea al insertar | Respetá el orden: categorías → clientes → productos → ventas. |
| Tildes o ñ mal mostradas | Guardá `EJ3.sql` con codificación **UTF-8**. |

---

## 7. Autora

**Rosana Espinoza** – Estudiante de análisis de datos.
GitHub: [@espinozarosana](https://github.com/espinozarosana)
