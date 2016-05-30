<properties
   pageTitle="Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (PowerShell) | Microsoft Azure"
   description="Tareas de PowerShell para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos."
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
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (PowerShell)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-manage-compute.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Escale el rendimiento mediante el escalado horizontal de los recursos de proceso y la memoria para satisfacer las necesidades cambiantes de la carga de trabajo. Ahorre costes reduciendo el escalado de los recursos fuera de horas punta o pausando el proceso por completo.

Esta colección de tareas usa el Portal de Azure para:

- Escalado de proceso
- Pausa del proceso
- Reanudación del proceso

Para más información, consulte [Manage compute power overview] (Administración de la potencia de proceso (información general)).


## Antes de empezar

### Instalar la versión más reciente de Azure PowerShell

> [AZURE.NOTE]  Para usar Azure PowerShell con Almacenamiento de datos SQL, es necesaria la versión 1.0.3 o posterior de Azure PowerShell. Para comprobar su versión actual, ejecute el comando **Get-Module -ListAvailable -Name Azure**. Puede instalar la versión más reciente desde [Instalador de plataforma web de Microsoft][]. Para más información, consulte [Instalación y configuración de Azure PowerShell][].

### Introducción a los cmdlets de Azure PowerShell

Primeros pasos:

1. Abra Azure PowerShell. 
2. En el símbolo del sistema de PowerShell, ejecute estos comandos para iniciar sesión en Azure Resource Manager y seleccione su suscripción.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Escalado de la potencia de proceso

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description (Descripción de escalado de DWU de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar las DWU, use el cmdlet de PowerShell [Set-AzureRmSqlDatabase][]. En el ejemplo siguiente se establece el objetivo de nivel de servicio en DW1000 para la base de datos MySQLDW que se hospeda en el servidor MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## Pausa del proceso

[AZURE.INCLUDE [SQL Data Warehouse pause description (Descripción de pausa de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos, use el cmdlet [Suspend-AzureRmSqlDatabase][]. El siguiente ejemplo pausa una base de datos denominada Database02 que está hospedada en un servidor llamado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

> [AZURE.NOTE] Tenga en cuenta que si el servidor es foo.database.windows.net, debe usar "foo" como -ServerName en los cmdlets de Powershell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
En el ejemplo siguiente, que es una variación, se recupera la base de datos en el objeto $database. A continuación, canaliza el objeto a [Suspend-AzureRmSqlDatabase][]. Los resultados se almacenan en el objeto resultDatabase. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## Reanudación del proceso

[AZURE.INCLUDE [SQL Data Warehouse resume description (Descripción de reanudación de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar una base de datos, use el cmdlet [Resume-AzureRmSqlDatabase][]. El siguiente ejemplo inicia una base de datos denominada Database02 que está hospedada en un servidor llamado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

En el ejemplo siguiente, que es una variación, se recupera la base de datos en el objeto $database. A continuación, canaliza el objeto a [Resume-AzureRmSqlDatabase][] y almacena los resultados en $resultDatabase. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## Pasos siguientes

Para otras tareas de administración, consulte [Información general de administración][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Performance scalability overview]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->