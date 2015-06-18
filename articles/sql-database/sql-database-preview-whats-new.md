<properties 
	pageTitle="Novedades de la Base de datos de SQL V12" 
	description="Describe las mejoras más recientes de la base de datos de SQL Azure que se agregaron en diciembre de 2014 o versiones posteriores." 
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


# Novedades de la Base de datos de SQL V12


<!--
GeneMi, 05 de marzo de 2015, jueves, 20:06 p.m.
Quitar 'Azure' del título del tema, anexar 'V12' (según J.G.).
-->



La versión V12 de la Base de datos SQL de Azure se promovió a Disponibilidad general (GA) en enero de 2015. Con esta actualización principal, la Base de datos SQL proporciona ahora una compatibilidad casi completa con el motor de Microsoft SQL Server. La Base de datos SQL ofrece ahora más rendimiento Premium a los clientes. Estas mejoras ayudan a simplificar las migraciones de aplicaciones de SQL Server a la base de datos SQL y ayudan a los clientes con cargas de trabajo de bases de datos más pesadas.


Un par de regiones geográficas están todavía en el estado de vista previa y no están aún en disponibilidad general. Encuentre su región en la [tabla GA por región](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable). Hasta que su región esté en GA, V12 es la idónea para probar bases de datos en lugar de bases de datos de producción.


**[Suscríbase](https://portal.azure.com) para que la base de datos SQL aproveche esta nueva generación en Microsoft Azure. En primer lugar, necesitará una suscripción a Microsoft Azure. Puede registrarse para conseguir una [prueba gratuita de Azure](http://azure.microsoft.com/pricing/free-trial) y consultar la [información](http://azure.microsoft.com/pricing/details/sql-database) sobre los precios.**


La ruta de acceso para planificar y actualizar las bases de datos V11 a V12 comienza en [Planeación y preparación de la actualización a la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


### Puntos clave destacados


Entre los puntos clave destacados de la base de datos SQL V12 de Azure se incluyen los siguientes:


- La **seguridad** disfruta de la nueva característica de [usuarios en las bases de datos independientes](../sql-database-preview-whats-new/#UsersInContainedDatabases). Las otras dos características son [seguridad de nivel de fila](../sql-database-preview-whats-new/#RowLevelSecurity) y [enmascaramiento de datos dinámicos](../sql-database-preview-whats-new/#DynamicDataMasking), aunque éstas están todavía en vista previa y no están aún en GA.


- **Administración más fácil** de bases de datos grandes para admitir cargas de trabajo más pesadas con consultas paralelas (solo Premium) [partición de tablas](http://msdn.microsoft.com/library/ms187802.aspx)[, indexación en línea](http://msdn.microsoft.com/library/ms188388.aspx), reconstrucción de índices grandes sin preocupaciones por el límite de tamaño de 2 GB eliminado y más opciones en la instrucción [alter database](http://msdn.microsoft.com/library/bb522682.aspx).


- **Compatibilidad con las funciones de programación clave** para impulsar un diseño más sólido de aplicaciones con [integración CLR](http://msdn.microsoft.com/library/ms189524.aspx), [funciones de ventana](http://msdn.microsoft.com/library/ms189461.aspx), [índices XML](http://msdn.microsoft.com/library/bb934097.aspx) y [seguimiento de cambios](http://msdn.microsoft.com/library/bb933875.aspx) para los datos.


- **Rendimiento avanzado** con compatibilidad para [almacén de columnas](http://msdn.microsoft.com/library/gg492153.aspx) en memoria (solo nivel Premium) para data mart y cargas de trabajo analíticas más pequeñas.


- **La supervisión y solución de problemas** se han mejorado con visibilidad de más de 100 nuevas vistas de tabla en un conjunto expandido de vistas de administración de base de datos ([DMV](http://msdn.microsoft.com/library/ms188754.aspx))


- **Nuevo nivel de rendimiento S3 en el nivel Standard:** ofrece más flexibilidad de precios entre Standard y Premium. S3 proporcionará más DTU (unidades de rendimiento de la base de datos) y todas las características disponibles en el nivel Standard.


## 1. Detalles de las nuevas mejoras de V12 explicadas


En esta sección se enumeran y explican las nuevas características de cada categoría.


### 1.1 Categoría: Administración de bases de datos expandida


| Característica | Descripción |
| :--- | :--- |
| . | ***Diciembre de 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Usuarios de bases de datos independientes | Ahora puede crear usuarios en la base de datos independiente sin tener un inicio de sesión correspondiente en la base de datos maestra. Esto simplifica mucho más el traslado de la base de datos a otro servidor. El código de conexión de las aplicaciones cliente es el mismo que si utiliza o no usuarios de la base de datos independiente.<br/><br/>Puede que el uso de esta característica sea necesaria para los clientes que deseen beneficiarse de los contratos de nivel de servicio más garantizados.<br/><br/>Por lo general, recomendamos a los clientes que consideren el uso de esta característica. Sin embargo, es posible que algunos clientes tengan escenarios concretos que hagan que el par tradicional *login+user* sea la mejor opción para usted en este momento.<br/><br/>Para obtener más información, consulte:<br/>- [Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Bases de datos independientes](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partición de tabla | Se eliminaron las limitaciones anteriores de la [partición de tabla](http://msdn.microsoft.com/library/ms190787.aspx). |
| Transacciones grandes | Con V12 ya no está limitado a un máximo de 2 GB de modificaciones de datos en una sola transacción. <br/><br/> Una ventaja es que volver a generar un índice grande ya no está limitado por el límite de tamaño de transacción de 2 GB. Para obtener información general, consulte [Límites de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Generación y regeneración de índices en línea | Antes de V12, la Base de datos SQL de Azure generalmente admitía la cláusula (ONLINE=ON) de la instrucción ALTER INDEX, pero esto no se admitía para los índices de una columna de tipo BLOB. Ahora V12 no admite (ONLINE = ON) incluso para índices en columnas BLOB.<br/><br/> La característica ONLINE habilita consultas para beneficiarse de un índice, incluso cuando se vuelve a generar el índice. |
| Compatibilidad de CHECKPOINT | Con V12 puede emitir la instrucción T-SQL CHECKPOINT para la base de datos. |
| Mejora de ALTER TABLE | Permite que lleven a cabo muchas acciones para alterar columnas mientras la tabla permanece disponible. Para obtener más información, vea [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Mejora de TRUNCATE TABLE | Permite el truncamiento de particiones específicas. Para obtener más información, vea [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Más opciones de ALTER DATABASE | V12 admite más de las opciones que están disponibles en la instrucción ALTER DATABASE. <br/><br/> Para obtener información más detallada, consulte [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) o [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Más comandos DBCC | Hay más comandos DBCC ahora disponibles en V12. Para obtener información más detallada, consulte [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


### 1.2 Categoría: Programación y compatibilidad con aplicaciones


| Característica | Descripción |
| :--- | :--- |
| . | ***Febrero de 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Vista previa de enmascaramiento de datos dinámicos | Cuando se genera un conjunto de filas a partir de una consulta, una directiva de enmascaramiento de datos establecida puede reemplazar partes de los datos con caracteres 'X' para ocultar la información confidencial y protegerla. Una vez completada la operación de enmascaramiento, el conjunto de filas modificado se envía al cliente.<br/><br/>Uno de los usos de ejemplo podría ser enmascarar todos los dígitos de un número de tarjeta de crédito, excepto los últimos.<br/><br/>**NOTA:** Esta característica se encuentra en el estado de vista previa y aún no se ha anunciado la disponibilidad general para su uso en producción.<br/><br/>Para obtener información detallada, consulte [Introducción al enmascaramiento de datos dinámicos de Base de datos SQL de Azure](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/). |
| . | ***Enero de 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Vista previa de la seguridad de nivel de fila (RLS) | **Precaución:** La característica RLS está actualmente en estado *preview* solo, incluso en las regiones geográficas donde V12 está en un estado de disponibilidad general (GA). Hasta que RLS esté en estado de disponibilidad general, no será adecuado todavía para su uso en una base de datos de producción crítica para los negocios.<br/><br/>La nueva instrucción CREATE SECURITY POLICY de T-SQL permite implementar RLS. RLS hace que el servidor de base de datos agregue condiciones que filtran algunas filas de datos antes de que se devuelva un conjunto de filas al usuario que efectúa la llamada.<br/><br/>En el sector, RLS a veces también se denomina "control de acceso específico".<br/><br/>Para ver un ejemplo de código y otras funciones, consulte [Vista previa de la seguridad de nivel de fila](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Diciembre de 2014:*** |
| Funciones de ventana en consultas de T-SQL | A las funciones de ventana de ANSI se accede con la [cláusula OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan tiene una excelente [entrada de blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) que explica más detalladamente las funciones de la ventana y la cláusula OVER. |
| Integración de CLR de .NET | El CLR (Common Language Runtime) de .NET Framework está integrado en V12. <br/><br/> Se admiten solo los ensamblados SAFE que se compilan totalmente en código binario. Para obtener información detallada, consulte [Restricciones del modelo de programación de integración de CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Puede encontrar información sobre esta característica en los temas siguientes: <br/> * [Introducción a la integración de CLR de SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Seguimiento de cambios para datos | El seguimiento de cambios para datos se puede configurar ahora en el nivel de base de datos o tabla. <br/><br/> Para obtener información sobre el seguimiento de cambios, consulte [Acerca del seguimiento de cambios (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Índices XML | V12 le permite utilizar las instrucciones T-SQL CREATE XML INDEX y CREATE SELECTIVE XML INDEX. <br/><br/> Existe información acerca de los índices XML en: <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> *[Crear, modificar y quitar índices XML selectivos](http://msdn.microsoft.com/library/jj670109.aspx) |
| Tabla como montón | V12 permite crear una tabla que no tenga ningún índice agrupado. <br/><br/> Esta característica es especialmente útil para su compatibilidad con la instrucción T-SQL SELECT...INTO que crea una tabla a partir del resultado de una consulta. |
| Roles de la aplicación | Para el control de seguridad y permisos, V12 le permite emitir instrucciones GRANT - DENY - REMOVE frente a [roles de aplicación](http://msdn.microsoft.com/library/ms190998.aspx). |


### 1.3 Categoría: Información de clientes mejorada


| Característica | Descripción |
| :--- | :--- |
| . | ***Diciembre de 2014:*** |
| DMV (vistas de administración dinámica) | Se agregan varias DMV en V12. Para obtener información más detallada, consulte [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Seguimiento de cambios | V12 es totalmente compatible con el seguimiento de cambios. <br/><br/> Para obtener información detallada sobre esta característica, consulte [Habilitar y deshabilitar el seguimiento de cambios (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


### 1.4 Mejoras de rendimiento del nivel de servicio Premium


Estas mejoras de rendimiento se aplican a los niveles P2 y P3 del nivel de servicio Premium.


| Característica | Descripción |
| :--- | :--- |
| . | ***Diciembre de 2014:*** |
| Procesamiento paralelo para consultas | V12 proporciona un mayor grado de paralelismo para las consultas que puede beneficiarse de él. |
| Latencia de E/S más breve | V12 tiene una latencia mucho más breve para las operaciones de entrada/salida. |
| E/S por segundo aumentada | V12 puede procesar una cantidad mayor de entrada/salida por segundo (E/S por segundo). |
| Tasa de registros | V12 puede registrar más cambios de datos por segundo. |


### 1.5 Resumen de mejoras


V12 eleva nuestra base de datos SQL cerca de la compatibilidad completa de características con nuestro producto de SQL Server. V12 se centra en las características más populares y en la compatibilidad con programación. Esto hace que el desarrollo sea más agradable y más eficaz.  Ahora es más fácil migrar las aplicaciones de la base de datos SQL a la nube.


Y en el nivel Premium V12 incorpora mejoras importantes de rendimiento. Algunas aplicaciones experimentarán mejoras extremas en la velocidad de la consulta. Las aplicaciones con cargas de trabajo grandes experimentarán un rendimiento sólido y fiable.


## <a name="V12AzureSqlDbPreviewGaTable"></a>2. Estado de disponibilidad general (GA) de V12 por región


La versión V12 de base de datos SQL de Azure se lanzó en modo de vista previa y pruebas en diciembre de 2014 en las regiones indicadas en la tabla siguiente. El cambio de estado de vista previa a la versión de disponibilidad general (GA) completa se está produciendo en fechas diferentes según la región. En la tabla siguiente, se muestra el estado de versión actual de V12 para cada región.


> [AZURE.NOTE]
> [Los precios](http://azure.microsoft.com/pricing/details/sql-database/) durante la vista previa tuvieron aplicado un descuento. El precio vuelve al nivel de disponibilidad general en todas las regiones el 31 de marzo de 2015.


| Región de Azure | Versión actual<br/>estado de V12 | Fecha de promoción<br/>a GA |
| :--- | :--- | :--- |
| Centro-Sur de EE. UU | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Central EE. UU.: | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Centro-Norte de EE. UU | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Oeste de EE. UU. | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Este de EE. UU. | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Este de EE. UU. 2 | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Asia oriental | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Sudeste asiático | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Oeste de Japón | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Este de Japón | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Europa del Norte | Disponibilidad general (GA) | 29 de enero de 2015 |
| Europa occidental | Disponibilidad general (GA) | 29 de enero de 2015 |
| Sur de Brasil | No disponible | Tercer trimestre de 2015, estimado |
| Australia Oriental | Vista previa | Segundo trimestre de 2015, estimado |
| Sudeste de Australia | Vista previa | Segundo trimestre de 2015, estimado |


Para cualquier región que haya alcanzado la fecha de disponibilidad general, todas las suscripciones nuevas y sus bases de datos posteriores utilizan la arquitectura de servicio V12 y, por tanto, tienen acceso a las características más recientes. En el presente artículo se enumeran las nuevas características que ofrece V12.


Cuando llegue la fecha de disponibilidad general, los clientes con servidores y bases de datos previos a V12 pueden actualizar (o mover) sus bases de datos a la arquitectura de servicio de V12 para usar estas nuevas características en la producción. Las bases de datos V12 deben estar en el [nivel de precios](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-servi Basic, Standard o Premiumce-tiers/).


## 3. Cómo continuar


Puede aprender cómo actualizar las bases de datos SQL de Azure de V11 a V12 en [Planear y preparar la actualización a la versión más reciente de SQL Database Update Preview](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


Los números de versión como V12 hacen referencia al valor devuelto por la siguiente instrucción Transact-SQL:<br/>
**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(o algo superior, V12)*


*Descuento:* Puede usar V12 a un precio con descuento durante el período de vista previa de V12. El nuevo nivel de S3 ofrece 100 DTU (unidades de rendimiento de base de datos) y todas las características disponibles en el nivel Standard por 0,2016 USD/hora. El precio tiene un descuento de hasta 0,1008 USD/hora durante la vista previa de V12. Para obtener información detallada, consulte [Precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).


## 4. Precauciones para la vista previa de V12


Tenga en cuenta las precauciones siguientes con respecto a la actualización y posterior actualización a Azure SQL Database Update V12 (vista previa).


### 4.1 Portal de vista previa para V12


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


### 4.2 Precauciones *during* actualización a V12


> [AZURE.NOTE]
> La base de datos anterior a V12 *remains available* para el acceso a los datos durante la actualización a V12.


- Para las bases de datos de más de 50 GB, la actualización a V12 puede tardar hasta 24 horas.
- Durante la actualización de una base de datos a un servidor de V12 de base de datos SQL de Azure, no se pueden realizar las siguientes acciones en el servidor hasta que se complete la actualización:
 - Crear una base de datos nueva
 - Copiar una base de datos en el servidor
 - Restaurar una base de datos eliminada
 - Restaurar una base de datos a un momento dado
 - Replicación geográfica
- Desde el momento en que finaliza una actualización a V12, el sistema necesita varios minutos para actualizar la entrada del sistema de nombres de dominio (DNS) a la base de datos de V12. La aplicación cliente puede conectarse a la base de datos después de que se produzca la actualización de DNS.
- El nivel de precios de servicio Web y Business no se admite en V12. Tampoco se admitirá en ninguna versión futura.


### 4.3 Precauciones *after* actualización a V12


- Cualquier base de datos que se actualice in situ a V12 no se podrá revertir a la versión anterior.
- El descuento de 50 % del coste para Base de datos SQL de Azure V12 está vigente en todas las regiones geográficas hasta su finalización el martes 31 de marzo de 2015. El descuento se aplica a las regiones, tanto en la vista previa como en el estado de disponibilidad general.


### 4.4 Exportación e importación *after* actualización a V12


Puede exportar o importar una base de datos V12 usando el [portal web de Azure](http://portal.azure.com/). También puede exportar o importar mediante cualquiera de las siguientes herramientas:


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Marco de trabajo de la aplicación de capa de datos (DacFx)


Sin embargo, para utilizar las herramientas, primero debe instalar las actualizaciones más recientes para asegurarse de que sean compatibles con las nuevas características de V12:


- [Actualización acumulativa 6 para SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Actualización de febrero de 2015 para Herramientas de la Base de datos SQL Server en Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Marco de trabajo de la aplicación de capa de datos (DacFx) de febrero de 2015 para la Base de datos SQL Server V12 de Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Los vínculos anteriores de la herramienta se actualizaron el 2 de marzo de 2015 o después. Se recomienda que utilice estas actualizaciones más recientes de estas herramientas.


[2. Estado de disponibilidad general (GA) de V12 por región]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
 