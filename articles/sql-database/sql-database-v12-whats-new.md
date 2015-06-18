<properties 
	pageTitle="Novedades de la Base de datos de SQL V12" 
	description="Describe las características más recientes que se han agregado a Base de datos SQL de Azure para V12, pero solo hasta mayo de 2015." 
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
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# Novedades de la Base de datos de SQL V12


Base de datos SQL de Azure V12 de Azure ([en vista previa en algunas regiones](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) ofrece una compatibilidad casi completa con el motor de Microsoft SQL Server. Las recientes mejoras ayudan a simplificar las migraciones de aplicaciones de SQL Server a Base de datos SQL y ayudan al sistema a procesar de forma sólida cargas de trabajo de bases de datos más pesadas.


[Regístrese](https://portal.azure.com) en Base de datos SQL para [iniciar](sql-database-get-started.md) esta nueva generación en Microsoft Azure. En primer lugar, necesitará una suscripción a Microsoft Azure. Puede registrarse para conseguir una [evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial) y consultar la información sobre los [precios](http://azure.microsoft.com/pricing/details/sql-database).


La ruta de acceso para planificar y actualizar las bases de datos de las versiones anteriores a la versión V12 comienza en [Planeación y preparación para realizar la actualización a Base de datos SQL V12](sql-database-v12-plan-prepare-upgrade.md).


> [AZURE.TIP]La página web principal para obtener información acerca de las características nuevas de Base de datos SQL de Azure es nuestra página web "Actualizaciones del servicio", en [http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/). En la página web Actualizaciones del servicio, en el control **Servicios**, seleccione **Base de datos SQL**.


## Información destacada


- El **Portal de vista previa de Azure** está [disponible](http://portal.azure.com/) para crear bases de datos y servidores de Base de datos SQL en la versión V12 u, opcionalmente, en una versión anterior. En el Portal de vista previa de Azure, especifique una base de datos de Base de datos SQL y, a continuación, especifique un servidor de Base de datos SQL que la hospeda.
 - A partir de abril de 2015, se seguirá admitiendo el [Portal de Azure anterior](http://manage.windowsazure.com/).


- **Elección de una versión** del servidor de Base de datos SQL cuando se utiliza el Portal de vista previa de Azure para crear una nueva base de datos. El valor predeterminado es V12, pero puede elegir la versión anterior del servidor de Base de datos SQL.


- La **seguridad** disfruta de la nueva característica de [usuarios en las bases de datos contenidas](sql-database-v12-whats-new.md#UsersInContainedDatabases). Las otras dos características son [seguridad de nivel de fila](sql-database-v12-whats-new.md#RowLevelSecurity) y [enmascaramiento dinámico de datos](sql-database-v12-whats-new.md#DynamicDataMasking), aunque están todavía en vista previa y no están aún en GA.


- **Administración más sencilla** de bases de datos grandes, con el fin de admitir mayores cargas de trabajo con consultas paralelas (solo Premium), [partición de tablas](http://msdn.microsoft.com/library/ms187802.aspx), [indización en línea](http://msdn.microsoft.com/library/ms188388.aspx), regeneraciones de índices grandes sin preocupaciones, ya que se ha quitado el límite de 2 GB y otras opciones del comando [ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx).


- **Compatibilidad con funciones de programación de teclas** para potenciar un diseño de aplicaciones más sólido con [integración con CLR](http://msdn.microsoft.com/library/ms189524.aspx), [funciones de ventana](http://msdn.microsoft.com/library/ms189461.aspx) de Transact-SQL, [índices XML](http://msdn.microsoft.com/library/bb934097.aspx) y [seguimiento de cambios](http://msdn.microsoft.com/library/bb933875.aspx) en los datos.


- **Rendimiento avanzado** con compatibilidad con consultas en el [índice de almacén de columnas](http://msdn.microsoft.com/library/gg492153.aspx) en memoria (solo nivel Premium) para data mart y cargas de trabajo analíticas pequeñas.


- La **supervisión y solución de problemas** se han mejorado con visibilidad de más de cien nuevas vistas de tabla en un conjunto expandido de vistas de administración de base de datos ([DMV](http://msdn.microsoft.com/library/ms188754.aspx))


- **Nuevo nivel de rendimiento S3 en el nivel Standard:** ofrece más flexibilidad de [precios](sql-database-upgrade-new-service-tiers.md) entre los niveles Standard y Premium. S3 proporcionará más DTU (unidades de rendimiento de la base de datos) y todas las características disponibles en el nivel Standard.


## Mejoras de V12: administración de bases de datos expandida


| Característica | Descripción |
| :--- | :--- |
| . | ***1 de mayo de 2015:*** |
| PowerShell para actualizar a V12 desde una versión anterior | Hay nuevos cmdlets de Powershell disponibles para iniciar, cancelar o supervisar una actualización a Base de datos SQL de Azure V12 desde la versión V11, o cualquier otra versión anterior a la V12.<br/><br/>Para obtener documentación de referencia acerca de estos cmdlets, consulte:<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| . | ***Abril de 2015:*** |
| TDE | El cifrado de datos transparente (vista previa) facilita la protección contra actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo. TDE no requiere cambios en la aplicación.<br/><br/>Para obtener información detallada, consulte:<br/>- [Cifrado de datos transparente con Base de datos SQL de Azure](http://msdn.microsoft.com/library/dn948096.aspx) (instrucciones detalladas).<br/>- [Cifrado de datos transparente (TDE)](http://msdn.microsoft.com/library/bb934049.aspx) (descripción general). |
| Certificados, claves y funciones criptográficas de Transact-SQL | En Base de datos SQL de Azure V12 se pueden crear certificados, claves simétricas y claves asimétricas. Ahora se admiten la mayoría de funciones criptográficas.<br/><br/>Para obtener más información, consulte:<br/>- [CREATE CERTIFICATE (Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [Funciones criptográficas (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| Grupo elástico de bases de datos SQL | Las tareas sencillas se complican cuando se escala a miles de bases de datos, ese es el inconveniente de un crecimiento explosivo. Los desarrolladores de SaaS pasan muchas horas escribiendo una lógica compleja para controlar los cambios de esquema y otras operaciones administrativas a medida que crece el negocio.<br/><br/>Las [bases de datos elásticas](sql-database-elastic-pool.md) les facilitan mucho su trabajo. Solo tiene que tomar un script y enviarlo como trabajo. Base de datos SQL de Azure hace el resto. |
| Recomendaciones de niveles de precios y el STA | Al mover la base de datos de las ediciones Web o Business a uno de los nuevos niveles de servicio Basic, Standard o Premium, puede obtener recomendaciones de los niveles de precios directamente desde el Portal de vista previa de Azure.<br/><br/>El servicio Base de datos SQL de Azure analiza los requisitos de rendimiento y características de las bases de datos existentes. El [Asesor de nivel de servicio (STA)](sql-database-service-tier-advisor.md) lee los datos de rendimiento para recomendar el nivel de servicio óptimo para su base de datos. |
| Permisos de DMV | En varias vistas de administración dinámica (DMV), anteriormente su ejecución requería el permiso VIEW SERVER STATE. En Base de datos SQL de Azure V12, en muchos casos la cuenta de administrador de Base de datos SQL puede ejecutar las DMV en las bases de datos de los niveles de servicio Basic o Standard.<br/><br/>En el nivel Premium, el acceso a las DMV puede concederse a más usuarios mediante el permiso VIEW DATABASE STATE de la base de datos.<br/><br/>Para obtener más información, consulte la sección Permisos de una [DMV](http://msdn.microsoft.com/library/ms188754.aspx) individual dada. |
| DBCC SQLPERF | El comando [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) está disponible en Base de datos SQL de Azure V12. Las opciones de *reiniciar las estadísticas de esperas* y *bloqueos temporales* no se admiten en Base de datos SQL V12. |
| . | ***Diciembre de 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Usuarios contenidos en bases de datos | Ahora se pueden crear usuarios en una base de datos sin tener un inicio de sesión correspondiente en la base de datos maestra. Se denominan *usuarios contenidos*. Esto simplifica mucho más el traslado de la base de datos a otro servidor. El código de conexión de las aplicaciones cliente es el mismo, independientemente de que se utilicen usuarios de bases de datos contenidas o no.<br/><br/>Esta característica es una magnífica forma de beneficiarse de contratos de nivel de servicio garantizados superiores.<br/><br/>Habitualmente fomentamos el uso de esta característica. Sin embargo, es posible que tenga escenarios concretos que hagan que la tradicional pareja *inicio de sesión + usuario* sea la mejor opción en este momento.<br/><br/>Para obtener más información, vea:<br/>- [Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Administrar bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Bases de datos contenidas](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partición de tabla | Se eliminan las limitaciones anteriores de la [partición de tablas](http://msdn.microsoft.com/library/ms190787.aspx). |
| Transacciones grandes | Con V12 ya no está limitado a un máximo de 2 GB de modificaciones de datos en una sola transacción. <br/><br/> Una ventaja es que la regeneración de índices grandes ya no tiene el límite de 2 GB en el tamaño de las transacciones. Para obtener información general, consulte [Límites de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Generación y regeneración de índices en línea | Antes de la versión V12, Base de datos SQL de Azure normalmente admitía la cláusula (ONLINE=ON) del comando [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx), pero dicha cláusula no era compatible con los índices de una columna del tipo BLOB. La V12 incluso admite (ONLINE = ON) índices en columnas BLOB.<br/><br/> La característica ONLINE habilita consultas para beneficiarse de un índice, incluso cuando se vuelve a generar el índice. |
| Compatibilidad de CHECKPOINT | Con V12 puede emitir el comando [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) de Transact-SQL para una base de datos. |
| Mejora de ALTER TABLE | Permite que lleven a cabo muchas acciones para alterar columnas mientras la tabla permanece disponible. Para obtener más información, consulte [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Mejora de TRUNCATE TABLE | Permite el truncamiento de particiones específicas. Para obtener más información, consulte [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Más opciones de ALTER DATABASE | V12 admite más de las opciones disponibles en el comando [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx). <br/><br/>Para obtener más información, consulte [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Más comandos DBCC | En V12 hay más comandos [DBCC](http://msdn.microsoft.com/library/ms188796.aspx) disponibles. Para obtener información más detallada, consulte [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


## Programación y compatibilidad con aplicaciones


| Característica | Descripción |
| :--- | :--- |
| . | ***1 de mayo de 2015:*** |
| Tamaño mayor de los índices | En Base de datos SQL V12, es posible combinar hasta 32 columnas en una única clave de índice compuesta. El tamaño máximo permitido de los valores de los índices es 900 bytes, en los índices agrupados, o 1700 bytes en los índice no agrupados.<br/><br/>Los límites son 16 columnas y 900 bytes en la versión V11 y en otras versiones anteriores a Base de datos SQL V12. |
| . | ***Abril de 2015:*** |
| Vista previa de búsqueda de texto completo | La [búsqueda de texto completo (FTS)](http://msdn.microsoft.com/library/ms142571.aspx) permite consultar las columnas basadas en caracteres de ciertas maneras que son más eficaces que el operador LIKE. Por ejemplo:<br/><br/>-FREETEXT: busca frases que coincidan con el *significado* de la frase de búsqueda, incluso cuando no coincida con el texto exacto.<br/>-CONTAINS: busca los valores de las celdas que contengan los dos términos de búsqueda con estrecha *proximidad física* entre sí.<br/><br/>** Nota: ** esta característica se encuentra en el estado de vista previa y aún no se ha anunciado la disponibilidad general para su uso en producción. La funcionalidad de vista previa del intervalo de FTS es un subconjunto del intervalo de FTS de Microsoft SQL Server. |
| . | ***Febrero de 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Vista previa de enmascaramiento dinámico de datos | Cuando se genera un conjunto de filas a partir de una consulta, una directiva de enmascaramiento de datos establecida puede reemplazar partes de los datos con caracteres 'X' para ocultar la información confidencial y protegerla. Una vez completada la operación de enmascaramiento, el conjunto de filas modificado se envía al cliente.<br/><br/>Uno de los usos de ejemplo podría ser enmascarar todos los dígitos menos los últimos, de una tarjeta de crédito.<br/><br/>** Nota: ** esta característica se encuentra en el estado de vista previa y aún no se ha anunciado la disponibilidad general para su uso en producción.<br/><br/>Para obtener más información, consulte [Introducción al enmascaramiento de datos dinámicos de Base de datos SQL](sql-database-dynamic-data-masking-get-started.md). |
| . | ***Enero de 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Vista previa de la seguridad de nivel de fila (RLS) | El nuevo comando [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) de Transact-SQL permite implementar RLS. RLS hace que el servidor de base de datos agregue condiciones que filtran algunas filas de datos antes de que un conjunto de filas se devuelva al autor de la llamada.<br/><br/>En la industria, RLS a veces también se denomina control de acceso específico.<br/><br/>** Nota: ** esta característica se encuentra en el estado de vista previa y aún no se ha anunciado la disponibilidad general para su uso en producción.<br/><br/>Para obtener un ejemplo de código y más información, consulte [Vista previa de seguridad de nivel de fila](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Diciembre de 2014:*** |
| Funciones de ventana en consultas de Transact-SQL | A las funciones de ventana de ANSI se accede con la [cláusula OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan tiene una excelente [publicación de blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) que explica las funciones de ventana y la cláusula OVER. |
| Integración de CLR de .NET | El CLR (Common Language Runtime) de .NET Framework está integrado en V12. <br/><br/>Solo se admiten los ensamblados SAFE que estén totalmente compilados en código binario. Para obtener información detallada, consulte [Restricciones del modelo de programación de integración de CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Puede encontrar información sobre esta característica en los siguientes temas: <br/> * [Introducción a la integración CLR de SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Seguimiento de cambios para datos | El seguimiento de cambios para datos se puede configurar ahora en el nivel de base de datos o tabla. <br/><br/> Para obtener información sobre el seguimiento de cambios, consulte [Acerca del seguimiento de cambios (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Índices XML | V12 permite utilizar los comandos [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) y [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx) de Transact-SQL. |
| Tabla como montón | V12 permite crear una tabla que no tenga ningún índice agrupado. <br/><br/>Esta característica es especialmente útil para su compatibilidad con el comando [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx) de Transact-SQL, que crea una tabla a partir del resultado de una consulta. |
| Roles de la aplicación | Para el control de la seguridad y los permisos, V12 permite emitir los comandos [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) contra los [roles de aplicación](http://msdn.microsoft.com/library/ms190998.aspx). |


## Información de clientes mejorada


| Característica | Descripción |
| :--- | :--- |
| . | ***Diciembre de 2014:*** |
| DMV (vistas de administración dinámica) | Se agregan varias DMV en V12. Para obtener información más detallada, consulte [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Seguimiento de cambios | V12 es totalmente compatible con el seguimiento de cambios. <br/><br/> Para obtener información detallada sobre esta característica, consulte [Habilitar y deshabilitar el seguimiento de cambios (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |
| Índices de almacén de columnas | Los índices de almacén de columnas mejoran el rendimiento del sistema en los almacenes de datos cuando una columna indizada contiene repeticiones del mismo valor. El [índice de almacén de columnas comprime](http://msdn.microsoft.com/library/gg492088.aspx) los valores duplicados para reducir el número de filas físicos que se debe tener acceso durante las consultas. |


## Mejoras de rendimiento del nivel de servicio Premium


Estas mejoras de rendimiento se aplican a los niveles P2 y P3 del nivel de servicio Premium.


| Característica | Descripción |
| :--- | :--- |
| . | ***Diciembre de 2014:*** |
| Procesamiento paralelo para consultas | V12 proporciona un mayor grado de paralelismo para las consultas que puede beneficiarse de él. |
| Latencia de E/S más breve | V12 tiene una latencia mucho más breve para las operaciones de entrada/salida. |
| E/S por segundo aumentada | V12 puede procesar una cantidad mayor de entrada/salida por segundo (E/S por segundo). |
| Tasa de registros | V12 puede registrar más cambios de datos por segundo. |


## La V12 se convertirá en la API predeterminada a partir del 1 de agosto de 2015  


| Característica | Descripción |
| :--- | :--- |
| . | ***Agosto de 2015:*** |
| Creación de un servidor con REST o PowerShell | Si se crea un servidor sin especificar una versión de servidor, la versión predeterminada cambiará de V11 a V12.<br/><br/>Esto se alinea con el [Portal de vista previa de Azure](http://portal.azure.com). |
| Crear bases de datos con Transact-SQL, [REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) o [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) | En los servidores de V11, cuando se crea una base de datos nueva sin especificar una edición o el objetivo de servicio, el objetivo de servicio predeterminado será [S0](http://azure.microsoft.com/pricing/details/sql-database/), en lugar de Web y Business. Esto se alinea con los servidores de V12 en el Portal de vista previa de Azure. |


## Resumen de mejoras


V12 eleva nuestra base de datos SQL cerca de la compatibilidad completa de características con nuestro producto de SQL Server. V12 se centra en las características más populares y en la compatibilidad con programación. Esto hace que el desarrollo sea más agradable y más eficaz. Ahora es más fácil migrar las aplicaciones de la base de datos SQL a la nube.


Y en el nivel Premium V12 incorpora mejoras importantes de rendimiento. Algunas aplicaciones experimentarán mejoras extremas en la velocidad de la consulta. Las aplicaciones con cargas de trabajo grandes experimentarán un rendimiento sólido y fiable.


## <a name="V12AzureSqlDbPreviewGaTable"></a>Estado de disponibilidad general (GA) de V12 por región


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) durante la vista previa tuvieron aplicado un descuento. El precio vuelve al nivel de GA en todas las regiones el martes, 31 de marzo de 2015.


| Región de Azure | Estado de la versión actual<br/>de V12 | Fecha de promoción<br/>a GA |
| :--- | :--- | :--- |
| Centro-Sur de EE. UU | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Central EE. UU.: | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Centro-Norte de EE. UU | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Oeste de EE. UU. | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Este de EE. UU. | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Este de EE. UU. 2 | Disponibilidad general (GA) | 9 de febrero de 2015 |
| Asia oriental | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Sudeste asiático | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Oeste de Japón | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Este de Japón | Disponibilidad general (GA) | 24 de febrero de 2015 |
| Europa del Norte | Disponibilidad general (GA) | 29 de enero de 2015 |
| Europa occidental | Disponibilidad general (GA) | 29 de enero de 2015 |
| Sur de Brasil | Disponibilidad general (GA) | 21 de abril de 2015 |
| Australia Oriental | Vista previa | Segundo trimestre de 2015, estimado |
| Sudeste de Australia | Vista previa | Segundo trimestre de 2015, estimado |


Para cualquier región que haya alcanzado la fecha de disponibilidad general, todas las suscripciones nuevas y sus bases de datos posteriores utilizan la arquitectura de servicio V12 y, por tanto, tienen acceso a las características más recientes. En el presente artículo se enumeran las nuevas características que ofrece V12.


En todas las regiones en las que el estado sea vista previa, y no estén aún en GA, debe [activar la opción para utilizar las bases de datos la versión V12](sql-database-v12-sign-up.md).


En GA, si tiene servidores y bases de datos anteriores a la versión V12, puede actualizar (o mover) las bases de datos a la arquitectura de servicios de V12. A continuación, puede utilizar las nuevas características para la producción. Las bases de datos de V12 deben estar en los [niveles de precios](sql-database-upgrade-new-service-tiers.md) Basic, Standard o Premium.


## Cómo continuar


En [Planeación y preparación para actualizar a Base de datos SQL de Azure V12](sql-database-v12-plan-prepare-upgrade.md), puede aprender a actualizar bases de datos de la versión V11 a la V12 de Base de datos SQL de Azure.


Los números de versión como V12 hacen referencia al valor que devuelve la siguiente instrucción de Transact-SQL:<br/> **SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(o algo superior, V12)*


Para obtener la información más reciente acerca de los precios de V12, consulte [Precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).


## Precauciones al realizar la actualización a V12


Hay aspectos importantes que se deben saber sobre las limitaciones durante y después de una actualización de una base de datos de V11 a V12. Puede leer los detalles de este vínculo a un [punto medio](sql-database-v12-plan-prepare-upgrade.md#limitations) del tema *Planeación y preparación para realizar la actualización a Base de datos SQL V12*.


## Otras fuentes de noticias recientes


El tema de *novedades* de Base de datos SQL de Azure V12 finalizará, y no se volverá a actualizar, probablemente después del 30 de abril de 2015. Para obtener información acerca de las nuevas características, así como otros anuncios,  será preciso utilizar el vínculo de anuncios que aparece a continuación:


- [Anuncios](http://azure.microsoft.com/updates/?service=sql-database) de Base de datos SQL de Azure en nuestra página web **Actualizaciones del servicio**.
 - Cuando en el control Servicios se ha seleccionado *Base de datos SQL*, puede hacer clic en el icono **RSS** de la página web.
- Síganos en Twitter: **@SQLTechCenter**.


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58--> 