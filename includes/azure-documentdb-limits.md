=======
Entidad|Cuota (Oferta estándar)
---|---
Cuentas de base de datos*|5
Número de bases de datos por cuenta de base de datos|100
Número de usuarios por cuenta de base de datos entre todas las bases de datos|500.000
Número de permisos por cuenta de base de datos entre todas las bases de datos|2.000.000
Almacenamiento de datos adjuntos por cuenta de base se datos (Característica en vista previa)|2 GB
Máximo de unidades de solicitud / segundos por colección|2500
Número de procedimientos almacenados, desencadenadores y UDF por colección* |25 cada uno
Tiempo de ejecución máximo para el procedimiento almacenado y desencadenador|5 segundos
Almacenamiento de documentos máximo/colección|10 GB
Máximo de colecciones por cuenta de base de datos*|100
Almacenamiento de documentos máximo por base de datos (100 colecciones)* |1 TB
Longitud máxima de la propiedad de identificador|255 caracteres
Máximo de elementos por página|Sin límite en la práctica
Tamaño máximo de solicitud de documentos y anexos |512KB
Tamaño máximo de solicitud de procedimiento almacenado, desencadenador y UDF|512KB
Tamaño máximo de respuesta|1 MB
Cadena|Todas las cadenas se deben ajustar a la codificación UTF-8. Dado que UTF-8 es una codificación de ancho variable, los tamaños de las cadenas se determinan mediante los bytes UTF-8.
Longitud máxima de propiedad o valor| Sin límite práctico
Número máximo de archivos UDF por consulta*|1
Número máximo de JOIN por consulta*|2
Número máximo de cláusulas AND por consulta*|5
Número máximo de cláusulas OR por consulta*|5
Número máximo de valores por expresión IN*|100
Número máximo de puntos en un argumento de polígono de una consulta ST_WITHIN*|16
Número máximo de creaciones de colección por minuto*|5
Número máximo de operaciones de escala por minuto*|5

Las cuotas que aparecen con un asterisco (*) [se pueden ajustar poniéndose en contacto con el soporte técnico de Azure](../articles/documentdb/documentdb-increase-limits.md).

<!----HONumber=Sept15_HO4-->