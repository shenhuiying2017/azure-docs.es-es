<properties 
    pageTitle="Herramientas de bases de datos elásticas de Base de datos SQL de Azure" 
    description="Escale fácilmente los recursos de las bases de datos en la nube con las herramientas de bases de datos elásticas." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/24/2015" 
    ms.author="sidneyh"/>

# Herramientas de bases de datos elásticas de Base de datos SQL de Azure

## Promesas y desafíos

Base de datos SQL de Azure ofrece recursos de base de datos prácticamente sin límites, así como la elasticidad de cargas de trabajo transaccionales. La habilitación de esta funcionalidad se facilita mediante características como la **biblioteca de cliente de bases de datos elásticas** y la **herramienta de división y combinación** conocidas, en conjunto, como **herramientas de bases de datos elásticas**. Estos componentes están diseñados para simplificar el desarrollo y la administración de soluciones de base de datos particionadas, lo que se analiza en este artículo.

A pesar que lograr la elasticidad y el escalado en las aplicaciones en la nube ha sido sencillo para el almacenamiento de blobs y proceso, el procesamiento de datos con estado en las bases de datos relacionales sigue siendo un desafío. Hemos visto que estos desafíos surgen principalmente en los dos escenarios siguientes:

* Aumento y disminución de la capacidad de la parte de la base de datos relacional de la carga de trabajo.
* La administración de las zonas activas puede afectar a un subconjunto específico de datos, como un usuario final (inquilino) particularmente ocupado.

Tradicionalmente, escenarios similares se han abordado a través de la inversión en servidores de base de datos a gran escala para admitir la aplicación. Sin embargo, esta opción está limitada en la nube donde todo el procesamiento ocurre en hardware estándar predefinido. En su lugar, la distribución de datos y el procesamiento a través de muchas bases de datos con una estructura idéntica (un patrón de escalado horizontal conocido como particionamiento) proporciona una alternativa atractiva a los enfoques de escalado vertical tradicionales, tanto en términos de costo como de elasticidad.

Las características de una base de datos elástica de Base de datos SQL de Azure simplifican el escalado horizontal y permiten que sea más flexible que nunca. Si desarrolla aplicaciones de software como servicio, los **grupos de bases de datos elásticas** facilitan la creación de bases de datos individuales para cada uno de los usuarios finales o inquilinos, y permiten que cada uno aumente o reduzca de manera dinámica su superficie de recursos automáticamente, mientras se mantiene un presupuesto predecible. Los **trabajos de base de datos elástica** le permiten realizar con confianza operaciones de administración a escala en todo un conjunto de bases de datos a través de la ejecución de scripts de T-SQL, para realizar así cambios de esquema o cualquier otra operación de mantenimiento de base de datos que elija. Si desea obtener más información acerca de los trabajos de base de datos elástica, consulte [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

Además, ya sea que use bases de datos independientes para cada inquilino o empaquete varios intervalos de datos en cada una de las bases de datos de una recopilación, la biblioteca de cliente de bases de datos y la herramienta de división y combinación reducen el esfuerzo que se requiere para compilar y administrar aplicaciones que utilicen el particionamiento. En lugar de escribir código para documentar el diseño de datos en todas las bases de datos y enrutar las conexiones a la ubicación adecuada, la biblioteca de cliente le permite centrarse en la lógica de negocios de la aplicación.

## Escalado horizontal y vertical
La ilustración siguiente muestra la dimensión horizontal y la dimensión vertical del escalado.
 
![Escalado horizontal frente a vertical][4]

Escalar horizontalmente es agregar o quitar bases de datos con el fin de ajustar la capacidad o el rendimiento general. Normalmente se denomina como "escalado horizontal". El particionamiento es un enfoque común para el escalado horizontal, en el que los datos se particionan en toda una recopilación de bases de datos con estructura idéntica.

Escalar verticalmente es aumentar o disminuir el nivel de rendimiento de una base individual; también se conoce como "escalado vertical".

La mayoría de las aplicaciones de bases de datos a escala de la nube usarán una combinación de estas dos estrategias. Por ejemplo, una aplicación de software como servicio podría utilizar escalado horizontal para aprovisionar usuarios finales nuevos y escalado vertical para permitir que la base de datos de cada cliente final aumente o disminuya los recursos, según lo requiera la carga de trabajo.

Las herramientas de bases de datos elásticas simplifican la compilación de aplicaciones que se basan en el particionamiento y el control de la infraestructura de escalado horizontal. Además, mediante el uso de un grupo de bases de datos elásticas para su familia de bases de datos, el sistema controla automáticamente el escalado vertical. Con los grupos, establecer límites en el sistema y en el intervalo de variación que desea permitir por base de datos es su responsabilidad. También puede cambiar manualmente las ediciones de la base de datos o los niveles de recursos para las bases de datos que no participan en los grupos de bases de datos elásticas. Por ejemplo, podría crear una nueva partición para gestionar una entrada masiva de datos al final de un mes. Mientras llegan los nuevos datos, la partición se escala verticalmente y luego se vuelve a reducir verticalmente a medida que la entrada disminuye.

Si desea obtener más información acerca de los grupos de bases de datos elásticas, consulte [Información general de los grupos de bases de datos elásticas](sql-database-elastic-pool.md).

## Funcionalidades de las herramientas de bases de datos elásticas 

Desarrollar, escalar y administrar aplicaciones de escalado horizontal mediante particionamiento presenta desafíos para el desarrollador y para el administrador. Las herramientas de bases de datos elásticas facilitan las operaciones de estos dos roles. Los números de la ilustración que aparece a continuación describen las funciones principales que brinda la biblioteca de cliente de bases de datos elásticas y la herramienta de división y combinación. La ilustración muestra un entorno con muchas bases de datos, donde cada base de datos corresponde a una partición. Las herramientas facilitan el desarrollo de aplicaciones de Base de datos SQL de Azure particionadas a través de las siguientes funcionalidades específicas:

Si desea obtener las definiciones de los términos que se usan aquí, consulte [Glosario de Escalado elástico](sql-database-elastic-scale-glossary.md).

![Capacidades de Escalado elástico][1]

1.  **Administración de mapas de particiones**: es la capacidad que tiene una aplicación de administrar distintos metadatos acerca de sus particiones. La administración de mapas de particiones es una característica de la biblioteca de cliente de bases de datos elásticas. Los desarrolladores pueden usar esta funcionalidad para registrar bases de datos como particiones, describir las asignaciones de claves de particionamiento individuales o intervalos de claves para esas bases de datos y mantener estos metadatos a medida que la cantidad y la composición de bases de datos evoluciona para reflejar los cambios en la capacidad. La administración de mapa de particiones constituye una gran parte del código de texto estándar que los clientes tenían que escribir en sus aplicaciones cuando implementaban el particionamiento. Para obtener más información, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md).
 
* **Enrutamiento dependiente de los datos**:imagine que la aplicación recibe una solicitud. Según el valor de clave de partición de la solicitud, la aplicación necesita determinar la base de datos correcta que contiene los datos para este valor de clave y luego abrir una conexión a ella para procesar la solicitud. El enrutamiento dependiente de los datos proporciona la capacidad de abrir conexiones con una sola llamada simple al mapa de particiones de la aplicación. El enrutamiento dependiente de los datos era otra área del código de infraestructura que ahora está cubierta por la funcionalidad de la biblioteca de cliente de bases de datos elásticas. Para obtener más información, consulte [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md).

* **Consultas a través de particiones múltiples (MSQ)**: las consultas a través de particiones múltiples cuando una solicitud implica varias particiones (o todas). Una consulta a través de particiones múltiples ejecuta el mismo código T-SQL en todas las particiones o en un conjunto de particiones. Los resultados de las particiones implicadas se combinan en un conjunto de resultados global usando la semántica UNION ALL. La funcionalidad que se expone a través de la biblioteca de cliente gestiona muchas tareas, como: administración de conexiones, administración de subprocesos, gestión de errores y procesamiento de resultados intermedios. MSQ puede consultar hasta cientos de particiones. Para obtener más información, consulte [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md).


* **Herramienta de división y combinación**: si elige no utilizar el modelo simple de asignación de bases de datos independientes en un grupo de bases de datos elásticas para cada shardlet (inquilino), es posible que la aplicación necesite redistribuir de manera flexible los datos a través de las bases de datos cuando las necesidades de capacidad fluctúan en conjunto con el dinamismo empresarial. Las herramientas de bases de datos elásticas incluyen una herramienta de división y combinación hospedada por el cliente para reequilibrar la distribución de los datos y administrar las zonas activas para las aplicaciones particionadas en situaciones que también implican el desplazamiento de datos. Se basa en una funcionalidad subyacente para mover shardlets a petición entre distintas bases de datos y se integra con la administración de mapa de particiones para mantener asignaciones coherentes y conexiones de enrutamiento dependiente de los datos precisas. Para obtener más información, consulte [División y combinación con las herramientas de bases de datos elásticas](sql-database-elastic-scale-overview-split-and-merge.md).

En general, los clientes que usan las herramientas de bases de datos elásticas pueden esperar obtener toda la funcionalidad de T-SQL al enviar las operaciones de partición local en lugar de las operaciones entre particiones que tienen su propia semántica.

## Patrones comunes de particionamiento

**Particionamiento**: es una técnica para distribuir grandes cantidades de datos estructurados de manera idéntica entre un número de bases de datos independientes. De uso muy extendido entre desarrolladores de nube que crean ofertas de software como servicio (SAAS) para empresas o clientes finales. Estos clientes finales a menudo se conocen como "Inquilinos". El particionamiento puede ser necesario por diversos motivos:

* La cantidad total de datos es demasiado grande para caber dentro de las restricciones de una base de datos 
* El rendimiento de transacciones de la carga de trabajo total supera la capacidad de una sola base de datos 
* Los inquilinos pueden requerir el aislamiento físico entre sí, por lo que se necesitan bases de datos independientes para cada inquilino 
* Distintas secciones de una base de datos pueden tener que residir en diferentes geografías por motivos de cumplimiento, rendimiento o geopolíticos. 

En otros escenarios, como la recopilación de datos desde dispositivos distribuidos, el particionamiento se puede usar para llenar un conjunto de bases de datos organizadas de manera temporal. Por ejemplo, puede dedicarse una base de datos independiente a cada día o semana. En ese caso, la clave de particionamiento puede ser un entero que representa la fecha (presente en todas las filas de las tablas particionadas) y la aplicación debe enrutar las consultas que recuperan información de un intervalo de fechas al subconjunto de bases de datos que abarca el intervalo en cuestión.
 
El particionamiento funciona mejor cuando todas las transacciones de una aplicación pueden restringirse a un único valor de una clave de particionamiento. De este modo se garantiza que todas las transacciones sean locales con respecto a una base de datos.

Algunas aplicaciones usan el enfoque más simple de crear una base de datos independiente para cada inquilino. Se trata del **patrón de particionamiento de un solo inquilino** que proporciona aislamiento, capacidad de copia de seguridad y restauración, y ajuste de escala de recursos en la granularidad del inquilino. Con el particionamiento de un solo inquilino, cada base de datos se asocia a un determinado valor de identificador de inquilino (o valor de clave de cliente), pero no es necesario que esa clave esté siempre presente en los propios datos. Es responsabilidad de la aplicación enrutar cada solicitud a la base de datos adecuada y la biblioteca de cliente de bases de datos elásticas puede simplificar este proceso.

![Un solo inquilino frente a multiinquilinos][3]

Otros escenarios empaquetan varios inquilinos juntos en bases de datos, en lugar de aislarlos en bases de datos independientes. Se trata de un **patrón de particionamiento multiinquilinos** típico y puede estar impulsado el hecho de que una aplicación administra grandes cantidades de inquilinos muy pequeños. En el particionamiento de varios inquilinos, las filas de las tablas de bases de datos están diseñadas para contener una clave que identifique la clave de particionamiento o el identificador del inquilino. De nuevo, la capa de aplicación es la responsable de enrutar la solicitud de un inquilino a la base de datos adecuada, y esto puede admitirlo la biblioteca de cliente de bases de datos elásticas. Además, es posible usar seguridad en el nivel de fila para filtrar las filas a las que puede tener acceso cada inquilino; si desea obtener detalles, consulte [Aplicaciones multiinquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md). La redistribución de datos entre las bases de datos puede ser necesaria con el patrón de particionamiento multiinquilinos, lo que se ve facilitado por la herramienta de división y combinación de bases de datos elásticas.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/overview.png
[2]: ./media/sql-database-elastic-scale-intro/tenancy.png
[3]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png
[4]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
 

<!---HONumber=July15_HO4-->