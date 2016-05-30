<properties
   pageTitle="Cmdlets de PowerShell para Almacenamiento de datos SQL de Azure"
   description="Busque los principales cmdlets de PowerShell para Almacenamiento de datos SQL de Azure, incluidos aquellos para pausar y reanudar una base de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/14/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Cmdlets de PowerShell y API de REST para Almacenamiento de datos SQL

Muchas tareas de administración de Almacenamiento de datos SQL se pueden administrar mediante los cmdlets de Azure PowerShell o las API de REST. A continuación se muestran algunos ejemplos de cómo usar comandos de PowerShell para automatizar tareas comunes en Almacenamiento de datos SQL. O bien, para obtener una lista de API de REST para automatizar estas mismas tareas, consulte [Operaciones para bases de SQL de Azure][].

> [AZURE.NOTE]  Para usar Azure Powershell con Almacenamiento de datos SQL, se necesita instalar Azure PowerShell versión 1.0.3 o superior. Puede comprobar la versión ejecutando **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][]. Para más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

## Introducción a los cmdlets de Azure PowerShell

1. Abra Windows PowerShell. 
2. En el símbolo del sistema de PowerShell, ejecute estos comandos para iniciar sesión en Azure Resource Manager y seleccione su suscripción.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Ejemplo de pausa de Almacenamiento de datos SQL

Pausa una base de datos denominada "Database02" que está hospedada en un servidor cuyo nombre es "Server01". El servidor está en un grupo de recursos de Azure denominado "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Este ejemplo, que es una variación, canaliza el objeto recuperado a [Suspend-AzureRmSqlDatabase][]. El resultado es que se pausa la base de datos. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Ejemplo de inicio de Almacenamiento de datos SQL

Reanuda el funcionamiento de una base de datos denominada "Database02" que está hospedada en un servidor cuyo nombre es "Server01". El servidor está en un grupo de recursos denominado "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Este ejemplo, que es una variación, recupera una base de datos denominada "Database02" desde un servidor cuyo nombre es "Server01" que está incluido en un grupo de recursos denominado "ResourceGroup1". Canaliza el objeto recuperado a [Resume-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Tenga en cuenta que si el servidor es foo.database.windows.net, debe usar "foo" como -ServerName en los cmdlets de Powershell.

## Cmdlets de PowerShell usados con frecuencia

Estos cmdlets de PowerShell se usan con frecuencia con Almacenamiento de datos SQL de Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][] 
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## Pasos siguientes
Para obtener más ejemplos de PowerShell, consulte:

- [Creación de Almacenamiento de datos SQL con PowerShell][]
- [Restauración desde instantánea][]
- [Restauración geográfica desde instantánea][]

Para obtener una lista de todas las tareas que se pueden automatizar con PowerShell, consulte [Azure SQL Database Cmdlets][] (Cmdlets de Base de datos SQL de Azure).

<!--Image references-->

<!--Article references-->
[Cómo instalar y configurar Azure PowerShell]: powershell-install-configure.md
[Creación de Almacenamiento de datos SQL con PowerShell]: sql-data-warehouse-get-started-provision-powershell.md
[Restauración desde instantánea]: sql-data-warehouse-backup-and-restore-from-snapshot.md
[Restauración geográfica desde instantánea]: sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Operaciones para bases de SQL de Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->