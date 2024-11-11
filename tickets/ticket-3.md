## Punto 3: Tickets para el Desarrollo del Backend

### Ticket 3.1: Implementar Autenticación y Manejo de Roles

- **Descripción**: Configurar la autenticación y el manejo de roles en el backend, asegurando que solo los usuarios con roles adecuados puedan acceder a ciertos endpoints, como la funcionalidad de añadir candidatos. La autenticación será manejada mediante tokens de sesión, y los roles determinarán los permisos de cada usuario.

- **Especificaciones**:
  - **Ubicación**:
    - **Presentation (API)**: Configurar las rutas en `backend/src/routes/authRoutes.ts` para exponer los endpoints de inicio de sesión, cierre de sesión y validación de sesión.
    - **Application**: Crear el controlador en `backend/src/controllers/authController.ts` que se encargará de la lógica de autenticación, incluyendo el manejo de sesiones y verificación de tokens.
    - **Domain**: Implementar la lógica de negocio relacionada con la autenticación y roles en `backend/src/domain/services/authService.ts`, incluyendo la generación de tokens y la validación de roles.
    - **Infrastructure**: Crear el repositorio de usuarios en `backend/src/infrastructure/repositories/userRepository.ts` para manejar la verificación de credenciales, además de un repositorio para la gestión de sesiones en `sessionRepository.ts`.
  - **Funcionalidad de la Ruta**:
    - Crear un endpoint POST (`/api/auth/login`) para manejar el inicio de sesión, recibiendo las credenciales del usuario y devolviendo un token de sesión si son válidas.
    - Crear un endpoint GET (`/api/auth/validate`) para validar la sesión y el rol de un usuario.
    - Crear un endpoint POST (`/api/auth/logout`) para manejar el cierre de sesión y revocar el token de sesión.
  - **Controlador de Autenticación**:
    - Implementar funciones en `authController.ts` que manejen las solicitudes de autenticación, invocando el servicio de dominio para la lógica de autenticación y roles.
    - Validar las credenciales y roles en el servicio antes de devolver un token o permitir acceso a rutas protegidas.
  - **Servicio de Dominio**:
    - En `authService.ts`, implementar la lógica para generar y validar tokens de sesión, así como para verificar los permisos basados en roles.
    - Este servicio será el encargado de invocar los repositorios de usuario y sesión, manteniendo la lógica de negocio desacoplada del acceso a datos.
  - **Repositorio de Usuarios y Sesiones**:
    - En `userRepository.ts`, implementar las operaciones CRUD para la entidad de usuario, incluyendo la verificación de credenciales durante el inicio de sesión.
    - En `sessionRepository.ts`, gestionar la creación y eliminación de sesiones de usuario, y almacenar los tokens en una ubicación segura.
  - **Manejo de Roles**:
    - Definir roles en la base de datos (e.g., `recruiter`, `admin`) y asegurarse de que los permisos de acceso se basen en estos roles.
    - Implementar una verificación de roles en los endpoints protegidos, de modo que solo los usuarios autorizados puedan acceder.

- **Pasos de Implementación** (TDD aplicado):
  1. Escribir pruebas de integración en Jest para verificar que los endpoints de autenticación (`/api/auth/login`, `/api/auth/validate`, `/api/auth/logout`) funcionen correctamente.
  2. En `authRoutes.ts`, configurar las rutas POST `/login`, GET `/validate`, y POST `/logout`.
  3. En `authController.ts`, implementar la lógica de autenticación, invocando el servicio de dominio para la generación y validación de tokens.
  4. En `authService.ts`, implementar la generación de tokens, validación de sesión y verificación de roles, utilizando los repositorios de usuario y sesión.
  5. En `userRepository.ts` y `sessionRepository.ts`, implementar las funciones necesarias para gestionar usuarios y sesiones.
  6. Realizar pruebas en la API para verificar que las rutas protegidas solo permiten acceso a usuarios autenticados con los roles adecuados.

- **Criterios de Aceptación**:
  - Los endpoints de autenticación permiten iniciar sesión, validar sesiones y roles, y cerrar sesión correctamente.
  - Las rutas protegidas solo son accesibles para usuarios autenticados con los roles correctos.
  - Los tokens de sesión son generados, almacenados y revocados de forma segura.
  - Los repositorios de usuario y sesión permiten gestionar credenciales y sesiones, asegurando la separación de responsabilidades.

---

### Ticket 3.2: Configurar Rutas, Controladores y Almacenamiento de Información del Candidato, Incluyendo Documentos

- **Descripción**: Configurar las rutas, controladores y almacenamiento en la base de datos necesarios para registrar a un candidato y cargar su CV en una sola solicitud. Esta configuración permitirá recibir y almacenar la información del candidato, junto con su documento, en una única transacción.

- **Especificaciones**:
  - **Ubicación**:
    - **Presentation (API)**: Configurar la ruta en `backend/src/routes/candidateRoutes.ts` para exponer el endpoint de añadir candidato.
    - **Application**: Crear el controlador en `backend/src/controllers/candidateController.ts`, que manejará la validación de la solicitud y coordinará el flujo hacia la capa de dominio.
    - **Domain**: Implementar la lógica de negocio en `backend/src/domain/services/candidateService.ts`, encargada de la validación y coordinación para almacenar el candidato, su documento, educación y experiencia.
    - **Infrastructure**: Centralizar la lógica de almacenamiento en `backend/src/infrastructure/repositories/candidateRepository.ts`, que gestionará tanto los datos del candidato como la carga de documentos, optimizando el proceso en un solo flujo.
  - **Funcionalidad de la Ruta y Almacenamiento**:
    - Configurar una ruta POST (`/api/candidates`) que reciba toda la información del formulario de candidato, incluyendo el archivo de CV, en un solo formulario multipart.
    - Validar que el archivo de CV cumpla con los requisitos de formato (PDF o DOCX) y tamaño (hasta 5 MB).
    - La ruta debe procesar y validar los datos antes de enviarlos a la capa de dominio.
    - En la capa de dominio, el servicio coordina el almacenamiento del candidato, sus registros de educación y experiencia, y el documento en una sola transacción.
  - **Repositorio de Candidato (Con Almacenamiento de Archivos Integrado)**:
    - En `candidateRepository.ts`, implementar funciones CRUD para el candidato, incluyendo el almacenamiento seguro de documentos en el servidor y el registro de su ruta en la base de datos.
    - Utilizar Prisma para gestionar la integridad referencial y el mapeo relacional en los datos de candidatos, educación, experiencia y documentos.
  - **Manejo de Errores**:
    - Validar el tipo y tamaño del archivo de CV antes de almacenarlo.
    - Enviar mensajes de error claros en caso de fallos de validación o problemas en la conexión con la base de datos.
    - Asegurar que el proceso de almacenamiento sea transaccional, de modo que si alguna operación falla, no se guarden datos incompletos.

- **Pasos de Implementación** (TDD aplicado):
  1. Escribir pruebas de integración en Jest para validar que la ruta `/api/candidates` permite recibir y almacenar los datos del candidato, educación, experiencia y documento en una sola solicitud.
  2. En `candidateRoutes.ts`, configurar la ruta POST `/api/candidates`.
  3. En `candidateController.ts`, implementar la función para recibir y validar los datos, y luego llamar al servicio de dominio.
  4. En `candidateService.ts`, coordinar el almacenamiento de los datos en `candidateRepository`.
  5. En `candidateRepository.ts`, integrar la lógica de almacenamiento del archivo de CV, junto con la gestión de datos del candidato y registros relacionados.
  6. Probar la API para asegurar el correcto almacenamiento de datos y manejo de errores en el flujo unificado de registro y carga de documentos.

- **Criterios de Aceptación**:
  - La ruta `/api/candidates` permite el registro completo del candidato y carga del CV en una sola solicitud.
  - El archivo de CV se almacena de forma segura y se relaciona con el candidato correspondiente en la base de datos.
  - Los mensajes de error son específicos y claros tanto para problemas en datos de candidatos como de documentos.
  - El flujo de almacenamiento es transaccional, asegurando que no se guarden datos incompletos en caso de error.

---

Con esta organización, los tickets están estructurados de forma coherente y reflejan el flujo optimizado para el registro de candidatos y la carga de documentos en una sola transacción. Avísame si necesitas alguna modificación o ajuste final.