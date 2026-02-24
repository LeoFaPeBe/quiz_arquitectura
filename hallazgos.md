FASE 1: 
AuthController.java

| # | Descripción del problema                                                          | Archivo             | Línea aprox. | Principio violado               | Riesgo |
| - | --------------------------------------------------------------------------------- | ------------------- | ------------ | ------------------------------- | ------ |
| 1 | Nombres de variables poco descriptivos (`s`, `u`, `p`, `e`)                       | AuthController.java | 13, 20, 26   | Clean Code (Naming)             | Bajo   |
| 2 | Uso de `throws Exception` genérico en endpoints                                   | AuthController.java | 20, 26       | Clean Code / Robustez           | Medio  |
| 3 | Uso de `Map<String,Object>` como respuesta en lugar de DTO                        | AuthController.java | 19, 25       | SRP / Clean Architecture        | Medio  |
| 4 | Uso de `@RequestParam` para credenciales en lugar de `@RequestBody`               | AuthController.java | 20–26        | Seguridad / Diseño REST         | Medio  |
| 5 | Posible exposición de datos sensibles si el service devuelve información completa | AuthController.java | 21, 27       | Seguridad (exposición de datos) | Alto   |
| 6 | Falta de validación de entrada (null, vacío, longitud mínima)                     | AuthController.java | 20–26        | Seguridad básica                | Medio  |

User.Java

| #  | Descripción del problema                          | Archivo   | Línea aprox. | Principio violado                 | Riesgo |
| -- | ------------------------------------------------- | --------- | ------------ | --------------------------------- | ------ |
| 7  | Atributos públicos (violación de encapsulamiento) | User.java | 4–6          | Clean Code / OOP                  | Alto   |
| 8  | Password almacenado como String plano             | User.java | 6            | Seguridad básica                  | Alto   |
| 9  | Clase anémica sin validaciones ni comportamiento  | User.java | 3–7          | SRP mal aplicado / Modelo anémico | Medio  |
| 10 | No hay separación entre entidad y DTO             | User.java | —            | Arquitectura / SRP                | Medio  |

UserRepository.java

| #  | Descripción del problema                                 | Archivo             | Línea aprox.   | Principio violado            | Riesgo     |
| -- | -------------------------------------------------------- | ------------------- | -------------- | ---------------------------- | ---------- |
| 11 | Credenciales de BD hardcodeadas                          | UserRepository.java | 11–13          | Seguridad básica             |  Alto    |
| 12 | Uso de `Statement` con concatenación → SQL Injection     | UserRepository.java | 18, 32         | Seguridad (SQL Injection)    | Crítico |
| 13 | No se usa `PreparedStatement`                            | UserRepository.java | 17–33          | Seguridad / Buenas prácticas |  Alto    |
| 14 | No se cierran conexiones, statements ni resultsets       | UserRepository.java | 16–27, 29–34   | Resource Management          |  Alto    |
| 15 | Conexión creada manualmente (no usa DataSource / Spring) | UserRepository.java | 16, 30         | DIP / Arquitectura           |  Medio   |
| 16 | Manejo de password en texto plano                        | UserRepository.java | 23, 32         | Seguridad                    |  Alto    |
| 17 | Violación fuerte de SRP (gestión conexión + SQL + mapeo) | UserRepository.java | Clase completa | SRP (SOLID)                  |  Medio   |
| 18 | Retorna `null` en vez de Optional                        | UserRepository.java | 27             | Clean Code                   |  Bajo    |

AuthService.java

| #  | Descripción del problema                                  | Archivo             | Línea aprox.   | Principio violado             | Riesgo     |
| -- | --------------------------------------------------------- | ------------------- | -------------- | ----------------------------- | ---------- |
| 1  | SQL Injection por concatenación                           | UserRepository.java | 18, 32         | Seguridad (OWASP)             |  Crítico |
| 2  | Credenciales de BD hardcodeadas                           | UserRepository.java | 11–13          | Seguridad básica              |  Alto    |
| 3  | No se cierran conexiones JDBC                             | UserRepository.java | 16–34          | Resource Management           |  Alto    |
| 4  | Uso de MD5 para hashing de password                       | AuthService.java    | 57             | Seguridad criptográfica       |  Crítico |
| 5  | Password almacenado en texto plano (modelo público)       | User.java           | 6              | Seguridad                     |  Alto    |
| 6  | Se devuelve el hash al cliente                            | AuthService.java    | 27, 32         | Exposición de datos sensibles |  Alto    |
| 7  | Campos públicos en entidad                                | User.java           | 4–6            | Encapsulamiento (OOP)         |  Alto    |
| 8  | Uso de `Statement` en vez de `PreparedStatement`          | UserRepository.java | 17–33          | Buenas prácticas              |  Alto    |
| 9  | Uso de `throws Exception` genérico                        | Controller/Service  | varios         | Clean Code                    |  Medio   |
| 10 | `Map<String,Object>` como contrato de API                 | Controller/Service  | varios         | SRP / Diseño API              |  Medio   |
| 11 | Uso de `System.out.println` en producción                 | AuthService.java    | 24–31, 46–51   | Clean Code                    |  Bajo    |
| 12 | No hay validaciones reales de entrada                     | Controller/Service  | varios         | Seguridad básica              |  Medio   |
| 13 | Uso de `@RequestParam` para credenciales                  | AuthController.java | 20–26          | Diseño REST                   |  Medio   |
| 14 | Retorno de `null` en repository                           | UserRepository.java | 27             | Clean Code                    |  Bajo    |
| 15 | Violación de SRP en repository (conexión + SQL + mapping) | UserRepository.java | clase completa | SOLID (SRP)                   |  Medio   |
| 16 | Nombres pobres (`u`, `p`, `c`, `r`, `s`)                  | varios              | varios         | Clean Code (Naming)           |  Bajo    |



Respuestas postman

POST "http://localhost:8080/login?u=admin&p=12345"
¿Qué datos sensibles aparecen en la respuesta? ¿Debería retornarse eso?

{
    "ok": true,
    "user": "admin",
    "hash": "827ccb0eea8a706c4c34a16891f84e7b"
}

