---
title: Uso de extensiones de PostgreSQL en Azure Database for PostgreSQL | Microsoft Docs
description: Describe la capacidad de ampliar la funcionalidad de su base de datos mediante extensiones en Azure Database for PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 4f85e27d01341d3d9d61d1984add32e72a53b6ac
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017

---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensiones de PostgreSQL en Azure Database for PostgreSQL
PostgreSQL ofrece la capacidad de ampliar la funcionalidad de su base de datos mediante extensiones. Las extensiones permiten agrupar varios objetos SQL relacionados en un solo paquete que se puede cargar o quitar de la base de datos con un solo comando. Una vez cargadas en la base de datos, las extensiones pueden funcionar exactamente igual que las características integradas. Para obtener más información acerca de las extensiones de PostgreSQL, consulte la información sobre el [empaquetado de objetos relacionados en una extensión](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>¿Cómo se utilizan las extensiones de PostgreSQL?
Es necesario instalar las extensiones de PostgreSQL para su base de datos antes de utilizarlas. Para instalar una extensión determinada, ejecute el comando [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) desde la herramienta psql para cargar los objetos empaquetados en la base de datos.

Azure Database for PostgreSQL admite un subconjunto de extensiones de claves, como se enumeran aquí. Aparte de las que se indican, no se admiten otras extensiones. No puede crear su propia extensión con el servicio Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensiones admitidas por Azure Database for PostgreSQL
En las tablas siguientes se enumeran las extensiones estándar de PostgreSQL que Azure Database for PostgreSQL admite actualmente. También puede obtener esta información emitiendo la consulta pg\_available\_extensions. 

### <a name="data-types-extensions"></a>Extensiones de tipos de datos

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Proporciona un tipo de cadena de caracteres que no distingue entre mayúsculas y minúsculas. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Proporciona el tipo de datos para almacenar conjuntos de pares clave/valor. |

### <a name="functions-extensions"></a>Extensiones de funciones

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Proporciona varias funciones para determinar las similitudes y la distancia entre las cadenas. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Proporciona funciones y operadores para manipular matrices de enteros sin valores nulos. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Proporciona funciones de cifrado. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Administra las tablas con particiones por hora o identificador. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Proporciona funciones y operadores para determinar la similitud del texto alfanumérico basado en la coincidencia de trigrama. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificadores únicos universales (UUID). |

### <a name="full-text-search-extensions"></a>Extensiones de búsqueda de texto completo

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Un diccionario de búsqueda de texto que quita los acentos (signos diacríticos) de lexemas. |

### <a name="index-types-extensions"></a>Extensiones de tipos de índices

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Proporciona clases de operador GIN de ejemplo que implementan el comportamiento similar al del árbol B para determinados tipos de datos. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Proporciona clases de operador de índice de GiST que implementan el árbol B. |

### <a name="language-extensions"></a>Extensiones de lenguaje

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Lenguaje de procedimientos que puede cargar PL/pgSQL |

### <a name="miscellaneous-extensions"></a>Extensiones variadas

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Proporciona un medio para examinar lo que sucede en la caché del búfer compartido en tiempo real. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Proporciona una manera de cargar datos de relación en la caché del búfer. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Proporciona un medio para realizar el seguimiento de las estadísticas de ejecución de todas las instrucciones SQL ejecutadas por un servidor. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Se trata de un contenedor de datos externo utilizado para tener acceso a los datos almacenados en los servidores externos de PostgreSQL. |

### <a name="postgis"></a>PostGIS

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciales y geográficos para PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Se utilizan para analizar una dirección en los elementos que la componen. Se utilizan para admitir el paso de normalización de la dirección de geocodificación. |
| [pgrouting](http://pgrouting.org/) | Extiende la base de datos geoespacial de PostGIS/PostgreSQL para proporcionar la funcionalidad de enrutamiento geoespacial. |

## <a name="next-steps"></a>Pasos siguientes
¿No ve una extensión que le gustaría utilizar? Díganoslo. Vote las solicitudes existentes o cree nuevos comentarios y deseos en nuestro [foro de comentarios de clientes](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

