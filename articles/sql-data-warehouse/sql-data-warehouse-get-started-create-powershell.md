<properties
   pageTitle="Crear Almacenamiento de datos SQL mediante el uso de Powershell | Microsoft Azure"
   description="Creación de Almacenamiento de datos SQL con Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/05/2015"
   ms.author="lodipalm"/>

# Creación de Almacenamiento de datos SQL con Powershell

> [AZURE.NOTE]Para usar Microsoft Azure Powershell con Almacenamiento de datos SQL, se necesita la versión 0.9.4 o superior. Puede comprobar la versión ejecutando (Get-Module Azure). Versión de Powershell.

## Obtención y ejecución de los cmdlets de Azure PowerShell.
Si no tiene ya instalado Powershell, puede:

1. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Para ejecutar el módulo, en la ventana de inicio, escriba **Microsoft Azure PowerShell**.
3. Si no ha agregado todavía su cuenta a la máquina, ejecute el siguiente cmdlet. (Para obtener más información, vea [Instalación y configuración de Azure PowerShell][]):

            Add-AzureAccount

4. También tendrá que ejecutar PowerShell en el modo ARM. Puede cambiar a este modo ejecutando el siguiente comando:

            switch-azuremode AzureResourceManager

## Creación de Almacenamiento de datos SQL
Una vez que se haya asegurado de que Powershell está correctamente configurado en su cuenta puede ejecutar lo siguiente para implementar un nuevo Almacenamiento de datos SQL:

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

Los parámetros necesarios para este cmdlet son los siguientes:

 + **RequestedServiceObjectiveName**: la cantidad de DWU solicita, en la forma "DWXXX" los valores que se admiten actualmente son: 100, 200, 300, 400, 500, 600, 1000, 1500, 1200 y 2000.
 + **DatabaseName**: el nombre del Almacenamiento de datos SQL que está creando.
 + **NombreDeServidor**: el nombre del servidor que se usa para la creación (tiene que ser V12).
 + **ResourceGroupName**: el grupo de recursos que está usando. Para obtener los grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
 + **Edition**: tiene que establecer la edición como "DataWarehouse" para crear un Almacenamiento de datos SQL. 

## Pasos siguientes
Después de que su Almacenamiento de datos SQL termine el aprovisionamiento, puede [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][], o [migrar][].

Si está interesado en obtener más información sobre cómo administrar Almacenamiento de datos SQL mediante programación, consulte nuestra documentación de [Powershell][] o [API de REST][].



<!--Image references-->

<!--Article references-->
[migrar]: https://azure.microsoft.com/es-es/documentation/articles/sql-data-warehouse-overview-migrate/
[desarrollar]: https://azure.microsoft.com/es-es/documentation/articles/sql-data-warehouse-overview-develop/
[cargar]: https://azure.microsoft.com/es-es/documentation/articles/sql-data-warehouse-overview-load/
[cargar datos de ejemplo]: https://azure.microsoft.com/es-es/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/es-es/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[API de REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/es-es/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/es-es/documentation/articles/sql-database-configure-firewall-settings/
[Instalación y configuración de Azure PowerShell]: powershell-install-configure.md

<!---HONumber=Oct15_HO2-->