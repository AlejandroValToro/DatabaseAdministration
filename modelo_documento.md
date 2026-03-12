# CASO DE ESTUDIO: SISTEMA DE DENUNCIAS DE ROBOS EN PARQUEADEROS PÚBLICOS DE MEDELLÍN

---

## 1. MODELO ENTIDAD-RELACIÓN (MODELO CONCEPTUAL)

### 1.1 Identificación de Entidades

| # | Entidad              | Tipo       | Descripción                                                    |
|---|----------------------|------------|----------------------------------------------------------------|
| 1 | ADMINISTRADOR        | Fuerte     | Persona encargada de la gestión de un parqueadero              |
| 2 | PARQUEADERO          | Fuerte     | Establecimiento público de estacionamiento en Medellín         |
| 3 | CELDA                | Débil      | Espacio individual dentro de un parqueadero para un vehículo   |
| 4 | VEHICULO             | Fuerte     | Automotor estacionado en una celda al momento de un robo       |
| 5 | PROPIETARIO          | Fuerte     | Dueño de un vehículo y posible denunciante                     |
| 6 | DENUNCIA             | Fuerte     | Reporte formal de un robo presentado por un propietario        |
| 7 | DENUNCIA_PENAL       | Subtipo    | Denuncia que se investiga judicialmente                        |
| 8 | DENUNCIA_INFORMAL    | Subtipo    | Denuncia que solo deja registro del robo                       |
| 9 | OBJETO_ROBADO        | Fuerte     | Bien sustraído de un vehículo, registrado en una denuncia      |

### 1.2 Atributos por Entidad

**ADMINISTRADOR**
- `id_administrador` (PK) — Identificador único
- `cedula` — Número de documento de identidad
- `nombre` — Nombre completo
- `telefono` — Teléfono de contacto
- `email` — Correo electrónico
- `direccion` — Dirección de residencia

**PARQUEADERO**
- `id_parqueadero` (PK) — Identificador único
- `nit` — Número de identificación tributaria
- `nombre` — Nombre comercial del parqueadero
- `direccion` — Ubicación del parqueadero
- `telefono` — Teléfono del parqueadero

**CELDA** *(Entidad débil — depende de PARQUEADERO)*
- `numero_celda` (PK parcial / discriminante) — Número de la celda dentro del parqueadero (desde 1 en adelante)
- `tamano` — Tamaño de la celda (PEQUENA, MEDIANA, GRANDE)

**VEHICULO**
- `placa` (PK) — Placa del vehículo (clave natural)
- `marca` — Marca del vehículo
- `modelo` — Modelo o referencia
- `anio` — Año del vehículo
- `color` — Color del vehículo
- `tipo` — Tipo de vehículo (Sedan, SUV, Hatchback, Camioneta, etc.)

**PROPIETARIO**
- `id_propietario` (PK) — Identificador único
- `cedula` — Número de documento de identidad
- `nombre` — Nombre completo
- `telefono` — Teléfono de contacto
- `email` — Correo electrónico
- `direccion` — Dirección de residencia

**DENUNCIA** *(Supertipo)*
- `numero_denuncia` (PK) — Número único del formato de denuncia
- `fecha_denuncia` — Fecha en que se presenta la denuncia
- `tipo_denuncia` — Discriminador: 'PENAL' o 'INFORMAL'

**DENUNCIA_PENAL** *(Subtipo de DENUNCIA)*
- `numero_juzgado` — Número del juzgado donde se entabló la denuncia

**DENUNCIA_INFORMAL** *(Subtipo de DENUNCIA)*
- `hora_denuncia` — Hora en que se entabló la denuncia

**OBJETO_ROBADO**
- `id_objeto` (PK) — Identificador único
- `descripcion` — Descripción general del objeto (portátil, celular, etc.)
- `valor` — Valor estimado del objeto en pesos
- `marca` — Marca del objeto robado

### 1.3 Relaciones y Cardinalidades

| Relación                   | Entidad 1       | Entidad 2          | Cardinalidad | Descripción                                                                 |
|----------------------------|-----------------|--------------------|--------------|-----------------------------------------------------------------------------|
| ADMINISTRA                 | ADMINISTRADOR   | PARQUEADERO        | 1:N          | Un administrador puede administrar varios parqueaderos; cada parqueadero tiene exactamente un administrador. |
| CONTIENE                   | PARQUEADERO     | CELDA              | 1:N (identificante) | Un parqueadero contiene muchas celdas; cada celda pertenece a un solo parqueadero. Relación identificante (celda es entidad débil). |
| POSEE                      | PROPIETARIO     | VEHICULO           | N:M          | Un propietario puede tener varios vehículos; un vehículo puede tener varios propietarios. |
| PRESENTA                   | PROPIETARIO     | DENUNCIA           | 1:N          | Un propietario puede presentar varias denuncias; cada denuncia es presentada por exactamente un propietario. |
| AFECTA                     | DENUNCIA        | VEHICULO           | N:1          | Cada denuncia se refiere a un vehículo específico; un vehículo puede aparecer en varias denuncias. |
| OCURRE_EN                  | DENUNCIA        | CELDA              | N:1          | Cada denuncia ocurre en una celda específica; una celda puede tener varias denuncias a lo largo del tiempo. |
| REGISTRA                   | DENUNCIA        | OBJETO_ROBADO      | 1:N          | Una denuncia registra uno o más objetos robados; cada objeto robado pertenece a una sola denuncia. |
| ES_UN (Especialización)    | DENUNCIA        | DENUNCIA_PENAL     | Total, Disjunta | Jerarquía de especialización: toda denuncia es penal o informal, nunca ambas. |
| ES_UN (Especialización)    | DENUNCIA        | DENUNCIA_INFORMAL  | Total, Disjunta | (Continuación de la jerarquía anterior.) |

### 1.4 Descripción del Diagrama ER

```
  ┌───────────────────┐       ADMINISTRA        ┌───────────────────┐
  │   ADMINISTRADOR   │─────────(1:N)───────────│    PARQUEADERO    │
  │                   │                          │                   │
  │ PK: id_admin      │                          │ PK: id_parq       │
  │ cedula, nombre    │                          │ nit, nombre       │
  │ telefono, email   │                          │ direccion, tel    │
  │ direccion         │                          │                   │
  └───────────────────┘                          └────────┬──────────┘
                                                          │
                                                    CONTIENE (1:N)
                                                   (identificante)
                                                          │
                                                 ┌────────┴──────────┐
                                                 │      CELDA        │
                                                 │  (Entidad débil)  │
                                                 │ PK parcial: num   │
                                                 │ tamano             │
                                                 └────────┬──────────┘
                                                          │
                                                    OCURRE_EN (N:1)
                                                          │
  ┌───────────────────┐       PRESENTA (1:N)     ┌────────┴──────────┐
  │   PROPIETARIO     │────────────────────────│      DENUNCIA      │
  │                   │                          │  (Supertipo)       │
  │ PK: id_prop       │                          │ PK: num_denuncia   │
  │ cedula, nombre    │                          │ fecha, tipo        │
  │ telefono, email   │                          │                    │
  │ direccion         │                          └──┬────────────┬────┘
  └────────┬──────────┘                             │            │
           │                                   ┌────┴────┐  ┌────┴────────┐
      POSEE (N:M)                              │ PENAL   │  │ INFORMAL    │
           │                                   │ juzgado │  │ hora        │
  ┌────────┴──────────┐                        └─────────┘  └─────────────┘
  │    VEHICULO       │
  │                   │         AFECTA (N:1)
  │ PK: placa         │◄────────────────────── DENUNCIA
  │ marca, modelo     │
  │ anio, color, tipo │          REGISTRA (1:N)
  └───────────────────┘    DENUNCIA ──────────► ┌──────────────┐
                                                 │ OBJETO_ROBADO│
                                                 │ PK: id_obj   │
                                                 │ descripcion  │
                                                 │ valor, marca │
                                                 └──────────────┘
```

**Nota:** Este diagrama es una representación textual simplificada. Se recomienda dibujarlo
con una herramienta como draw.io, Lucidchart o ERDPlus usando la notación Chen o pata de gallo.

---

## 2. MODELO RELACIONAL (MODELO LÓGICO)

### 2.1 Reglas de Traducción Aplicadas

1. **Entidad fuerte** → Tabla con sus atributos. La clave primaria de la entidad se convierte en PK.
2. **Entidad débil (CELDA)** → Tabla cuya PK es la combinación de la PK del propietario fuerte (id_parqueadero) + el discriminante (numero_celda). En la implementación física se usa un surrogate key (id_celda) con restricción UNIQUE sobre la clave natural compuesta.
3. **Relación N:M (POSEE)** → Tabla intermedia VEHICULO_PROPIETARIO con PKs de ambas entidades como PK compuesta.
4. **Relación 1:N** → La PK del lado "1" se agrega como FK en la tabla del lado "N".
5. **Especialización total disjunta (DENUNCIA)** → Se mantiene la tabla supertipo con discriminador, más una tabla por cada subtipo cuya PK es también FK al supertipo.

### 2.2 Esquemas Relacionales

La notación utilizada es: **TABLA**(<u>PK</u>, atributo, *FK→TABLA_REFERENCIADA*)

1. **ADMINISTRADOR**(<u>id_administrador</u>, cedula, nombre, telefono, email, direccion)

2. **PARQUEADERO**(<u>id_parqueadero</u>, nit, nombre, direccion, telefono, *id_administrador→ADMINISTRADOR*)

3. **CELDA**(<u>id_celda</u>, numero_celda, tamano, *id_parqueadero→PARQUEADERO*)
   - UNIQUE(id_parqueadero, numero_celda)

4. **VEHICULO**(<u>placa</u>, marca, modelo, anio, color, tipo)

5. **PROPIETARIO**(<u>id_propietario</u>, cedula, nombre, telefono, email, direccion)

6. **VEHICULO_PROPIETARIO**(<u>*placa→VEHICULO*</u>, <u>*id_propietario→PROPIETARIO*</u>)

7. **DENUNCIA**(<u>numero_denuncia</u>, fecha_denuncia, tipo_denuncia, *id_propietario→PROPIETARIO*, *placa→VEHICULO*, *id_celda→CELDA*)

8. **DENUNCIA_PENAL**(<u>*numero_denuncia→DENUNCIA*</u>, numero_juzgado)

9. **DENUNCIA_INFORMAL**(<u>*numero_denuncia→DENUNCIA*</u>, hora_denuncia)

10. **OBJETO_ROBADO**(<u>id_objeto</u>, descripcion, valor, marca, *numero_denuncia→DENUNCIA*)

### 2.3 Diagrama del Modelo Relacional

```
ADMINISTRADOR                    PARQUEADERO                         CELDA
┌──────────────────┐            ┌──────────────────┐              ┌──────────────────┐
│ id_administrador │◄───────────│ id_administrador │              │ id_celda         │
│ cedula           │    (FK)    │ id_parqueadero   │◄─────────────│ id_parqueadero   │
│ nombre           │            │ nit              │     (FK)     │ numero_celda     │
│ telefono         │            │ nombre           │              │ tamano           │
│ email            │            │ direccion        │              └────────┬─────────┘
│ direccion        │            │ telefono         │                       │
└──────────────────┘            └──────────────────┘                  (FK) │
                                                                          │
PROPIETARIO                      DENUNCIA                                 │
┌──────────────────┐            ┌──────────────────┐                      │
│ id_propietario   │◄───────────│ id_propietario   │──────────────────────┘
│ cedula           │    (FK)    │ numero_denuncia  │
│ nombre           │            │ fecha_denuncia   │
│ telefono         │            │ tipo_denuncia    │──────┐
│ email            │            │ placa            │      │
│ direccion        │            │ id_celda         │      │
└───────┬──────────┘            └───────┬──────────┘      │
        │                               │                 │
        │  VEHICULO_PROPIETARIO         │           ┌─────┴──────┐
        │  ┌──────────────────┐         │           │            │
        └──│ id_propietario   │   ┌─────┴─────┐  ┌─┴──────┐  ┌──┴───────────┐
           │ placa            │   │DENUNCIA    │  │DENUNCIA│  │ OBJETO       │
           └───────┬──────────┘   │PENAL       │  │INFORMAL│  │ ROBADO       │
                   │              │num_denuncia│  │num_den │  │ id_objeto    │
           ┌───────┴──────────┐   │num_juzgado │  │hora    │  │ descripcion  │
           │   VEHICULO       │   └────────────┘  └────────┘  │ valor        │
           │ placa            │                               │ marca        │
           │ marca            │                               │ num_denuncia │
           │ modelo           │                               └──────────────┘
           │ anio             │
           │ color            │
           │ tipo             │
           └──────────────────┘
```

---

## 3. NORMALIZACIÓN

Se analizan tres tablas del modelo relacional, justificando la forma normal superior
(más alta) en la que se encuentran.

### 3.1 Tabla: PROPIETARIO

**Esquema:** PROPIETARIO(<u>id_propietario</u>, cedula, nombre, telefono, email, direccion)

**Claves candidatas:** {id_propietario}, {cedula} (ambas identifican de forma única a un propietario)

**Análisis:**

| Forma Normal | Criterio | ¿Cumple? | Justificación |
|---|---|---|---|
| **1FN** | Todos los atributos son atómicos y existe una clave primaria | ✅ Sí | Cada atributo almacena un solo valor indivisible. `id_propietario` es PK. |
| **2FN** | No hay dependencias parciales | ✅ Sí | La PK es simple (un solo atributo), por lo que es imposible que existan dependencias parciales. |
| **3FN** | No hay dependencias transitivas | ✅ Sí | Todos los atributos no clave (cedula, nombre, telefono, email, direccion) dependen directa y exclusivamente de `id_propietario`. No existe un atributo no clave que dependa de otro atributo no clave. |
| **FNBC** | Todo determinante es clave candidata | ✅ Sí | Los determinantes son `id_propietario` y `cedula`, y ambos son claves candidatas. No hay dependencias funcionales donde el determinante no sea superclave. |

**Conclusión:** La tabla PROPIETARIO se encuentra en **Forma Normal de Boyce-Codd (FNBC)**, que es la forma normal más alta aplicable en este caso.

---

### 3.2 Tabla: DENUNCIA

**Esquema:** DENUNCIA(<u>numero_denuncia</u>, fecha_denuncia, tipo_denuncia, id_propietario, placa, id_celda)

**Clave candidata:** {numero_denuncia}

**Dependencias funcionales identificadas:**
- numero_denuncia → fecha_denuncia, tipo_denuncia, id_propietario, placa, id_celda

**Análisis:**

| Forma Normal | Criterio | ¿Cumple? | Justificación |
|---|---|---|---|
| **1FN** | Todos los atributos son atómicos y existe PK | ✅ Sí | Cada atributo almacena un solo valor. `numero_denuncia` es PK. |
| **2FN** | No hay dependencias parciales | ✅ Sí | La PK es simple (un solo atributo), por lo que no pueden existir dependencias parciales. |
| **3FN** | No hay dependencias transitivas | ✅ Sí | Todos los atributos no clave dependen directamente de `numero_denuncia`. No existe cadena transitiva: por ejemplo, `tipo_denuncia` no determina otros atributos de esta tabla, ni `id_propietario` determina `placa` dentro de este contexto. |
| **FNBC** | Todo determinante es clave candidata | ✅ Sí | El único determinante es `numero_denuncia`, que es la clave candidata. Las FKs (id_propietario, placa, id_celda) no determinan otros atributos no clave de esta tabla. |

**Conclusión:** La tabla DENUNCIA se encuentra en **Forma Normal de Boyce-Codd (FNBC)**.

---

### 3.3 Tabla: OBJETO_ROBADO

**Esquema:** OBJETO_ROBADO(<u>id_objeto</u>, descripcion, valor, marca, numero_denuncia)

**Clave candidata:** {id_objeto}

**Dependencias funcionales identificadas:**
- id_objeto → descripcion, valor, marca, numero_denuncia

**Análisis:**

| Forma Normal | Criterio | ¿Cumple? | Justificación |
|---|---|---|---|
| **1FN** | Todos los atributos son atómicos y existe PK | ✅ Sí | Cada atributo es atómico. `id_objeto` es PK. No hay grupos repetitivos ni atributos multivaluados. |
| **2FN** | No hay dependencias parciales | ✅ Sí | La PK es simple (un solo atributo), lo que elimina la posibilidad de dependencias parciales. |
| **3FN** | No hay dependencias transitivas | ✅ Sí | No existe dependencia transitiva entre atributos no clave. `descripcion` no determina `valor` (objetos con la misma descripción pueden tener diferentes valores). `marca` no determina `valor` ni `descripcion`. Cada atributo depende únicamente de `id_objeto`. |
| **FNBC** | Todo determinante es clave candidata | ✅ Sí | El único determinante funcional es `id_objeto`, que es la clave candidata. Ningún subconjunto de atributos no clave actúa como determinante. |

**Conclusión:** La tabla OBJETO_ROBADO se encuentra en **Forma Normal de Boyce-Codd (FNBC)**.

---

### 3.4 Resumen de Normalización

| Tabla           | Forma Normal Superior | Justificación clave                                                       |
|-----------------|-----------------------|---------------------------------------------------------------------------|
| PROPIETARIO     | FNBC                  | PKs simples, dos claves candidatas (id, cedula), sin dependencias transitivas |
| DENUNCIA        | FNBC                  | PK simple, todas las dependencias parten del número de denuncia            |
| OBJETO_ROBADO   | FNBC                  | PK simple (surrogate key), sin dependencias transitivas ni parciales       |

Las tres tablas cumplen con FNBC porque en todos los casos cada determinante funcional es una superclave de la relación, lo cual es el criterio definitorio de esta forma normal.

---

