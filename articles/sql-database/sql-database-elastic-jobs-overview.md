<properties  
	pageTitle="Información general de los trabajos de bases de datos elásticas" 
	description="Muestra el servicio de trabajo de base de datos elástica" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# Información general de Trabajos de base de datos elástica

La característica **Trabajos de base de datos elástica** (vista previa) permite ejecutar de forma confiable un script de Transact-SQL (T-SQL) o aplicar un DACPAC transversalmente en un grupo de bases de datos, que puede incluir una colección personalizada de bases de datos, todas las bases de datos de un [grupo de bases de datos elásticas (vista previa)](sql-database-elastic-pool.md) o un conjunto de particiones (creado con la [biblioteca cliente de base de datos elástica](sql-database-elastic-database-client-library.md)). En la vista previa, **Trabajos de base de datos elástica** es actualmente un servicio en la nube de Azure alojado en el cliente que permite la ejecución de tareas administrativas ad hoc y programadas, que se denominan trabajos. Con esta característica, puede administrar fácilmente y de forma confiable Base de datos SQL de Azure a escala en todo un grupo de bases de datos ejecutando scripts de Transact-SQL para realizar operaciones administrativas como, por ejemplo, cambios de esquema, administración de credenciales, actualizaciones de datos de referencia, recopilación de datos de rendimiento o recopilación de telemetría de inquilinos (cliente). Normalmente, debe conectarse a cada base de datos de forma independiente para ejecutar instrucciones T-SQL o realizar otras tareas administrativas. **Trabajos de base de datos elástica** controla la tarea de inicio de sesión y la ejecución confiable del script, al mismo tiempo que registra el estado de la ejecución en cada base de datos. Para obtener instrucciones sobre la instalación, vaya a [Instalación de componentes de Trabajo de base de datos elástica](sql-database-elastic-jobs-service-installation.md).

![Servicio del trabajo de bases de datos elásticas][1]

## Ventajas
* Definición de grupos personalizados de bases de datos SQL de Azure
* Definición, mantenimiento y conservación de los scripts de Transact-SQL que se van a ejecutar transversalmente en un grupo de bases de datos elásticas 
* Implementación de una aplicación de capa de datos (DACPAC)
* Ejecución de scripts de Transact-SQL o una aplicación de archivos DACPAC confiable con reintento automático y a escala
* Seguimiento del estado de ejecución de los trabajos
* Definición de programaciones de ejecución
* Recopilación de datos transversal en una colección de bases de datos SQL de Azure que guarda los resultados en una sola tabla de destino

> [AZURE.NOTE]La funcionalidad de **Trabajos de base de datos elástica ** del Portal de Azure revela un conjunto de características reducido que también se limita a grupos de bases de datos SQL elásticas de Azure. Use las API de PowerShell para tener acceso al conjunto completo de funcionalidad actual.

## Escenarios

* Realización de tareas administrativas como, por ejemplo, la implementación de un nuevo esquema
* Actualice los datos de referencia como, por ejemplo, información del producto común para todas las bases de datos, e incluso use programaciones que automaticen las actualizaciones todos los días laborables después del horario.
* Regeneración de índices para mejorar el rendimiento de consultas La regeneración puede configurarse para que se ejecute periódicamente en una colección de bases de datos como, por ejemplo, fuera de horas pico.
* Recopile los resultados de consulta de un conjunto de bases de datos en una tabla central de forma continua. Las consultas de rendimiento pueden ejecutarse continuamente y configurarse para que desencadenen la ejecución de otras tareas.
* Ejecute consultas de procesamiento de datos de ejecución más larga en un conjunto grande de bases de datos; por ejemplo, la recopilación de telemetría de cliente. Los resultados se recopilan en una sola tabla de destino para su posterior análisis.

## Revisión completa y sencilla de Trabajos de base de datos elástica
1.	Instale los componentes de **Trabajos de base de datos elástica**. Para obtener más información, vea [Instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md). En caso de error en la instalación, vea [Desinstalación](sql-database-elastic-jobs-uninstall.md).
2.	Use las API de PowerShell para tener acceso a otra funcionalidad, por ejemplo, crear colecciones de bases de datos personalizadas, agregar programaciones o recopilar conjuntos de resultados. Use el Portal para instalar, crear y supervisar de manera sencilla los trabajos que se limiten a la ejecución de un **grupo de bases de datos elásticas**. 
3.	Cree credenciales cifradas para la ejecución de trabajos y [agregue el usuario (o rol) a cada base de datos del grupo](sql-database-elastic-jobs-add-logins-to-dbs.md).
4.	Cree un script de T-SQL idempotente que se pueda ejecutar en cada base de datos del grupo.
5.	Siga este procedimiento para ejecutar el script: [Creación y administración de trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md) 

## Componentes y precios 
Los siguientes componentes funcionan conjuntamente para crear un servicio de nube de Azure que permite la ejecución de ad hoc de trabajos administrativos. Los componentes se instalan y configuran automáticamente durante la instalación, en su suscripción. Puede identificar los servicios, ya que todos tienen el mismo nombre generado automáticamente. El nombre es único y se compone del prefijo "edj" seguido de 21 caracteres generados de forma aleatoria.

* **Servicio de nube de Azure**: los trabajos de bases de datos elásticas (vista previa) se entregan como un servicio de nube de Azure hospedado por el cliente para realizar la ejecución de las tareas requeridas. Desde el portal, el servicio se implemente y hospeda en su suscripción de Microsoft Azure. El servicio implementado predeterminado se ejecuta con un mínimo de dos roles de trabajador para ofrecer un elevado nivel de disponibilidad. El tamaño predeterminado de cada función de trabajador (ElasticDatabaseJobWorker) se ejecuta en una instancia de A0. Para obtener información sobre los precios, vea [Precios de servicios de nube](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Base de datos SQL de Azure**: el servicio usa una base de datos SQL de Azure conocida como **base de datos de control** para almacenar todos los metadatos. El nivel de servicio predeterminado es S0. Para obtener información sobre precios, vea [Precios de bases de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).
* **Bus de servicio de Azure**: el bus de servicio de Azure permite coordinar el trabajo del servicio de nube de Azure. Vea [Precios del bus de servicio](http://azure.microsoft.com/pricing/details/service-bus/).
* **Almacenamiento de Azure**: se usa una cuenta de almacenamiento de Azure para almacenar los registros de salida de diagnóstico en caso de que un problema requiera una mayor depuración (práctica común para [Diagnósticos de Azure](../cloud-services-dotnet-diagnostics.md)). Para obtener información sobre precios, vea [Precios de almacenamiento de Azure](http://azure.microsoft.com/pricing/details/storage/).

## Funcionamiento de Trabajos de base de datos elástica
1.	Una Base de datos SQL de Azure se designa como base de datos de control que almacena todos los datos de estado y los metadatos.
2.	**Trabajos de base de datos elástica** obtiene acceso a la base de control para iniciar los trabajos que deben ejecutarse y hacerles el seguimiento.
3.	Dos roles diferentes se comunican con la base de datos de control: 
	* Controlador: determina los trabajos que requieren tareas para realizar el trabajo solicitado y reintenta los trabajos con errores creando nuevas tareas de trabajo.
	* Ejecución de tareas de trabajo: lleva a cabo las tareas de trabajo.

### Tipos de tareas de trabajo
Hay varios tipos de tareas de trabajo que efectúan la ejecución de trabajos:

* ShardMapRefresh: consulta el mapa de particiones para determinar todas las bases de datos que se usan como particiones
* ScriptSplit: divide el script en lotes de instrucciones 'GO'
* ExpandJob: crea trabajos secundarios para cada base de datos en un trabajo destinado a un grupo de bases de datos
* ScriptExecution: ejecuta un script en una base de datos concreta con las credenciales que se definan
* Dacpac: aplica un DACPAC a una base de datos concreta con las credenciales que se determinen

## Flujo de trabajo de ejecución de trabajos completo
1.	Con el Portal o la API de PowerShell, se inserta un trabajo en la **base de datos de control**. El trabajo solicita la ejecución de un script de Transact-SQL en un grupo de bases de datos con las credenciales específicas.
2.	El controlador identifica el nuevo trabajo. Se crean tareas de trabajo y se ejecutan para dividir el script y actualizar las bases de datos del grupo. Por último, se crea otro trabajo y se ejecuta para expandir el trabajo y crear nuevos trabajos secundarios donde se especifica que cada trabajo secundario ejecute el script de Transact-SQL en una base de datos individual del grupo.
3.	El controlador identifica los trabajos secundarios creados. Para cada trabajo, el controlador crea y desencadena una tarea de trabajo que ejecuta el script en una base de datos. 
4.	Tras completar todas las tareas de trabajo, el controlador actualiza los trabajos con el estado completado. En cualquier momento durante la ejecución de trabajos, puede usarse la API de PowerShell para ver el estado actual de la ejecución de trabajos. Todas las horas que devuelven las API de PowerShell se representan en formato UTC. Si lo desea, se puede iniciar una solicitud de cancelación para detener un trabajo. 

## Pasos siguientes
[Instale los componentes](sql-database-elastic-jobs-service-installation.md) y luego [cree y agregue un registro en cada base de datos del grupo](sql-database-elastic-jobs-add-logins-to-dbs.md). Para comprender mejor la administración y creación de trabajos, consulte [Creación y administración de trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=August15_HO7-->