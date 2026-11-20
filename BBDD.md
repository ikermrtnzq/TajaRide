# TajaRide - Documentación de la Base de Datos

## 1. Sistema gestor

La base de datos del proyecto utiliza **Microsoft SQL Server**.

Este documento recoge la estructura de las tablas, sus campos, claves primarias, claves foráneas y relaciones entre ellas.

---

## 2. Tablas de usuarios y formación

Estas tablas forman parte de la estructura de usuarios de Tajamar.

### 2.1. USUARIOSTAJAMAR

Almacena la información de los usuarios.

| Campo | Descripción |
|---|---|
| IDUSUARIO | Clave primaria |
| NOMBRE | Nombre del usuario |
| APELLIDOS | Apellidos del usuario |
| EMAIL | Correo electrónico |
| ESTADO | Estado del usuario |
| IMAGEN | Imagen de perfil |
| PASSWORD | Contraseña |
| IDROLE | Clave foránea hacia ROLESCHARLASTAJAMAR |

### 2.2. ROLESCHARLASTAJAMAR

Almacena los roles disponibles para los usuarios.

| Campo | Descripción |
|---|---|
| IDROLE | Clave primaria |
| ROLE | Nombre del rol |

### 2.3. CURSOSTAJAMAR

Almacena los cursos existentes.

| Campo | Descripción |
|---|---|
| IDCURSO | Clave primaria |
| NOMBRE | Nombre del curso |
| FECHAINICIO | Fecha de inicio |
| FECHAFIN | Fecha de finalización |
| ACTIVO | Indica si el curso está activo |

### 2.4. CURSOSUSUARIOSTAJAMAR

Tabla intermedia que relaciona usuarios con cursos.

| Campo | Descripción |
|---|---|
| IDCURSOSUSUARIOS | Clave primaria |
| IDCURSO | Clave foránea hacia CURSOSTAJAMAR |
| IDUSUARIO | Clave foránea hacia USUARIOSTAJAMAR |

---

## 3. Tablas de eventos y grupos

### 3.1. EVENTOS

Almacena la información de los eventos organizados en Tajamar.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idEvento | varchar(255) | Clave primaria |
| NombreEvento | varchar(255) | Nombre del evento |
| CapacidadEvento | varchar(255) | Capacidad máxima del evento |
| LocalizacionEvento | varchar(255) | Lugar donde se celebra |
| FechaEvento | datetime | Fecha y hora del evento |
| OrganizadorEvento | varchar(255) | Empresa organizadora |
| CosteEvento | varchar(255) | Coste del evento |
| GrupoEvento | T/F | Indica si se pueden crear grupos para el evento |

**Funcionamiento de los grupos en los eventos:**

El campo `GrupoEvento` indica si un evento permite crear grupos o no.

- Si `GrupoEvento` es TRUE, los usuarios podrán crear y consultar grupos asociados a ese evento.
- Si `GrupoEvento` es FALSE, no se podrán crear grupos para ese evento.

Para consultar los grupos de un evento, se buscarán en la tabla `GRUPOS` utilizando el `idEvento` correspondiente.

### 3.2. ASISTENTES

Tabla intermedia que relaciona usuarios con eventos.

Cada registro representa a un usuario que se ha apuntado a un evento.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idAsistencia | varchar(255) | Clave primaria |
| idEvento | varchar(255) | Clave foránea hacia EVENTOS |
| idUsuario | varchar(255) | Clave foránea hacia USUARIOSTAJAMAR |

Para apuntarse a un evento se insertará un registro en esta tabla.

Un mismo usuario no debería poder registrarse varias veces en el mismo evento.

### 3.3. GRUPOS

Almacena los grupos creados dentro de los eventos.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idGrupo | tinyint(255) | Clave primaria |
| idEvento | tinyint(255) | Clave foránea hacia EVENTOS |
| nombreGrupo | varchar(255) | Nombre del grupo |
| tamañoGrupo | tinyint(255) | Tamaño del grupo |
| idUsuario | tinyint(255) | Clave foránea hacia USUARIOSTAJAMAR.  |

**Funcionamiento de los grupos:**

- Cada grupo pertenece a un evento.
- Un evento puede tener varios grupos.

Para localizar los grupos de un evento se utilizará su `idEvento`.

---

## 4. Tablas de coches y movilidad

### 4.1. COCHEUSUARIO

Almacena los coches registrados por los usuarios.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idCoche | int(255) | Clave primaria |
| idUsuario | int(255) | Clave foránea hacia USUARIOSTAJAMAR |
| capacidad | varchar(255) | Capacidad del vehículo |

### 4.2. VIAJES

Almacena los viajes publicados por los conductores.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idViaje | int(255) | Clave primaria |
| idCoche | int(255) | Clave foránea hacia COCHEUSUARIO |
| inicio | varchar(255) | Lugar de origen |
| destino | varchar(255) | Lugar de destino |
| horaSalida | time | Hora de salida |
| observacionesViaje | varchar(255) | Información adicional del viaje |

### 4.3. SOLICITUDES

Almacena las solicitudes e invitaciones relacionadas con los viajes.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idSolicitud | int(255) | Clave primaria |
| idUsuario | int(255) | Clave foránea hacia USUARIOSTAJAMAR |
| idViaje | int(255) | Clave foránea hacia VIAJES |
| estadoSolicitud | ENUM | PENDIENTE, ACEPTADA o CANCELADA |
| tipoSolicitud | ENUM | SOLICITUD o INVITACION |
| mensajeSolicitud | varchar(255) | Mensaje asociado a la solicitud |

### 4.4. PASAJEROS

Relaciona a los usuarios con los viajes en los que participan como pasajeros.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idPasajero | int(255) | Clave primaria |
| idUsuario | int(255) | Clave foránea hacia USUARIOSTAJAMAR |
| idViaje | int(255) | Clave foránea hacia VIAJES |

**Funcionamiento de PASAJEROS:**

Esta tabla almacenará exclusivamente a los pasajeros que tengan una invitación aceptada.

No se almacenarán aquí usuarios con solicitudes pendientes o canceladas.

### 4.5. UBICACIONESUSUARIO

Almacena las coordenadas geográficas asociadas a los usuarios.

| Campo | Tipo propuesto | Descripción |
|---|---|---|
| idUbicacion | int(255) | Clave primaria |
| idUsuario | int(255) | Clave foránea hacia USUARIOSTAJAMAR |
| latitud | decimal(9,6) | Latitud geográfica |
| longitud | decimal(9,6) | Longitud geográfica |

**Funcionamiento de las ubicaciones:**

Las ubicaciones se utilizarán principalmente desde el frontend para localizar usuarios cercanos.

La aplicación podrá utilizar las coordenadas de los usuarios para mostrar personas próximas y facilitar la organización de desplazamientos compartidos.

---

## 5. Relaciones entre tablas

Las relaciones planteadas son las siguientes:

| Tabla principal | Tabla relacionada | Relación |
|---|---|---|
| ROLESCHARLASTAJAMAR | USUARIOSTAJAMAR | Un rol puede estar asociado a varios usuarios |
| USUARIOSTAJAMAR | CURSOSUSUARIOSTAJAMAR | Un usuario puede estar asociado a varios cursos |
| CURSOSTAJAMAR | CURSOSUSUARIOSTAJAMAR | Un curso puede tener varios usuarios |
| EVENTOS | ASISTENTES | Un evento puede tener varios asistentes |
| USUARIOSTAJAMAR | ASISTENTES | Un usuario puede apuntarse a varios eventos |
| EVENTOS | GRUPOS | Un evento puede tener varios grupos |
| USUARIOSTAJAMAR | GRUPOS | Un usuario puede ser creador de varios grupos |
| USUARIOSTAJAMAR | COCHEUSUARIO | Un usuario puede registrar coches |
| COCHEUSUARIO | VIAJES | Un coche puede tener varios viajes |
| USUARIOSTAJAMAR | SOLICITUDES | Un usuario puede realizar varias solicitudes |
| VIAJES | SOLICITUDES | Un viaje puede recibir varias solicitudes |
| USUARIOSTAJAMAR | PASAJEROS | Un usuario puede aparecer como pasajero |
| VIAJES | PASAJEROS | Un viaje puede tener varios pasajeros |
| USUARIOSTAJAMAR | UBICACIONESUSUARIO | Un usuario puede tener ubicaciones registradas |

---

## 6. Aspectos pendientes de definir

Los siguientes puntos todavía necesitan concretarse:

1. **Cola de espera:** falta definir cómo se almacenarán los usuarios que esperan una plaza cuando un viaje esté completo.

2. **Tipos de datos:** revisar los tipos propuestos para adaptarlos correctamente a Microsoft SQL Server.

---

## 7. Indicaciones para la IA

Este documento representa el diseño acordado inicialmente por el equipo para la base de datos de TajaRide.

Al trabajar con esta estructura se deben respetar las siguientes indicaciones:

- Utilizar Microsoft SQL Server.
- Mantener los nombres de las tablas y campos originales siempre que sea posible.
- Respetar las relaciones entre tablas.
- Identificar correctamente las claves primarias y foráneas.
- No inventar tablas o campos que no estén definidos.
- No dar por resueltos los aspectos pendientes.
- Si se considera necesario modificar la estructura, explicar primero el motivo y las consecuencias.
- Tener en cuenta que los tipos de datos indicados son una propuesta inicial y pueden necesitar adaptación a SQL Server.
- Los grupos tienen un creador, pero este no dispone de permisos especiales para gestionar a sus integrantes.
- La tabla PASAJEROS solo almacena pasajeros con invitaciones aceptadas.
- La tabla UBICACIONESUSUARIO proporciona las coordenadas que utilizará el frontend para localizar usuarios cercanos.
- La tabla EVENTOS indica si se permiten grupos mediante el campo `GrupoEvento`. Los grupos concretos se consultan en la tabla GRUPOS mediante `idEvento`.

