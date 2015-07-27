<properties 
	pageTitle="Información general de los trabajos de bases de datos elásticas" 
	description="Muestra el servicio de trabajo de base de datos elástica" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# Información general de Trabajos de base de datos elástica

**Trabajos de bases de datos elásticas** (vista previa) permite ejecutar scripts de T-SQL (trabajos) en todas las bases de datos de un [grupo de bases de datos elásticas (vista previa)](sql-database-elastic-pool.md). Por ejemplo, puede actualizar fácilmente el esquema de todas las bases de datos para incluir una nueva tabla. Normalmente, debe conectarse a cada base de datos de forma independiente para ejecutar instrucciones T-SQL o realizar otras tareas administrativas. Un **trabajo de base de datos elástica** controla la tarea de inicio de sesión y permite ejecutar de manera fiable el script, al mismo tiempo que registra el estado de la ejecución en cada base de datos. Para obtener instrucciones acerca de cómo instalar la vista previa, vaya a [Instalación del componente de trabajo de la base de datos elástica](sql-database-elastic-jobs-service-installation.md).

![Servicio del trabajo de bases de datos elásticas][1]

## Ventajas

* Definición, mantenimiento y conservación de las secuencias de comandos de T-SQL que se van a ejecutar en un grupo de bases de datos elásticas
* Ejecución de secuencias de comandos de T-SQL de manera fiable con reintentos automáticos y a escala
* Seguimiento del estado de ejecución de los trabajos

## Escenarios

* Realización de tareas administrativas como, por ejemplo, la implementación de un nuevo esquema
* Actualización de datos de referencia como, por ejemplo, la información del producto común para todas las bases de datos
* Regeneración de los índices para mejorar el rendimiento de las consultas

## Cómo funciona el trabajo

1.	Instale los servicios que usan los trabajos de las bases de datos elásticas. Vea [Instalación del componente de trabajo de la base de datos elástica](sql-database-elastic-jobs-service-installation.md). En caso de error en la instalación, vea [Desinstalación](sql-database-elastic-jobs-uninstall.md).
2.	Configure el grupo de bases de datos elásticas para la ejecución del trabajo mediante la [adición de un usuario a cada base de datos](sql-database-elastic-jobs-add-logins-to-dbs.md).
3.	Cree un script de T-SQL que se pueden ejecutar en cada base de datos de grupo.
4.	Siga estos pasos para ejecutar el script: [Creación y administración de trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md) 

## Componentes y precios 

Los siguientes componentes funcionan conjuntamente para crear un servicio de nube de Azure que permite la ejecución de ad hoc de trabajos administrativos. Los componentes se instalan y configuran automáticamente durante la instalación, en su suscripción. Puede identificar los servicios, ya que todos tienen el mismo nombre generado automáticamente. El nombre es único y se compone del prefijo "edj" seguido de 21 caracteres generados de forma aleatoria.

* **Servicio de nube de Azure**: los trabajos de bases de datos elásticas (vista previa) se entregan como un servicio de nube de Azure hospedado por el cliente para realizar la ejecución de las tareas requeridas. Desde el portal, el servicio se implemente y hospeda en su suscripción de Microsoft Azure. El servicio implementado predeterminado se ejecuta con un mínimo de dos roles de trabajador para ofrecer un elevado nivel de disponibilidad. El tamaño predeterminado de cada función de trabajador (ElasticDatabaseJobWorker) se ejecuta en una instancia de A0. Para obtener información sobre los precios, vea [Precios de servicios de nube](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Base de datos SQL de Azure**: el servicio usa una base de datos SQL de Azure conocida como **base de datos de control** para almacenar todos los metadatos. El nivel de servicio predeterminado es S0. Para obtener información sobre precios, vea [Precios de bases de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).
* **Bus de servicio de Azure**: el bus de servicio de Azure permite coordinar el trabajo del servicio de nube de Azure. Vea [Precios del bus de servicio](http://azure.microsoft.com/pricing/details/service-bus/).
* **Almacenamiento de Azure**: se usa una cuenta de almacenamiento de Azure para almacenar los registros de salida de diagnóstico en caso de que un problema requiera una mayor depuración (práctica común para [Diagnósticos de Azure](../cloud-services-dotnet-diagnostics.md)). Para obtener información sobre precios, vea [Precios de almacenamiento de Azure](http://azure.microsoft.com/pricing/details/storage/).

## Pasos siguientes
[Instale los componentes](sql-database-elastic-jobs-service-installation.md) y, a continuación, [cree y agregue un registro en cada base de datos del grupo](sql-database-elastic-jobs-add-logins-to-dbs.md). Para comprender mejor la administración y creación de trabajos, consulte [Creación y administración de trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO2-->