<properties title="DocumentDB limits for the preview release" pageTitle="Límites de la Base de datos de documentos para la versión preliminar | Azure" description="Learn about the limits and quota enforcements of DocumentDB for the preview release." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />


#Límites de la Base de datos de documentos para la versión preliminar
La siguiente tabla describe los límites y aplicaciones de cuota de la Base de datos de documentos durante la versión preliminar. En la mayoría de los casos los límites se aplican con la intención de obtener sus comentarios o según las restricciones de capacidad actuales. Si tiene la necesidad empresarial de flexibilizar los límites, llámenos y haremos todo lo posible para adaptarnos a las restricciones de la oferta pública.    

|Entidad |Cuota (Oferta estándar para la versión preliminar)|
|-------|--------|
|Cuentas de la base de datos     |5
|Número de bases de datos por cuenta de base de datos     |100
|Número de usuarios por cuenta de base de datos en todas las bases de datos |500 000
|Número de permisos por cuenta de base de datos en todas las bases de datos   |2 000 000
|Almacenamiento anexo por cuenta de base de datos      |2 GB
|Número máximo de unidades de capacidad por cuenta de base de datos       |5
|Número de colecciones por unidad de capacidad      |3
|Almacenamiento asignado mínimo por colección con 1 documento como mínimo    |3,3 GB
|Rendimiento asignado mínimo por colección con 1 documento como mínimo |667 RU
|Elasticidad de una colección    |0-10 GB
|Unidades de solicitud/s máximos por colección   |2000
|Número de procedimientos almacenados, desencadenadores y UDF por colección       |25 cada uno
|Tiempo de ejecución máximo para el procedimiento almacenado y desencadenador     |5 segundos
|Almacenamiento de documentos/unidad de capacidad aprovisionados |10 GB
|Unidades de solicitud/s/unidad de capacidad aprovisionadas     |2000
|Almacenamiento de documentos máximo por base de datos (5 unidades de capacidad)    |50 GB
|Longitud máxima de la propiedad de identificador    |255 caracteres
|Número predeterminado de elementos por página     |100
|Elementos máximos por página        |1000
|Tamaño de solicitud máximo del documento y el adjunto       |256 KB
|Tamaño de solicitud máximo del procedimiento almacenado, desencadenador y UDF        |256 KB
|Tamaño máximo de respuesta |1 MB
|Número máximo de rutas exclusivas por colección       |100
|Cadena |Todas las cadenas se deben ajustar a la codificación UTF-8. Dado que UTF-8 es una codificación de ancho variable, los tamaños de las cadenas se determinan mediante los bytes UTF-8.
|Longitud máxima de la propiedad o valor  |Sin límite práctico
|Número máximo de UDF por consulta     |1
|Número máximo de JOIN por consulta    |2
|Número máximo de cláusulas AND por consulta      |5
|Número máximo de cláusulas OR por consulta       |5
