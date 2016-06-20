<properties 
   pageTitle="Continuidad del negocio de la base de datos SQL durante la actualización de la aplicación" 
   description="En esta sección se proporcionan instrucciones para evitar el tiempo de inactividad durante una actualización de aplicaciones." 
   services="sql-database"
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="elfish"/>

#Actualización de una aplicación sin tiempo de inactividad

En el contexto de Microsoft Azure, el término "aplicación" hace referencia a componentes como, por ejemplo, los servidores front-end, los servicios implementados en un servicio de nube o al nivel de datos usado para conservar datos de la aplicación o metadatos. Las aplicaciones de nube a menudo están diseñadas para proporcionar un servicio ininterrumpido 24 horas durante los 7 días de la semana. La implementación de una nueva versión de la aplicación, cuando se aplican cambios en el nivel de datos en el sitio activo, puede causar interrupciones como, por ejemplo, la reducción de las características disponibles o incluso la inactividad completa.

Al diseñar el proceso de actualización de la aplicación, el objetivo principal que se debe fijar es el de la eliminación o reducción considerable del tiempo que la capacidad de la aplicación se ve reducida. Para ello, el proceso debe implicar la creación de una copia temporal de la aplicación que se usará como copia de seguridad en caso de que se produzca un error en la actualización. Deben tenerse en cuenta los factores siguientes al diseñar y planear la actualización:

1.	Tiempo máximo aceptable que la aplicación tendrá sus capacidades reducidas 
2.	Conjunto mínimo de características que estarán disponibles durante el proceso de actualización
3.	Capacidad para deshacer el proceso en caso de errores durante la actualización
4.	Coste total en dólares asociado Aquí se incluye el costo de los componentes de aplicación adicionales necesarios para crear una copia temporal (como, por ejemplo, las bases de datos adicionales para la replicación geográfica activa) y los costos incrementales de las implementaciones temporales que usa el proceso de actualización. 

Si la aplicación puede funcionar temporalmente en modo de solo lectura, el flujo de trabajo de actualización se puede diseñar para eliminar de manera eficaz el tiempo de inactividad. Para entender cómo implementar el flujo de trabajo de actualizaciones para su topología de aplicaciones específica, consulte [Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de Base de datos SQL](sql-database-manage-application-rolling-upgrade.md).
 
 
 

<!---HONumber=AcomDC_0608_2016-->