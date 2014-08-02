<properties linkid="manage-services-how-to-scale-a-sqldb" urlDisplayName="How to scale" pageTitle="How to scale a SQL Database - Azure" metaKeywords="" description="Learn about options for scaling your SQL Database in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Scale a SQL Database Solution" authors="" solutions="" manager="" editor="" />

Escalación de una solución de Base de datos SQL
===============================================

En Azure, la escalabilidad de la base de datos es sinónimo de escalado horizontal, donde la carga de trabajo se redistribuye entre los distintos servidores de mercancía en un centro de datos. El escalado horizontal es una estrategia para solucionar problemas relacionados con el rendimiento o la capacidad de los datos. Una base de datos de gran tamaño en una trayectoria de crecimiento considerable requerirá finalmente una estrategia de escalado horizontal, tanto si algunos o muchos usuarios obtienen acceso a ella.

La forma en la que mejor se consigue el escalado horizontal en Azure es mediante la federación. La federación de Base de datos SQL se basa en el particionamiento horizontal, en donde una o más tablas se dividen por fila y se fragmentan entre los distintos miembros de la federación.

La federación no es la única respuesta a cada problema de escalabilidad. A veces, las características de los requisitos de aplicación o de los datos apuntan a enfoques más sencillos. La siguiente lista muestra las posibles soluciones en orden de complejidad.

Aumento del tamaño de la base de datos
--------------------------------------

Las bases de datos se crean con un tamaño fijo sujeto a un máximo impuesto por cada edición. Para la Web Edition, puede aumentar la base de datos hasta un máximo de 5 gigabytes. En la Business Edition, el tamaño máximo de la base de datos es de 150 gigabytes. La forma más lógica de aumentar la capacidad de datos es cambiar la edición y el tamaño máximo:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

Uso de varias bases de datos y asignación de usuarios
-----------------------------------------------------

En determinados escenarios, puede crear copias de una base de datos y, a continuación, asignar inicios de sesión y usuarios entre cada base de datos. Antes de que la federación fuese una opción, este era un enfoque común para la redistribución de una carga de trabajo. Este enfoque es viable para las bases de datos que usa a corto plazo y que, a continuación, fusiona en una base de datos principal que mantiene en local, y para soluciones que proporcionan datos de solo lectura.

Uso de federaciones
-------------------

Las federaciones en Base de datos SQL se usan para conseguir un rendimiento y una escalabilidad superiores. Una o más tablas en una base de datos se dividen por fila y se fragmentan entre varias bases de datos (miembros de la federación). A este tipo de partición horizontal se le conoce a veces como "particionamiento". Los escenarios principales en los que es útil son aquellos en los que es necesario conseguir una escalación o un rendimiento determinados, o para administrar la capacidad.

Las federaciones se admiten en la Business Edition. Para obtener más información, consulte [Federaciones en Base de datos SQL de Azure (anteriormente SQL Azure)](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) y [Tutorial de Federaciones de bases de datos SQL: DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx).

Consideración de otras formas de almacenamiento
-----------------------------------------------

Recuerde que Azure es compatible con varias formas de almacenamiento de datos, incluido el almacenamiento de tablas y el almacenamiento de blobs. Si no precisa características relacionales, el almacenamiento de tablas o blobs puede resultar más económico.

