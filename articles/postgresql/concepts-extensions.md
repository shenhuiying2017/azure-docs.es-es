---
title: Uso de extensiones de PostgreSQL en Azure Database for PostgreSQL | Microsoft Docs
description: Describe la capacidad de ampliar la funcionalidad de su base de datos mediante extensiones en Azure Database for PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: f02588495e7107b34dac7e076cf3612de12b51d4
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensiones de PostgreSQL en Azure Database for PostgreSQL
PostgreSQL ofrece la capacidad de ampliar la funcionalidad de su base de datos mediante extensiones. Las extensiones permiten agrupar varios objetos SQL relacionados en un solo paquete que se puede cargar o quitar de la base de datos con un solo comando. Después de cargarse en la base de datos, las extensiones pueden funcionar de la misma forma que las características integradas. Para obtener más información acerca de las extensiones de PostgreSQL, consulte la información sobre el [empaquetado de objetos relacionados en una extensión](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>¿Cómo se utilizan las extensiones de PostgreSQL?
Para poder usar las extensiones de PostgreSQL, es preciso que estén instaladas en su base de datos. Para instalar una extensión determinada, ejecute el comando [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) desde la herramienta psql para cargar los objetos empaquetados en la base de datos.

Azure Database for PostgreSQL actualmente admite un subconjunto de extensiones de claves, como se enumeran a continuación. Aparte de las que se indican, no se admiten otras extensiones. No puede crear su propia extensión con el servicio Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensiones admitidas por Azure Database for PostgreSQL
En las tablas siguientes se enumeran las extensiones estándar de PostgreSQL que Azure Database for PostgreSQL admite actualmente. Esta información también está disponible mediante la consulta de `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Extensiones de tipos de datos

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Proporciona un tipo de datos para las contraseñas de cifrado automático. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Proporciona un tipo de cadena de caracteres que no distingue entre mayúsculas y minúsculas. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Proporciona un tipo de datos para los cubos multidimensionales. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Proporciona un tipo de datos para almacenar conjuntos de pares clave/valor. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Proporciona tipos de datos para los estándares internacionales de numeración de productos. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Proporciona un tipo de datos para las estructuras de árbol jerárquicas. |

### <a name="functions-extensions"></a>Extensiones de funciones

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Proporciona un medio para calcular distancias de círculo máximo en la superficie de la Tierra. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Proporciona varias funciones para determinar las similitudes y la distancia entre las cadenas. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Proporciona funciones y operadores para manipular matrices de enteros sin valores nulos. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Proporciona funciones de cifrado. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Administra las tablas con particiones por hora o identificador. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Proporciona funciones y operadores para determinar la similitud del texto alfanumérico basado en la coincidencia de trigrama. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Proporciona funciones que manipulan la totalidad del contenido de las tablas, incluidas tablas de referencias cruzadas. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificadores únicos universales (UUID). |

### <a name="full-text-search-extensions"></a>Extensiones de búsqueda de texto completo

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Proporciona una plantilla de diccionario de búsqueda de texto para números enteros. |
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
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Lenguaje de procedimientos que puede cargar PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensiones variadas

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Proporciona un medio para examinar lo que sucede en la caché del búfer compartido en tiempo real. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Proporciona una manera de cargar datos de relación en la caché del búfer. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Proporciona un medio para realizar el seguimiento de las estadísticas de ejecución de todas las instrucciones SQL ejecutadas por un servidor. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Proporciona un medio para mostrar información de bloqueo a nivel de fila. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Proporciona un medio para mostrar estadísticas de nivel de tupla. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Se trata de un contenedor de datos externo utilizado para tener acceso a los datos almacenados en los servidores externos de PostgreSQL. |

### <a name="postgis-extensions"></a>Extensiones de PostGIS

> [!div class="mx-tableFixed"]
| **Extensión** | **Descripción** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciales y geográficos para PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Se utilizan para analizar una dirección en los elementos que la componen. Se utilizan para admitir el paso de normalización de la dirección de geocodificación. |
| [pgrouting](http://pgrouting.org/) | Extiende la base de datos geoespacial de PostGIS/PostgreSQL para proporcionar la funcionalidad de enrutamiento geoespacial. |

## <a name="next-steps"></a>Pasos siguientes
Si no ve una extensión que le gustaría utilizar, háganoslo saber. Vote por las solicitudes existentes o cree nuevos comentarios y solicitudes en nuestro [foro de comentarios de clientes](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
