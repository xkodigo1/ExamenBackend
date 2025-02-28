# Concesionario CampusCar
# Diseño de Base de Datos para Concesionario de Vehículos

## 1. Introducción

Este documento describe el diseño de una base de datos relacional para un concesionario de vehículos. La base de datos ha sido diseñada para gestionar la información sobre vehículos en stock, clientes, ventas y servicios de mantenimiento.

## 2. Diagrama Entidad-Relación

El siguiente diagrama muestra la estructura general de la base de datos normalizada:

```mermaid
erDiagram
    MARCA {
        int id_marca PK
        string nombre UK
    }
    
    MODELO {
        int id_modelo PK
        string nombre
        int id_marca FK
    }
    
    TIPO_COMBUSTIBLE {
        int id_tipo_combustible PK
        string descripcion UK
    }
    
    TIPO_TRANSMISION {
        int id_tipo_transmision PK
        string descripcion UK
    }
    
    METODO_PAGO {
        int id_metodo_pago PK
        string descripcion UK
    }
    
    TIPO_SERVICIO {
        int id_tipo_servicio PK
        string descripcion UK "Preventivo / Correctivo / etc."
    }
    
    VEHICULO {
        int id_vehiculo PK
        string vin UK "Número de serie único"
        int id_modelo FK
        int anio
        decimal precio
        string color
        int id_tipo_combustible FK
        int id_tipo_transmision FK
        enum estado "Nuevo / Usado"
        boolean disponible "True = Disponible, False = Vendido"
    }
    
    CLIENTE {
        int id_cliente PK
        string nombre
        string apellido
        string telefono
        string email
        varchar direccion "Dirección completa del cliente"
    }
    
    VENDEDOR {
        int id_vendedor PK
        string nombre
        string apellido
        string num_empleado UK
        date fecha_contratacion
    }
    
    VENTA {
        int id_venta PK
        date fecha
        decimal total_transaccion
        int id_metodo_pago FK
        int id_cliente FK
        int id_vendedor FK
    }
    
    DETALLE_VENTA {
        int id_detalle_venta PK
        int id_venta FK
        int id_vehiculo FK
        decimal precio_venta
    }
    
    MANTENIMIENTO {
        int id_mantenimiento PK
        date fecha
        int id_tipo_servicio FK
        string descripcion
        decimal costo
        int id_vehiculo FK
        int id_cliente FK "Nullable"
    }
    
    MARCA ||--o{ MODELO : "tiene"
    MODELO ||--o{ VEHICULO : "categoriza"
    TIPO_COMBUSTIBLE ||--o{ VEHICULO : "clasifica"
    TIPO_TRANSMISION ||--o{ VEHICULO : "define"
    CLIENTE ||--o{ VENTA : "realiza"
    METODO_PAGO ||--o{ VENTA : "utiliza"
    VENDEDOR ||--o{ VENTA : "gestiona"
    VENTA ||--o{ DETALLE_VENTA : "contiene"
    VEHICULO ||--o{ DETALLE_VENTA : "incluido en"
    VEHICULO ||--o{ MANTENIMIENTO : "recibe"
    CLIENTE ||--o{ MANTENIMIENTO : "solicita"
    TIPO_SERVICIO ||--o{ MANTENIMIENTO : "categoriza"
```

## 3. Descripción de Entidades

### 3.1 Tablas de Catálogo

#### 3.1.1 MARCA

Almacena la información de las marcas de vehículos.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_marca | INT | PK | Identificador único de la marca |
| nombre | VARCHAR | UK | Nombre de la marca (ej. Toyota, Ford) |

#### 3.1.2 MODELO

Almacena los modelos de vehículos asociados a cada marca.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_modelo | INT | PK | Identificador único del modelo |
| nombre | VARCHAR | - | Nombre del modelo (ej. Corolla, Focus) |
| id_marca | INT | FK | Referencia a la marca del vehículo |

#### 3.1.3 TIPO_COMBUSTIBLE

Catálogo de tipos de combustible que pueden usar los vehículos.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_tipo_combustible | INT | PK | Identificador único del tipo de combustible |
| descripcion | VARCHAR | UK | Descripción (ej. Gasolina, Diésel, Eléctrico) |

#### 3.1.4 TIPO_TRANSMISION

Catálogo de tipos de transmisión de los vehículos.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_tipo_transmision | INT | PK | Identificador único del tipo de transmisión |
| descripcion | VARCHAR | UK | Descripción (ej. Manual, Automática, CVT) |

#### 3.1.5 METODO_PAGO

Catálogo de métodos de pago disponibles para las ventas.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_metodo_pago | INT | PK | Identificador único del método de pago |
| descripcion | VARCHAR | UK | Descripción (ej. Efectivo, Tarjeta, Transferencia) |

#### 3.1.6 TIPO_SERVICIO

Catálogo de tipos de servicio de mantenimiento.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_tipo_servicio | INT | PK | Identificador único del tipo de servicio |
| descripcion | VARCHAR | UK | Descripción (ej. Preventivo, Correctivo) |

### 3.2 Entidades Principales

#### 3.2.1 VEHICULO

Almacena la información detallada de cada vehículo en el concesionario.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_vehiculo | INT | PK | Identificador único del vehículo |
| vin | VARCHAR | UK | Número de serie único del vehículo (VIN) |
| id_modelo | INT | FK | Referencia al modelo del vehículo |
| anio | INT | - | Año de fabricación |
| precio | DECIMAL | - | Precio de venta |
| color | VARCHAR | - | Color del vehículo |
| id_tipo_combustible | INT | FK | Referencia al tipo de combustible |
| id_tipo_transmision | INT | FK | Referencia al tipo de transmisión |
| estado | ENUM | - | Estado del vehículo (Nuevo/Usado) |
| disponible | BOOLEAN | - | Indica si el vehículo está disponible para venta |

#### 3.2.2 CLIENTE

Almacena la información de los clientes del concesionario.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_cliente | INT | PK | Identificador único del cliente |
| nombre | VARCHAR | - | Nombre del cliente |
| apellido | VARCHAR | - | Apellido del cliente |
| telefono | VARCHAR | - | Número de teléfono |
| email | VARCHAR | - | Correo electrónico |
| direccion | VARCHAR | - | Dirección completa del cliente |

#### 3.2.3 VENDEDOR

Almacena la información de los vendedores del concesionario.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_vendedor | INT | PK | Identificador único del vendedor |
| nombre | VARCHAR | - | Nombre del vendedor |
| apellido | VARCHAR | - | Apellido del vendedor |
| num_empleado | VARCHAR | UK | Número de empleado único |
| fecha_contratacion | DATE | - | Fecha de contratación |

### 3.3 Entidades Transaccionales

#### 3.3.1 VENTA

Registra las transacciones de venta realizadas.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_venta | INT | PK | Identificador único de la venta |
| fecha | DATE | - | Fecha de la transacción |
| total_transaccion | DECIMAL | - | Monto total de la venta |
| id_metodo_pago | INT | FK | Referencia al método de pago utilizado |
| id_cliente | INT | FK | Referencia al cliente que realizó la compra |
| id_vendedor | INT | FK | Referencia al vendedor que gestionó la venta |

#### 3.3.2 DETALLE_VENTA

Registra los vehículos incluidos en cada venta.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_detalle_venta | INT | PK | Identificador único del detalle |
| id_venta | INT | FK | Referencia a la venta |
| id_vehiculo | INT | FK | Referencia al vehículo vendido |
| precio_venta | DECIMAL | - | Precio de venta específico del vehículo |

#### 3.3.3 MANTENIMIENTO

Registra los servicios de mantenimiento realizados a los vehículos.

| Campo | Tipo | Restricción | Descripción |
|-------|------|-------------|-------------|
| id_mantenimiento | INT | PK | Identificador único del servicio |
| fecha | DATE | - | Fecha del servicio |
| id_tipo_servicio | INT | FK | Referencia al tipo de servicio realizado |
| descripcion | VARCHAR | - | Descripción detallada del servicio |
| costo | DECIMAL | - | Costo del servicio |
| id_vehiculo | INT | FK | Referencia al vehículo que recibió el servicio |
| id_cliente | INT | FK, Nullable | Referencia al cliente (opcional) |

## 4. Relaciones entre Entidades

### 4.1 Relaciones de Catálogo

- **MARCA - MODELO**: Una marca puede tener múltiples modelos (1:N).
- **MODELO - VEHICULO**: Un modelo puede estar asociado a múltiples vehículos (1:N).
- **TIPO_COMBUSTIBLE - VEHICULO**: Un tipo de combustible puede estar asociado a múltiples vehículos (1:N).
- **TIPO_TRANSMISION - VEHICULO**: Un tipo de transmisión puede estar asociado a múltiples vehículos (1:N).
- **TIPO_SERVICIO - MANTENIMIENTO**: Un tipo de servicio puede estar asociado a múltiples mantenimientos (1:N).
- **METODO_PAGO - VENTA**: Un método de pago puede estar asociado a múltiples ventas (1:N).

### 4.2 Relaciones Transaccionales

- **CLIENTE - VENTA**: Un cliente puede realizar múltiples ventas (1:N).
- **VENDEDOR - VENTA**: Un vendedor puede gestionar múltiples ventas (1:N).
- **VENTA - DETALLE_VENTA**: Una venta puede contener múltiples detalles de venta (1:N).
- **VEHICULO - DETALLE_VENTA**: Un vehículo puede estar asociado a un detalle de venta (1:1).
- **VEHICULO - MANTENIMIENTO**: Un vehículo puede recibir múltiples servicios de mantenimiento (1:N).
- **CLIENTE - MANTENIMIENTO**: Un cliente puede solicitar múltiples servicios de mantenimiento (1:N).

## 5. Restricciones e Integridad de Datos

### 5.1 Claves Primarias y Únicas

- Todas las tablas tienen definida una clave primaria (PK).
- Los siguientes campos tienen restricciones de unicidad (UK):
  - `nombre` en MARCA
  - `vin` en VEHICULO
  - `num_empleado` en VENDEDOR
  - `descripcion` en las tablas de catálogo

### 5.2 Claves Foráneas

Se han establecido relaciones mediante claves foráneas (FK) para garantizar la integridad referencial:

- El campo `id_marca` en MODELO referencia a MARCA
- Los campos `id_modelo`, `id_tipo_combustible`, `id_tipo_transmision` en VEHICULO referencian a sus respectivas tablas
- Los campos `id_cliente`, `id_vendedor`, `id_metodo_pago` en VENTA referencian a sus respectivas tablas
- Los campos `id_venta`, `id_vehiculo` en DETALLE_VENTA referencian a sus respectivas tablas
- Los campos `id_vehiculo`, `id_cliente`, `id_tipo_servicio` en MANTENIMIENTO referencian a sus respectivas tablas

### 5.3 Otras Restricciones

- El campo `id_cliente` en MANTENIMIENTO es opcional (puede ser NULL) para permitir mantenimientos a vehículos no vendidos.
- El campo `disponible` en VEHICULO debe actualizarse automáticamente a FALSE cuando el vehículo se incluye en una venta.
