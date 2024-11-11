## Punto 1: Tickets para el Desarrollo del Modelo de Base de Datos con Prisma
---

### 1.1 Diagrama de Entidad-Relación (ER)

Este diagrama representa las entidades `User`, `Role`, `Session`, `Candidate`, `Education`, `Experience` y `Document` en la base de datos, junto con sus relaciones y atributos clave. Todos los modelos están alineados con la estructura aprobada, garantizando que cada entidad incluya los atributos necesarios y que se mantenga la integridad referencial entre las relaciones.

```mermaid
erDiagram
    User {
        int id PK
        string email UNIQUE
        string password
        string firstName
        string lastName
        string phone
        string address
        int roleId FK
    }

    Role {
        int id PK
        string roleName
    }

    Session {
        int id PK
        int userId FK
        string token
        datetime expiresAt
    }

    Candidate {
        int id PK
        string firstName
        string lastName
        string email UNIQUE
        string phone
        string address
    }

    Education {
        int id PK
        string degree
        string institution
        date startDate
        date endDate
        int candidateId FK
    }

    Experience {
        int id PK
        string position
        string company
        date startDate
        date endDate
        int candidateId FK
    }

    Document {
        int id PK
        string filePath
        string fileType
        datetime uploadDate
        int candidateId FK
    }

    User ||--o{ Session : "has many"
    User ||--o{ Candidate : "created by"
    User }|--|| Role : "has one"
    Candidate ||--o{ Education : "has many"
    Candidate ||--o{ Experience : "has many"
    Candidate ||--o{ Document : "has many"
```

---

### 1.2 Tickets de Trabajo

Cada ticket se ha ajustado para incluir todos los detalles y pasos específicos para implementar el modelo de datos en Prisma, respetando la estructura aprobada y la organización en capas del proyecto.

---

#### Ticket 1.1: Crear el Modelo "User" en Prisma

- **Descripción**: Configurar el modelo `User` en Prisma para almacenar la información de cada usuario registrado en el sistema, como reclutadores y administradores. Este modelo incluirá los datos de perfil, rol y credenciales del usuario.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `User` en el archivo `prisma/schema.prisma`.
  - **Campos**:
    - `id`: Clave primaria.
    - `email`: Correo electrónico único para cada usuario, necesario para el inicio de sesión.
    - `password`: Contraseña encriptada para proteger las credenciales del usuario.
    - `firstName`, `lastName`: Nombre y apellido del usuario.
    - `phone`, `address`: Información adicional de contacto.
    - `roleId`: Relación con el modelo `Role` para definir el rol del usuario.
  - **Relaciones**:
    - Relacionar `User` con `Role` mediante el campo `roleId`, definiendo una relación uno a uno.
    - Relacionar `User` con `Session` y `Candidate`, permitiendo múltiples sesiones y la creación de candidatos por cada usuario.

- **Criterios de Aceptación**:
  - El modelo `User` está correctamente definido en Prisma con las relaciones y restricciones necesarias para `Role`, `Session` y `Candidate`.
  - El campo `email` tiene una restricción de unicidad, y las contraseñas se almacenan en un formato encriptado.

---

#### Ticket 1.2: Crear el Modelo "Role" en Prisma

- **Descripción**: Configurar el modelo `Role` en Prisma para definir roles específicos de usuario, como `recruiter` o `admin`, que se utilizarán para gestionar permisos y accesos en la aplicación.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `Role` en `prisma/schema.prisma`.
  - **Campos**:
    - `id`: Clave primaria.
    - `roleName`: Nombre del rol que describe el tipo de acceso (e.g., `recruiter`, `admin`).
  - **Relaciones**:
    - Asociar `Role` con `User` mediante `roleId`, estableciendo una relación uno a uno entre el rol y cada usuario.

- **Criterios de Aceptación**:
  - El modelo `Role` está definido en Prisma, permitiendo la asignación de roles específicos a cada usuario y ayudando en la gestión de permisos.

---

#### Ticket 1.3: Crear el Modelo "Session" en Prisma

- **Descripción**: Crear el modelo `Session` en Prisma para gestionar las sesiones activas de usuario, permitiendo validar la autenticación y controlar la validez de cada sesión.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `Session` en `prisma/schema.prisma`.
  - **Campos**:
    - `id`: Clave primaria.
    - `userId`: Relación con el modelo `User`, identificando al usuario de cada sesión.
    - `token`: Token de sesión único.
    - `expiresAt`: Fecha y hora de expiración de la sesión.
  - **Relaciones**:
    - Crear una relación entre `Session` y `User` mediante `userId`, permitiendo que cada usuario tenga múltiples sesiones activas.

- **Criterios de Aceptación**:
  - El modelo `Session` está correctamente configurado en Prisma, y permite almacenar y manejar tokens de sesión, con validación de expiración y asociación al usuario correspondiente.

---

#### Ticket 1.4: Crear el Modelo "Candidate" en Prisma

- **Descripción**: Configurar el modelo `Candidate` en Prisma para almacenar la información básica de cada candidato, incluyendo los datos personales y de contacto.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `Candidate` en `prisma/schema.prisma`.
  - **Campos**:
    - `id`: Clave primaria.
    - `firstName`, `lastName`, `email`, `phone`, `address`: Información personal y de contacto del candidato.
  - **Relaciones**:
    - Relacionar `Candidate` con `User` (creador del candidato) y permitir múltiples registros de `Education` y `Experience`.

- **Criterios de Aceptación**:
  - El modelo `Candidate` está correctamente definido en Prisma, permitiendo almacenar todos los datos básicos de cada candidato y mantener la integridad de sus relaciones con `User`, `Education` y `Experience`.

---

#### Ticket 1.5: Crear el Modelo "Education" en Prisma

- **Descripción**: Crear el modelo `Education` en Prisma para registrar múltiples experiencias educativas asociadas a un candidato, permitiendo un historial académico detallado.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `Education` en `prisma/schema.prisma`.
  - **Campos**:
    - `id`: Clave primaria.
    - `degree`: Título obtenido.
    - `institution`: Institución educativa.
    - `startDate`, `endDate`: Fechas de inicio y finalización de la educación.
    - `candidateId`: Relación con `Candidate`.
  - **Relaciones**:
    - Cada registro de `Education` está relacionado con un `Candidate`, permitiendo almacenar múltiples experiencias educativas para cada candidato.

- **Criterios de Aceptación**:
  - El modelo `Education` está configurado en Prisma y permite registrar múltiples experiencias educativas asociadas a un candidato específico.

---

#### Ticket 1.6: Crear el Modelo "Experience" en Prisma

- **Descripción**: Crear el modelo `Experience` en Prisma para registrar múltiples experiencias laborales asociadas a un candidato, permitiendo un historial laboral completo.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `Experience` en `prisma/schema.prisma`.
  - **Campos**:
    - `id`: Clave primaria.
    - `position`: Cargo desempeñado.
    - `company`: Empresa.
    - `startDate`, `endDate`: Fechas de inicio y finalización de la experiencia.
    - `candidateId`: Relación con `Candidate`.
  - **Relaciones**:
    - Cada registro de `Experience` se asocia con un `Candidate`, permitiendo un historial detallado de la experiencia laboral de cada candidato.

- **Criterios de Aceptación**:
  - El modelo `Experience` permite almacenar múltiples experiencias laborales para cada candidato, y las relaciones con `Candidate` se mantienen con integridad referencial.

---

#### Ticket 1.7: Crear el Modelo "Document" para Almacenamiento de Archivos

- **Descripción**: Crear el modelo `Document` en Prisma para gestionar el almacenamiento de documentos, como el CV de cada candidato, en la base de datos.

- **Especificaciones**:
  - **Ubicación**: Definir el modelo `Document` en `prisma/schema.prisma`.
  - **Campos**:


    - `id`: Clave primaria.
    - `filePath`: Ruta de almacenamiento del archivo.
    - `fileType`: Tipo de archivo (e.g., `PDF`, `DOCX`).
    - `uploadDate`: Fecha de carga del archivo.
    - `candidateId`: Relación con el candidato al que pertenece el documento.
  - **Relaciones**:
    - El modelo `Document` está relacionado con `Candidate`, permitiendo que cada candidato tenga múltiples documentos asociados.

- **Criterios de Aceptación**:
  - El modelo `Document` se define correctamente en Prisma y permite almacenar y gestionar múltiples documentos asociados a un candidato específico.

---

#### Ticket 1.8: Ejecutar Migraciones para Crear Tablas en la Base de Datos

- **Descripción**: Ejecutar una migración única en Prisma para crear todas las tablas y relaciones de la base de datos una vez que todos los modelos estén aprobados.

- **Especificaciones**:
  - **Ubicación**: En `prisma/schema.prisma`, verificar que todos los modelos estén definidos y revisados.
  - **Comando de Migración**: Ejecutar `prisma migrate dev --name initial_migration` para aplicar todas las migraciones en una sola operación.

- **Criterios de Aceptación**:
  - La migración crea todas las tablas y relaciones, incluyendo `User`, `Role`, `Session`, `Candidate`, `Education`, `Experience` y `Document`, asegurando la integridad referencial entre las entidades.

---
