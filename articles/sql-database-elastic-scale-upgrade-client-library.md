<properties 
	title="Upgrade to the Latest Elastic Scale Client Library" 
	pageTitle="Actualizar a la última biblioteca de cliente de escala elástica" 
	description="Instrucciones de actualización con PowerShell y C#" 
	metaKeywords="sharding,elastic scale, Azure SQL DB sharding" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="stuarto", "sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto" />

# Actualizar a la última biblioteca de cliente de escala elástica

Hay nuevas versiones disponibles de la biblioteca de cliente de escala elástica [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) y de la interfaz del Administrador de NuGetPackage en Visual Studio. Las actualizaciones contienen correcciones de errores y compatibilidad para nuevas capacidades de escala elástica.

## Pasos de actualización

La actualización requiere volver a generar la aplicación con la nueva biblioteca, así como cambiar los metadatos de administrador de asignación de particiones existentes almacenados en sus bases de datos de SQL Azure para admitir nuevas características.

Siga la secuencia que se indica a continuación para actualizar las aplicaciones, la base de datos de administrador de asignación de particiones y los metadatos del administrador de asignación de particiones local de cada partición.  Al realizar pasos de actualización en este orden se asegura de que las versiones anteriores de la biblioteca de cliente ya no está presentes en el entorno cuando se actualizan los objetos de metadatos, lo que significa que no se crearán objetos de metadatos de la antigua versión después de la actualización.   

**1. Actualice sus aplicaciones.** En Visual Studio, descargue y haga referencia a la versión más reciente de la biblioteca de clientes en todos los proyectos de escala elástica; a continuación, vuelva a generar e implementar. 

 * En la solución de Visual Studio, seleccione **Herramientas** --> **Administrador de paquetes NuGet** --> **Administrar paquetes NuGet para la solución**. 
 * En el panel izquierdo, seleccione **Actualizaciones** y, a continuación, seleccione el botón **Actualizar** en el paquete **Biblioteca de clientes de escala elástica de base de datos de SQL Azure** que aparece en la ventana.
	![Upgrade Nuget Pacakges][1]
 
 * Genere e implemente. 

**2. Actualice sus secuencias de comandos.** Si está utilizando secuencias de comandos de **PowerShell** para administrar particiones, [descargue la nueva versión de la biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) y cópiela en el directorio desde el que ejecutar secuencias de comandos. 

**3. Actualice el servicio División y combinación.** Si usa el servicio División y combinación de escala elástica para reorganizar datos particionados, [descargue e implemente la versión más reciente del servicio](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Puede encontrar pasos detallados de actualización del servicio [aquí](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-overview-split-and-merge/). 

**4. Actualice sus bases de datos de administrador de asignación de partición**. Actualice los metadatos compatibles con sus asignaciones de partición en la base de datos de SQL Azure.  Hay dos maneras de hacerlo, mediante PowerShell o C#. Ambas opciones se muestran a continuación.

***Opción 1: Actualizar los metadatos mediante PowerShell***

1. Descargue la utilidad de línea de comandos más reciente de NuGet desde [aquí](http://nuget.org/nuget.exe) y guárdela en una carpeta. 

2. Abra un símbolo del sistema, navegue a la misma carpeta y emita el comando:
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Vaya a la subcarpeta que contiene la nueva versión DLL de cliente que acaba de descargar, por ejemplo:
`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.0.8.0\lib\net45`

4. Descargue el scriptlet de actualización del cliente de escala elástica del [Centro de scripts](http://go.microsoft.com/?linkid=9876343) y guárdelo en la misma carpeta que contiene el archivo DLL.

5. Desde esa carpeta, ejecute "PowerShell .\upgrade.ps1" desde el símbolo del sistema y siga las indicaciones.
 
***Opción 2: Actualizar los metadatos mediante C#***

De forma alternativa, puede crear una aplicación de Visual Studio que abra su ShardMapManager, recorra en iteración todas las particiones y realice la actualización de metadatos mediante una llamada a los métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) y [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) como en este ejemplo: 

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

Estas técnicas para las actualizaciones de metadatos se pueden aplicar varias veces sin problema. Por ejemplo, si una versión anterior del cliente crea sin darse cuenta una partición después de haber actualizado, puede ejecutar la actualización de nuevo en todas las particiones para asegurarse de que la última versión de metadatos está presente en toda su infraestructura. 

**Nota:** las nuevas versiones de la biblioteca cliente publicadas hasta la fecha continuarán funcionando con las versiones anteriores de los metadatos del administrador de asignación de particiones en la base de datos de SQL Azure y viceversa. Sin embargo, para aprovechar algunas de las nuevas características en el cliente más reciente, es necesario actualizar los metadatos. Tenga en cuenta que las actualizaciones de metadatos no afectarán a los datos de usuario ni los datos específicos de la aplicación, solo a los objetos creados y utilizados por el administrador de asignación de particiones. Asimismo, las aplicaciones seguirán funcionando en la secuencia de actualización que se ha descrito anteriormente.

## Historial de versiones de cliente de escala elástica 

**Versión 0.8: marzo de 2015**

* Compatibilidad de Async agregada para el enrutamiento según los datos con los nuevos métodos ShardMap.OpenConnectionForKeyAsync. 
* Propiedad KeyType pública agregada a ShardMap 
* Mejoras agregadas de compatibilidad con escenarios de recuperación ante desastres y restauración de base de datos para particiones 

**Versión 0.7: octubre de 2014** 

Versión inicial de vista previa 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

<!--HONumber=47-->
