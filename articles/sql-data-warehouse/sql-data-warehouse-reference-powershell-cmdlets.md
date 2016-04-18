<properties
   pageTitle="Cmdlets de PowerShell para Almacenamiento de datos SQL de Azure"
   description="Busque los principales cmdlets de PowerShell para Almacenamiento de datos SQL de Azure, incluidos aquellos para pausar y reanudar una base de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="barbkess;mausher;sonyama"/>

# Cmdlets de PowerShell y API de REST para Almacenamiento de datos SQL

El Almacenamiento de datos SQL se puede administrar mediante los cmdlets de PowerShell de Azure o las API de REST.

La mayoría de los comandos definidos para **Base de datos SQL de Azure** también se usan para **Almacenamiento de datos SQL**. Para obtener una lista actualizada, consulte [Cmdlets SQL de Azure](https://msdn.microsoft.com/library/mt574084.aspx) Los cmdlets [Suspend-AzureRmSqlDatabase][] y [Resume-AzureRmSqlDatabase][] son elementos adicionales diseñados para Almacenamiento de datos SQL.

De forma similar, las API de REST para **Base de datos de SQL Azure** también pueden usarse para las instancias de **Almacenamiento de datos SQL**. Para obtener la lista actualizada, consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Introducción a los cmdlets de Azure PowerShell

1. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://aka.ms/webpi-azps). Para obtener más información sobre este instalador, consulte [Cómo instalar y configurar Azure PowerShell][].
2. Para iniciar PowerShell, haga clic en **Iniciar** y escriba **Windows PowerShell**.
3. En el símbolo del sistema de PowerShell, ejecute estos comandos para iniciar sesión en Azure Resource Manager y seleccione su suscripción.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## Cmdlets de PowerShell de uso frecuente

Estos cmdlets de PowerShell se usan con frecuencia con Almacenamiento de datos SQL de Azure:


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


## Ejemplos de Almacenamiento de datos SQL

Estos ejemplos son para las funciones que solo se aplican a Almacenamiento de datos SQL.

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

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


## Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][]. Para obtener más ejemplos de PowerShell, consulte:
- [Creación de Almacenamiento de datos SQL con PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
- [Restauración desde instantánea](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [Restauración geográfica desde instantánea](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[Información general de referencia de Almacenamiento de datos SQL]: sql-data-warehouse-overview-reference.md
[Cómo instalar y configurar Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0406_2016-->