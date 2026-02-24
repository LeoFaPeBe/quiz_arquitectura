ADR-001: Refactorización del módulo de autenticación por vulnerabilidades críticas de seguridad y violaciones a SOLID
Contexto

El sistema actual implementa un módulo de autenticación compuesto por AuthController, AuthService, UserRepository y el modelo User. Su objetivo es permitir registro, login y exponer un endpoint de salud (/health). Sin embargo, la auditoría técnica identificó múltiples vulnerabilidades críticas y problemas estructurales que comprometen la seguridad, mantenibilidad y calidad del código.

Entre los hallazgos más graves se encuentran: vulnerabilidad a SQL Injection por uso de Statement con concatenación de strings, uso de MD5 para hashing de contraseñas, exposición del hash al cliente, almacenamiento de contraseñas sin protección adecuada, credenciales de base de datos hardcodeadas y falta de cierre de conexiones JDBC. Además, se detectaron violaciones a principios SOLID (especialmente SRP y DIP), uso de estructuras dinámicas (Map<String,Object>) como contrato de API y un modelo con atributos públicos que rompe encapsulamiento.

Estos problemas representan un riesgo alto para el negocio, ya que podrían permitir robo de credenciales, acceso no autorizado y filtración de datos sensibles. El equipo de desarrollo también se ve afectado debido a la baja mantenibilidad y alto acoplamiento del código. La situación requiere intervención inmediata antes de considerar el sistema apto para entornos productivos.

Decisión

Se decide realizar una refactorización estructural del módulo de autenticación aplicando principios de seguridad moderna, Clean Code y SOLID.

1. Eliminación de SQL Injection

Se reemplazará el uso de Statement por PreparedStatement o preferentemente JdbcTemplate/Spring Data JPA.
Esto elimina la concatenación manual de queries y aplica el principio de mínima exposición, delegando la sanitización al driver JDBC.

2. Sustitución de MD5 por BCrypt

Se reemplazará el algoritmo MD5 por BCrypt mediante PasswordEncoder de Spring Security.
La decisión se basa en que BCrypt incorpora salt automático y es resistente a ataques de fuerza bruta. No se elige SHA-256 porque no está diseñado específicamente para hashing de contraseñas.

3. Separación de capas y aplicación de SRP

Se reestructurará el módulo de la siguiente manera:

Controller → solo manejo HTTP

Service → lógica de negocio

Repository → acceso a datos

DTOs → contratos de entrada y salida

Entity → modelo persistente encapsulado

Esto elimina el uso de Map<String,Object> y evita exposición accidental de campos sensibles.

4. Externalización de configuración sensible

Las credenciales de base de datos se moverán a application.properties o variables de entorno, eliminando secretos hardcodeados.

5. Encapsulamiento del modelo

La clase User tendrá atributos privados, getters/setters controlados y nunca expondrá el password en respuestas.

La arquitectura resultante será una arquitectura en capas limpia, con bajo acoplamiento, contratos explícitos y responsabilidades claras.

Consecuencias
Consecuencias positivas

Eliminación de vulnerabilidades críticas (SQL Injection y hashing inseguro)

Cumplimiento de buenas prácticas OWASP

Mayor mantenibilidad y claridad en responsabilidades

Contratos de API tipados y más robustos

Mejor testabilidad gracias a menor acoplamiento

Código más alineado con estándares empresariales

Consecuencias negativas / riesgos

Incremento en el tiempo de desarrollo debido al refactoring

Posibles regresiones si no se implementan pruebas adecuadas

Necesidad de migrar contraseñas existentes (si hubiera usuarios ya registrados)

Curva de aprendizaje si el equipo no domina Spring Security

Alternativas consideradas
1. Corregir solo los problemas de seguridad sin refactorizar arquitectura

Descartado porque mantener la estructura actual seguiría violando SRP y Clean Code, generando deuda técnica que reaparecería en el futuro.

2. Reescribir el módulo desde cero

Descartado porque implicaría mayor costo y riesgo de introducir nuevos errores. La refactorización incremental permite mejorar la calidad sin perder funcionalidad existente.

3. Mantener MD5 pero agregar “salt manual”

Descartado porque MD5 está criptográficamente roto y no es recomendado para hashing de contraseñas en sistemas modernos.