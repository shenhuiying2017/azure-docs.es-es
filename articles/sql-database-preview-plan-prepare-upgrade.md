<properties 
	pageTitle="Planeación y preparación para actualizar a SQL Database V12" 
	description="Describe los preparativos y las limitaciones que conlleva la actualización a SQL Database Update V12 de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# Planeación y preparación para actualizar a SQL Database V12 de Azure


<!--
GeneMi, 05 de marzo de 2015, jueves, 20:06 p.m.
Quite 'Azure' del título.
-->


En este tema se describen la planificación y los preparativos que deben realizarse para actualizar las bases de datos de SQL de Azure de la versión V11 a la versión V12.


Hay un nuevo [portal de Azure](http://portal.azure.com/) disponible para admitir la actualización a V12.


> [AZURE.NOTE]
> Las bases de datos de prueba, copias de la base de datos o bases de datos nuevas son buenas candidatas para la actualización a la vista previa. Las bases de datos de producción de las que depende su negocio deben esperar hasta después del período de la versión de vista previa.<br/><br/>
> Para la gama de la versión V12, puede determinar si en su región geográfica está disponible la versión de vista previa o si hay disponibilidad general. Para ello, consulte la tabla de regiones de [Novedades de la Base de datos de SQL V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable).


En la tabla siguiente se enumeran y describen otros temas de la Ayuda para V12.


| Título y vínculo | Descripción del contenido |
| :--- | :--- |
| [Novedades de la Base de datos de SQL V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | Describe los detalles de cómo V12 acerca de la Base de datos SQL de Azure a la paridad completa con Microsoft SQL Server 2014. |
| [Tutorial: Regístrese para obtener la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | Describe los pasos que debe llevar a cabo si desea actualizar sus bases de datos SQL de Azure a la versión de vista previa más reciente. |
| [Creación de una base de datos en la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | Describe cómo crear una base de datos SQL de Azure nueva que incluye las nuevas características de la vista previa más reciente. Describe varias opciones que van más allá de una base de datos vacía. |
| [Actualización a la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | Describe cómo actualizar las bases de datos SQL de Azure a la vista previa más reciente. Indica que debe hacer una copia de seguridad de la base de datos en primer lugar, porque la actualización es permanente y no se puede deshacer. |


## A. Planéelo por adelantado


En las siguientes subsecciones se describe el aprendizaje y la toma de decisiones que debe abordar antes de realizar acciones para actualizar la base de datos SQL de Azure a V12.

### A.1 Aclaración de versión


Este documento se refiere a la actualización de la base de datos SQL de Microsoft Azure de la versión V11 a la V12. Más formalmente, los números de la versión son próximos a los dos valores siguientes, como informa la instrucción Transact-SQL **SELECT @@version;**:


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(o algo superior, V12)*


### A.2 Planificación del nivel de servicio


A partir de V12, la Base de datos SQL de Azure solo será compatible con los niveles de servicio denominados Basic, Standard y Premium. En V12 no se admite el nivel de servicio Web y Business. Por lo tanto, si tiene previsto actualizar la Base de datos SQL de Azure que está actualmente en el nivel de servicio Web o Business, debe decidir cuál será su nuevo nivel de servicio.


Para obtener información detallada acerca de los niveles de servicio Basic, Standard y Premium, consulte:


- [Actualizar las bases de datos SQL Web o Business a niveles de servicio nuevos](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Precios de la Base de datos SQL de Azure](http://azure.microsoft.com/pricing/details/sql-database/)


### A.3 Revise la configuración de replicación geográfica


Si la base de datos SQL de Azure está configurada para replicación geográfica, debe documentar la configuración actual y detener la replicación geográfica antes de comenzar a preparar la actualización. Una vez completada la actualización debe volver a configurar la base de datos para la replicación geográfica.


La estrategia es dejar intacto el origen y realizar pruebas en una copia de la base de datos.


### A.4 Actualización local frente a copiar la base de datos en un servidor nuevo


Tiene dos técnicas entre las que elegir para actualizar la base de datos SQL de Azure V11 a V12:


- Actualización local: Una vez completado este proceso, la base de datos V11 no existirá. El servidor de la Base de datos SQL está en V12.
- Copia de la base de datos al servidor V12: Este proceso deja la base de datos V11 sin interrupciones y crea una copia actualizada de la base de datos V11 en un servidor de la Base de datos SQL de Azure V12. <br/> Tenga en cuenta que la información DNS que usa su código de cliente para acceder a la base de datos ya no tendrá acceso a la V11 y será necesario editar la información DNS para acceder a la base de datos V11.


## B. Acciones de preparación


Después de completar la planificación, puede realizar los pasos de acción que se describen en las subsecciones siguientes para preparar la fase final de la actualización.


Una descripción detallada de la fase final de la actualización está disponible en los temas de ayuda vinculados en la parte superior de este tema de ayuda.


### B.1 Acciones de nivel de servicio


El nivel de precios de servicio Web y Business no se admite en V12.


Si la base de datos SQL de Azure V11 es una base de datos Web o Business, el proceso de actualización se ofrece para cambiar la base de datos a un nivel compatible. La actualización recomienda un nivel que se ajuste al historial de la carga de trabajo de la base de datos. Sin embargo, puede elegir cualquier nivel compatible que quiera.


Los pasos necesarios durante la actualización se pueden reducir mediante el cambio de la base de datos V11 de los niveles Web y Business antes de iniciar la actualización. Puede hacerlo mediante el portal de Azure.


Si no sabe a qué nivel de servicio cambiar, el nivel S0 del nivel Standard puede ser una elección inicial razonable.


### B.2 Suspensión de la replicación geográfica durante la actualización


No se puede ejecutar la actualización a V12 si la replicación geográfica está activa en la base de datos. Primero debe volver a configurar la base de datos para dejar de usar la replicación geográfica.


Una vez completada la actualización puede configurar la base de datos para volver a usar la replicación geográfica.


##<a id="limitations"></a>C. Limitaciones durante la actualización a V12 y después de ella


Esta sección describe las limitaciones asociadas a la actualización a Base de datos SQL de Azure V12.


### C.1 Portal de vista previa para V12


Solo el primero de los siguientes dos portales de administración de Azure es compatible con las bases de datos de V12:


- [http://portal.azure.com/](http://portal.azure.com/)
 - Este portal más reciente se encuentra en estado de vista previa y no está aún en Disponibilidad general (GA).<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - Este portal más antiguo no se actualizará para admitir V12.


Recomendamos a los clientes que se conecten a sus bases de datos SQL de Azure con Visual Studio 2013 (VS2013). VS2013 se puede usar para tareas como las siguientes:


- Para ejecutar una instrucción T-SQL.
- Para diseñar un esquema.
- Para desarrollar una base de datos, en línea o sin conexión.


En su lugar, puede conectarse con la [Comunidad de Visual Studio 2013](https://www.visualstudio.com/es-es/news/vs2013-community-vs.aspx/), que es una versión gratuita y completa de VS2013.


En el portal de administración de Azure anterior, en la página de la base de datos, puede hacer clic en **Abrir en Visual Studio** para iniciar VS2013 en el equipo para la conexión con la base de datos SQL de Azure.


Para otra alternativa, puede utilizar SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/) para conectarse a la base de datos SQL de Azure. Encontrará más detalles en esta publicación de blog:<br/>[Actualizaciones de herramientas de cliente para la base de datos SQL de Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### C.2 Limitación de la *during* actualización a V12

En la tabla siguiente, se describen las limitaciones que están en vigor durante el proceso de actualización.


| Limitación | Descripción |
| :--- | :--- |
| Duración de la actualización | Para una base de datos muy grande, quizás mayor de 50 GB, el proceso de actualización puede tardar hasta 24 horas. |
| Retraso de la actualización de la entrada DNS | Cuando se complete la actualización, el sistema tarda varios minutos en actualizar la entrada DNS para la base de datos V12, para la conectividad desde la aplicación cliente. |
| Sin crear una base de datos | Mientras la actualización está en curso, las siguientes acciones para crear una base de datos no están disponibles en el servidor de la Base de datos SQL de Azure V12 de destino: <p></p> * Creación de una base de datos nueva <br/> * Copia de una base de datos en el servidor <br/> * Restauración de una base de datos eliminada <br/> * Restauración de una base de datos a un momento específico <br/><br/> Sin embargo, la compatibilidad con restauración a un momento específico es una característica que es posible que se admita antes del final del período de vista previa V12. |
| Sin replicación geográfica | No se admite la replicación geográfica en un servidor V12 que está implicado actualmente en una actualización desde V11. |
| Las reglas de alertas | Las reglas de alerta no se puede agregar a una base de datos V12. |


### C.3 Limitación *after* actualización a V12

En la tabla siguiente, se describen las limitaciones que se aplican durante el proceso de actualización.


| Limitación | Descripción |
| :--- | :--- |
| DNS para la base de datos V11 | Después de completar una actualización mediante la técnica de copiar la base de datos V11 a un nuevo servidor V12, la información de DNS que usa el código del cliente para conectarse hace referencia a la base de datos V12. <p> </p> El código del cliente necesitaría usar información DNS editada para hacer referencia a la base de datos V11. |
| No se puede revertir a V11 | Después de una actualización local, el resultado no se puede revertir o deshacer. |
| Nivel Web o Business | Cuando se inicia la actualización, el servidor para la nueva base de datos V12 ya no puede reconocer ni aceptar el nivel de servicio Web o Business. |
| 50 % de descuento no reflejado en las tarjetas de nivel de precios en el portal de Azure | Durante el período de vista previa, hay un descuento de vista previa del 50 %* en las bases de datos inscritas en la última actualización de vista previa de la base de datos SQL de Azure (V12). Incluso si no se muestra el descuento en el portal de vista previa en el cuadro del nivel de precios de servicio, el descuento está en vigor.<br/><br/> El 50 % de descuento permanece en vigor en todas las regiones geográficas hasta el **31 de marzo de 2015**, cuando caduca para todas las regiones. El descuento se aplica también en las regiones que se han anunciado como de disponibilidad general (GA).<br/><br/> (*El uso de características de la versión más reciente de SQL Database Update V12 de Azure está sujeto a los términos de la versión de vista previa incluidos en su contrato de licencia (por ejemplo, el contrato Enterprise, el de Microsoft Azure o el contrato Microsoft Online Subscription), así como cualquier [término adicional para usarlo en las versiones de vista previa de Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/).  Mientras que dure la versión de vista previa, Microsoft le facturará a usted (o a su revendedor, según corresponda) todas las bases de datos inscritas en esta versión de vista previa a la mitad de la tarifa de disponibilidad general (GA) para conseguir un descuento del 50 % en dicha versión de vista previa. Microsoft avisará con 30 días de antelación a través de correo electrónico antes de la expiración del período de la versión de vista previa y de la correspondiente tarifa con descuento. |


### C.4 Exportación e importación *after* actualización a V12


Puede exportar o importar una base de datos V12 usando el [portal web de Azure](http://portal.azure.com/). También puede exportar o importar mediante cualquiera de las siguientes herramientas:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Marco de trabajo de la aplicación de capa de datos (DacFx)


Sin embargo, para utilizar las herramientas, primero debe instalar las actualizaciones más recientes para asegurarse de que sean compatibles con las nuevas características de V12:


- [Actualización acumulativa 6 para SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Actualización de febrero de 2015 para Herramientas de la Base de datos SQL Server en Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Marco de trabajo de la aplicación de capa de datos (DacFx) de febrero de 2015 para la Base de datos SQL Server V12 de Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Los vínculos anteriores de la herramienta se actualizaron el 2 de marzo de 2015 o después. Se recomienda que utilice estas actualizaciones más recientes de estas herramientas.


#### C.4.1 La función de importación tiene un problema temporal con los archivos bacpac


 (A partir del miércoles, 18 de febrero de 2015)


Hay un problema conocido con la exportación de archivos bacpac desde un servidor de bases de datos SQL de Azure que se haya actualizado a V12. Los archivos bacpac exportados incluirán un objeto errante denominado script_deployment_databases. Los archivos bacpac que contienen este objeto errante no se pueden importar mediante las herramientas SQL Server Management Studio (SSMS), SqlPackage.exe ni la API de Data-Tier Application Framework (DacFx).


Sin embargo, el portal web de Azure se puede usar para importar un archivo bacpac afectado a la base de datos SQL de Azure. Esperamos lanzar una corrección permanente para este problema en torno al viernes 27 de febrero de 2015, incluida una actualización de las herramientas afectadas. Mientras tanto, póngase en contacto con el Soporte de Microsoft si necesita más ayuda para recuperar un archivo ya exportado cuya extensión de nombre de archivo sea .bacpac.


Además del problema de exportación, un número limitado de servidores y clientes que hayan actualizado recientemente a la versión V12 podría experimentar un error diferente al intentar importar un archivo bacpac. Este error está relacionado con los permisos y es transitorio: normalmente se resuelve en un servidor afectado en el plazo de un día. Esperamos que este problema también se solucione definitivamente durante la semana del lunes 23 de febrero de 2015. Mientras tanto, repita la operación de importación por si tiene éxito en alguna repetición. Póngase en contacto con el Soporte de Microsoft si necesita ayuda adicional para importar un archivo bacpac a una base de datos SQL de Azure.


Si es necesario, puede seguir estos pasos para ponerse en contacto con el Soporte de Microsoft:


1. Acceda al portal de Azure.
2. Haga clic con el botón derecho en el nombre de cuenta, que se encuentra en la esquina superior derecha.
3. En el menú contextual que aparece, haga clic en el elemento para acceder al soporte técnico.
 - El elemento se denomina probablemente **Contacto con el Soporte de Microsoft** o **Ayuda y soporte**.


> [AZURE.NOTE] (Lunes, 2 de marzo de 2015)
> 
> Se ha resuelto el problema descrito en esta sección C.4.1. No se requieren conocimiento o acciones especiales para que los clientes continúen como deseen.
> 
> La importación o la exportación automatizada funciona normalmente con independencia de la antigüedad de su archivo bacpac.
> 
> Los clientes que procesan sus archivos bacpac mediante DacFx deben descargar las herramientas actualizadas. Los vínculos de descarga están disponibles en la sección C.4.


### C.5 Restauración a V12 de una base de datos V11 eliminada

En el siguiente escenario se explica que una base de datos SQL de Azure V11 puede restaurarse en un servidor de base de datos SQL de Azure V12.

1. Suponga que tiene un servidor de Base de datos SQL de Azure de V11. <br/> En el servidor tiene una base de datos en el nivel de servicio Web y Business obsoleto.
2. Elimina la base de datos.
3. Actualiza el servidor a V12.
4. A continuación, restaura la base de datos en el servidor. <br/> La base de datos, por tanto, se actualiza a V12 en el nivel S0 del nivel de servicio Standard.
5. Puede cambiar la base de datos a cualquier nivel de servicio compatible, si S0 no es su preferencia.



## D. Resolución de errores

Si la actualización falla por algún extraño motivo, la base de datos V11 permanece activa y disponible como siempre.


## Vínculos relacionados

- [Características de la versión de vista previa](http://azure.microsoft.com/services/preview/) de Microsoft Azure


<!--Anchors-->
[Subtítulo 1]: #subheading-1
<!--HONumber=47-->
