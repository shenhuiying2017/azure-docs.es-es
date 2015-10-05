<properties
	pageTitle="Planificación de la actualización a Base de datos SQL V12 | Microsoft Azure"
	description="Describe los preparativos y las limitaciones que conlleva la actualización a la versión V12 de Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2015"
	ms.author="genemi"/>


# Planeación y preparación para actualizar a SQL Database V12


En este tema se describen la planificación y los preparativos que deben realizarse para actualizar las bases de datos de SQL de Azure de la versión V11 a la versión V12.


Hay un nuevo [Portal de vista previa de Azure](http://portal.azure.com/) disponible que admite la actualización a V12.


En la tabla siguiente se enumeran otros temas de Ayuda de V12.


| Título y vínculo | Descripción del contenido |
| :--- | :--- |
| [Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md) | Describe con detalle la forma en que la versión V12 acerca de Base de datos SQL de Azure a la paridad completa con Microsoft SQL Server. |
| [Tutorial: regístrese para obtener la actualización más reciente de Base de datos SQL V12](sql-database-v12-sign-up.md) | Describe los pasos que debe realizar si desea actualizar las bases de datos SQL de Azure a V12. |
| [Creación de una base de datos en la actualización de Base de datos SQL V12](sql-database-create.md) | Describe cómo se puede crear una nueva Base de datos SQL Azure en la versión V12. Describe varias opciones que van más allá de una base de datos vacía. |


## Planificación por adelantado


En las siguientes subsecciones se describe el aprendizaje y la toma de decisiones que debe abordar antes de realizar acciones para actualizar la base de datos SQL de Azure a V12.

### Aclaración de versión


Este documento se refiere a la actualización de la base de datos SQL de Microsoft Azure de la versión V11 a la V12. Más formalmente, los números de versión están próximos a los dos valores siguientes, como informa la instrucción **SELECT @@version;** de Transact-SQL:


- 12\.0.2000.8 *(o algo superior, V12)*
- 11\.0.9228.18 *(V11)*
 - En ocasiones, V11 también se conoce como SAWA v2.

### Planificación del nivel de servicio


A partir de V12, la Base de datos SQL de Azure solo será compatible con los niveles de servicio denominados Basic, Standard y Premium. En V12 no se admite el nivel de servicio Web y Business. Por lo tanto, si tiene previsto actualizar la Base de datos SQL de Azure que está actualmente en el nivel de servicio Web o Business, debe decidir cuál será su nuevo nivel de servicio.


Para obtener información detallada acerca de los niveles de servicio Basic, Standard y Premium, consulte:


- [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-new-service-tiers.md)
- [Precios de Base de datos SQL de Azure](http://azure.microsoft.com/pricing/details/sql-database/)


### Revisión de la configuración de replicación geográfica


Si la base de datos SQL de Azure está configurada para replicación geográfica, debe documentar la configuración actual y detener la replicación geográfica antes de comenzar a preparar la actualización. Una vez completada la actualización debe volver a configurar la base de datos para la replicación geográfica.


La estrategia es dejar intacto el origen y realizar pruebas en una copia de la base de datos.


## Acciones de preparación


Después de completar la planificación, puede realizar los pasos de acción que se describen en las subsecciones siguientes para preparar la fase final de la actualización.


Una descripción detallada de la fase final de la actualización está disponible en los temas de ayuda vinculados en la parte superior de este tema de ayuda.


### Acciones de nivel de servicio


El nivel de precios de servicio Web y Business no se admite en V12.


Si la base de datos SQL de Azure V11 es una base de datos Web o Business, el proceso de actualización se ofrece para cambiar la base de datos a un nivel compatible. La actualización recomienda un nivel que se ajuste al historial de la carga de trabajo de la base de datos. Sin embargo, puede elegir cualquier nivel compatible que quiera.


Los pasos necesarios durante la actualización se pueden reducir mediante el cambio de la base de datos V11 de los niveles Web y Business antes de iniciar la actualización. Puede hacerlo con el nuevo [Portal de vista previa de Azure](http://portal.azure.com/).


Si no sabe a qué nivel de servicio cambiar, el nivel S2 del nivel Standard puede ser una elección inicial razonable. Cualquier nivel inferior tendrá menos recursos que los niveles Web y Business.


### Suspensión de la replicación geográfica durante la actualización


No se puede ejecutar la actualización a V12 si la replicación geográfica está activa en la base de datos. Primero debe volver a configurar la base de datos para dejar de usar la replicación geográfica.


Una vez completada la actualización puede configurar la base de datos para volver a usar la replicación geográfica.


### Cliente en una VM de Azure


Si su programa cliente se conecta a Base de datos SQL V12, mientras el cliente se ejecuta en una máquina virtual (VM) de Azure, debe abrir los siguientes intervalos de puerto en dicha VM:

- 11000-11999
- 14000-14999


Haga clic [aquí](sql-database-develop-direct-route-ports-adonet-v12.md) para obtener más información acerca de los puertos para Base de datos SQL V12. Los puertos son necesarios por las mejoras de rendimiento en Base de datos SQL V12.


##<a id="limitations"></a>Limitaciones durante la actualización a V12 y después de ella


### Portales de V12


Hay tres portales de Azure, y cada uno tiene distintas capacidades con respecto a Base de datos SQL V12.


- [http://portal.azure.com/](http://portal.azure.com/)<br/>Este Portal de vista previa de Azure este nuevo y aún está en estado de vista previa. Este portal aún no está en la disponibilidad general (GA) completa. Este portal:
 - Puede administrar un servidor y una base de datos V12.
 - Puede actualizar una base de datos de V11 a V12.


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Este Portal de Azure puede que acabe por desaparecer. Este portal:
 - Puede administrar un servidor y una base de datos V12.
 - La base de datos de V11 *no* se puede actualizar a V12.


- (http://*yourservername*.database.windows.net)<br/> Portal de administración de Base de datos SQL de Azure
 - No se pueden administrar los servidores de V12


Se recomienda conectarse a las bases de datos SQL de Azure con Visual Studio 2013 (VS2013). VS2013 se puede usar para tareas como las siguientes:


- Ejecutar una instrucción de Transact-SQL.
- Para diseñar un esquema.
- Para desarrollar una base de datos, en línea o sin conexión.


En su lugar, puede conectarse con [Visual Studio Community 2013](https://www.visualstudio.com/es-ES/news/vs2013-community-vs.aspx/), que es una versión gratuita y completa de VS2013.


En el Portal de Azure anterior, en la página de la base de datos, puede hacer clic en **Abrir en Visual Studio** para iniciar VS2013 en el equipo para conectarse a Base de datos SQL de Azure.


Como alternativa, puede utilizar SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/) para conectarse a Base de datos SQL de Azure. En esta publicación de blog:<br/>[Actualizaciones de herramientas de cliente para Base de datos SQL de Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/) encontrará más detalles al respecto.


### Limitación *durante* la actualización a V12


La base de datos V11 sigue estando disponible para el acceso a los datos durante la actualización a V12. Sin embargo, existen algunas limitaciones que deben considerarse.


| Limitación | Descripción |
| :--- | :--- |
| Duración de la actualización | La duración de la actualización depende del tamaño, edición y número de bases de datos del servidor. El proceso de actualización puede tardar horas, o incluso días, en ejecutarse para los servidores, especialmente para aquellos que tiene bases de datos:<br/><br/>* Con más de 50 GB o <br/>* Sin nivel de servicio Premium <br/><br/>La creación de nuevas bases de datos en el servidor durante la actualización también puede aumentar la duración de la actualización. |
| Sin replicación geográfica | No se admite la replicación geográfica en un servidor V12 que está implicado actualmente en una actualización desde V11. |
| La base de datos no está disponible brevemente en la fase final de la actualización a V12 | Las bases de datos que pertenecen a su servidor V11 permanecen disponibles durante el proceso de actualización. Sin embargo, la conexión al servidor y las bases de datos no está disponible brevemente en la fase final, cuando el cambio comienza desde la V11 hasta la V12 lista.<br/><br/>El cambio puede tardar entre 40 segundos y 5 minutos. Para la mayoría de los servidores, el cambio se espera completar en 90 segundos. El cambio con el tiempo aumenta para los servidores que tienen un gran número de bases de datos o cuando las bases de datos tienen grandes cargas de trabajo de escritura. |


### Limitación *después de* la actualización a V12


| Limitación | Descripción |
| :--- | :--- |
| No se puede revertir a V11 | Después de una actualización local, el resultado no se puede revertir o deshacer. |
| Nivel Web o Business | Cuando se inicia la actualización, el servidor para la nueva base de datos V12 ya no puede reconocer ni aceptar el nivel de servicio Web o Business. |



### Exportación e importación *después de* la actualización a V12


Desde el [Portal de vista previa de Azure](http://portal.azure.com/) se puede exportar o importar una base de datos V12. También puede exportar o importar mediante cualquiera de las siguientes herramientas:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Marco de trabajo de la aplicación de capa de datos (DacFx)


Sin embargo, para utilizar las herramientas, primero debe instalar las actualizaciones más recientes para asegurarse de que sean compatibles con las nuevas características de V12:


- [Actualización acumulativa 6 para SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Actualización de febrero de 2015 de Herramientas de Base de datos SQL Server en Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Marco de trabajo de la aplicación de capa de datos de (DacFx) de febrero de 2015 de Base de datos SQL de Azure V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE]Los vínculos anteriores de la herramienta se actualizaron el 2 de marzo de 2015 o después. Se recomienda que utilice estas actualizaciones más recientes de estas herramientas.


#### Exportación automatizada


Exportación automatizada sigue estando disponible como vista previa. Si se usa Exportación automatizada, no se requieren actualizaciones de la herramienta de cliente. Si elige usar el archivo resultante e importarlo a un servidor local, las actualizaciones de herramientas indicadas anteriormente son necesarias para que la importación se realice correctamente.


### Restauración a la V12 de una base de datos V11 eliminada

En el siguiente escenario se explica que una base de datos SQL de Azure V11 puede restaurarse en un servidor de base de datos SQL de Azure V12.

1. Suponga que tiene un servidor de Base de datos SQL de Azure de V11. <br/> En el servidor tiene una base de datos en el nivel de servicio Web y Business obsoleto.
2. Elimina la base de datos.
3. Actualiza el servidor a V12.
4. A continuación, restaura la base de datos en el servidor. <br/> Por tanto, la base de datos se actualiza a V12, al nivel S0 del nivel de servicio Standard.
5. Puede cambiar la base de datos a cualquier nivel de servicio compatible, si S0 no es su preferencia.


### cmdlets de PowerShell


Los cmdlets de PowerShell se pueden usar para iniciar, detener o supervisar una actualización a Base de datos SQL de Azure V12 desde V11, o desde cualquier otra versión anterior a la V12.


Para obtener documentación de referencia acerca de estos cmdlets de Powershell, consulte:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


El cmdlet Stop- significa cancelar, no pausar. La única forma de reanudar una actualización es volver a iniciarla desde el principio. El cmdlet Stop- limpia y libera todos los recursos adecuados.


## Resolución de errores


Si la actualización falla por algún extraño motivo, la base de datos V11 permanece activa y disponible como siempre.


## Vínculos relacionados


- [Características de vista previa](http://azure.microsoft.com/services/preview/) de Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
 

<!---HONumber=Sept15_HO4-->