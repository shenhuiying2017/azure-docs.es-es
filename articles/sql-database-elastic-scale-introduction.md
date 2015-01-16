<properties title="Azure SQL Database Elastic Scale" pageTitle="Escalado elástico de Base de datos SQL de Azure" description="Permite escalar con facilidad los recursos de bases de datos en la nube con la característica de escala elástica de la base de datos de SQL Azure." metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Escalado elástico de Base de datos SQL de Azure 
Bienvenido a la vista previa pública de Escalado elástico de Base de datos SQL de Azure. 

###Promesas y desafíos
Escalado elástico de Base de datos SQL de Azure ofrece la promesa de la informática en la nube y habilita la capacidad virtualmente sin límites, así como la elasticidad en la plataforma de Base de datos SQL de Azure. Hasta la fecha, los proveedores de servicios en la nube podido cumplir en la mayoría de los aspectos relacionados con la capacidad sin límites del almacenamiento de blobs y de proceso. Sin embargo, la elasticidad sigue siendo un desafío cuando se refiere al procesamiento de datos con estado en la nube, especialmente con la administración de bases de datos relacional. Hemos visto que estos desafíos surgen principalmente en los dos escenarios siguientes: 

* Aumento y disminución de la capacidad de la parte de la base de datos relacional de la carga de trabajo.
* Administración de las zonas activas de utilización para las cargas de trabajo de base de datos con estado y sus datos.

Tradicionalmente, estos escenarios se abordan comprando más hardware que hospeda la capa de datos de la aplicación. Sin embargo, esta opción está limitada en la nube donde todo el procesamiento ocurre en hardware estándar predefinido. El particionamiento, o la distribución de datos y procesamiento entre varias unidades de escalado por motivos de capacidad, proporciona una alternativa atractiva a los enfoques tradicionales de escalado tanto en términos de costo como de elasticidad. En el transcurso de varios años, hemos descubierto que los clientes deben implementar sus propios enfoques de escalabilidad horizontal sobre Base de datos SQL de Azure para tener éxito con el particionamiento. Para algunos clientes, esto significaba administrar cientos o miles de bases de datos de SQL Azure. Eso significaba que una gran cantidad de código en su capa de datos que trata las complejidades del particionamiento en lugar de la lógica de negocios de la aplicación. 

Al trabajar directamente con clientes a lo largo de los años, hemos visto varios patrones de particionamiento que surgen de estos proyectos. Escalado elástico de Base de datos SQL de Azure proporciona bibliotecas de cliente y ofertas de servicio en torno a estos patrones. El Escalado elástico permite desarrollar, escalar y administrar más fácilmente las capas de datos con estado de las aplicaciones de Azure.

Luego puede centrarse en la lógica de negocios de la aplicación en lugar de construir una infraestructura para el particionamiento.  


##Funcionalidades 

Desarrollar, escalar y administrar aplicaciones de escalabilidad horizontal mediante particionamiento presenta desafíos para el desarrollador y para el administrador. El Escalado elástico de Base de datos SQL de Azure facilita la vida para ambos roles. Los números en el gráfico describen las funcionalidades principales proporcionadas con esta versión de vista previa única. 
La parte inferior muestra la capa de datos de la aplicación y la distribución de sus datos a través de varias bases de datos, denominadas particiones. Suponga que varias bases de datos almacenan los datos de varias particiones. 

Si desea obtener las definiciones de los términos que se usan aquí, consulte [Glosario de Escalado elástico](./sql-database-elastic-scale-glossary.md).

###Escalado elástico con particionamiento 

![][1]

La ilustración muestra el programador y el administrador en la izquierda y la derecha. Los clientes pueden esperar obtener toda la funcionalidad de T-SQL al enviar las operaciones de partición local en lugar de las operaciones entre particiones que tienen su propia semántica. 
La versión de vista previa pública de Escalado elástico de Base de datos SQL de Azure hace que sea más fácil desarrollar aplicaciones de Base de datos SQL de Azure particionadas a través de las siguientes funcionalidades específicas: 

* **Administración de mapa de particiones**: la administración de mapa de particiones (1) es la capacidad que tiene una aplicación para administrar distintos metadatos acerca de sus particiones. La administración de mapa de particiones es una característica de la biblioteca de cliente de Escalado elástico. Los desarrolladores pueden usar esta funcionalidad para registrar particiones, describir las asignaciones de claves de particionamiento individuales o intervalos de claves para particiones y mantener estos metadatos a medida que la capa de datos evoluciona para reflejar los cambios en la capacidad. La administración de mapa de particiones constituye una gran parte del código de texto estándar que los clientes tenían que escribir en sus aplicaciones cuando implementaban el particionamiento. Si desea obtener detalles, consulte [Administración de mapa de particiones](./sql-database-elastic-scale-shard-map-management.md)
 
* **Enrutamiento dependiente de datos**: Imagine que la aplicación recibe una solicitud. Según el valor de clave de particionamiento de la solicitud, la aplicación necesita determinar la partición correcta que contiene los datos para este valor de clave de particionamiento y, a continuación, abrir una conexión para esta partición (2) para procesar la solicitud. El enrutamiento dependiente de los datos proporciona la capacidad de abrir conexiones con una sola llamada simple al mapa de particiones de la aplicación. El enrutamiento dependiente de los datos era otra área del código de infraestructura que ahora está cubierta por la funcionalidad de la biblioteca de cliente de Escalado elástico. Si desea obtener detalles, consulte [Enrutamiento dependiente de los datos](./sql-database-elastic-scale-data-dependent-routing.md)

* **Consultas a través de particiones múltiples (MSQ)**: las consultas a través de particiones múltiples cuando una solicitud implica varias particiones (o todas). Una consulta a través de particiones múltiples (3) ejecuta el mismo código T-SQL en todas las particiones o en un conjunto de particiones. Los resultados de las particiones implicadas se combinan en un conjunto de resultados global usando la semántica UNION ALL. La funcionalidad se expone a través de la biblioteca de cliente que controla muchas tareas, las que incluyen: administración de conexiones, administración de subprocesos, control de errores y procesamiento de resultados intermedios. MSQ puede consultar hasta cientos de particiones. Para obtener detalles, consulte [Consultas a través de particiones múltiples](./sql-database-elastic-scale-multishard-querying.md).

* **Elasticidad de partición**: esta funcionalidad permite que los administradores automaticen el escalado vertical (aumentando o reduciendo la edición de una sola partición) y horizontal (agregando o quitando particiones de un mapa de particiones) de su entorno particionado a través de scripts de PowerShell y mediante el servicio Automatización de Azure. Para obtener detalles, consulte [Elasticidad de partición](./sql-database-elastic-scale-elasticity.md).

* **Servicio División y combinación**: cuando las necesidades de capacidad fluctúan en conjunto con el dinamismo empresarial, las aplicaciones debe redistribuir de manera flexible los datos a través de distintas bases de datos (4). El escalado elástico proporciona una experiencia de servicio hospedado en el cliente para aumentar y disminuir la capacidad de la capa de datos y administrar zonas activas para aplicaciones particionadas en situaciones que también implican el desplazamiento de datos. Se basa en una funcionalidad subyacente para mover shardlets a petición entre distintas particiones y se integra con la administración de mapa de particiones para mantener asignaciones coherentes y conexiones de enrutamiento dependiente de los datos precisas. Para obtener detalles, consulte [División y combinación con Escalado elástico](./sql-database-elastic-scale-overview-split-and-merge.md)

##Patrones comunes de particionamiento

El **particionamiento** es una técnica para distribuir grandes cantidades de datos estructurados de manera idéntica en varias bases de datos independientes. De uso muy extendido entre desarrolladores de nube que crean ofertas de software como servicio (SAAS) para empresas o clientes finales. Estos clientes finales a menudo se conocen como "Inquilinos". El particionamiento puede ser necesario por diversos motivos: 

* La cantidad total de datos es demasiado grande para caber dentro de las restricciones de una base de datos  
* El rendimiento de transacciones de la carga de trabajo total supera la capacidad de una sola base de datos 
* Los inquilinos pueden requerir el aislamiento físico entre sí, por lo que se necesitan bases de datos independientes para cada inquilino 
* Distintas secciones de una base de datos pueden tener que residir en diferentes geografías por motivos de cumplimiento, rendimiento o geopolíticos. 

En otros escenarios, como la recopilación de datos desde dispositivos distribuidos, el particionamiento se puede usar para llenar un conjunto de bases de datos organizadas de manera temporal. Por ejemplo, puede dedicarse una base de datos independiente a cada día o semana. En ese caso, la clave de particionamiento puede ser un entero que representa la fecha (presente en todas las filas de las tablas particionadas) y la aplicación debe enrutar las consultas que recuperan información de un intervalo de fechas al subconjunto de bases de datos que abarca el intervalo en cuestión.
 
El particionamiento funciona mejor cuando todas las transacciones de una aplicación pueden restringirse a un único valor de una clave de particionamiento. De este modo se garantiza que todas las transacciones sean locales con respecto a una base de datos. 

Algunas aplicaciones usan el enfoque más simple de crear una base de datos independiente para cada inquilino. Se trata del **patrón de particionamiento de un solo inquilino** que proporciona aislamiento, capacidad de copia de seguridad y restauración, y ajuste de escala de recursos en la granularidad del inquilino. Con el particionamiento de un solo inquilino, cada base de datos se asocia a un determinado valor de identificador de inquilino (o valor de clave de cliente), pero no es necesario que esa clave esté siempre presente en los propios datos. Es responsabilidad de la aplicación enrutar cada solicitud a la base de datos adecuada. 

![][2]

Otros escenarios empaquetan varios inquilinos juntos en bases de datos, en lugar de aislarlos en bases de datos independientes. Este es un **patrón de partición de varios inquilinos** típico y puede estar impulsado por las consideraciones de costo, eficiencia o por el hecho de que una aplicación administra grandes cantidades de inquilinos muy pequeños. En el particionamiento de varios inquilinos, las filas de las tablas de bases de datos están diseñadas para contener una clave que identifique la clave de particionamiento o el identificador del inquilino. De nuevo, la capa de aplicación es la responsable de enrutar la solicitud del inquilino a la base de datos adecuada. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-intro/overview.png
[2]:./media/sql-database-elastic-scale-intro/tenancy.png
