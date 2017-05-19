---
title: "Introducción a Azure Data Lake Analytics mediante Azure PowerShell | Microsoft Docs"
description: 'Use Azure Portal para crear una cuenta de Data Lake Analytics, crear un trabajo de Data Lake Analytics mediante U-SQL y enviar el trabajo. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: Introducción a Análisis de Azure Data Lake mediante Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar Azure PowerShell para crear cuentas de Azure Data Lake Analytics y luego enviar y ejecutar trabajos de U-SQL. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe tener la siguiente información:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Una estación de trabajo con Azure PowerShell**. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Preparativos para el tutorial
Para crear una cuenta de Data Lake Analytics, primero debe definir:

* **Grupo de recursos de Azure**: se debe crear una cuenta de Data Lake Analytics dentro de un grupo de recursos de Azure.
* **Nombre de la cuenta de Data Lake Analytics**: el nombre de la cuenta de Data Lake solo debe contener letras minúsculas y números.
* **Ubicación**: uno de los centros de datos de Azure que admite Análisis de Data Lake.
* **Cuenta predeterminada de Data Lake Store**: cada cuenta de Data Lake Analytics tiene una cuenta predeterminada de Data Lake Store. Estas cuentas deben estar en la misma ubicación.

Los fragmentos de código de PowerShell de este tutorial usan estas variables para almacenar esta información

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake

Si aún no tiene un grupo de recursos, cree uno. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Cada cuenta de Data Lake Analytics requiere una cuenta predeterminada de Data Lake Store que se usa para almacenar registros. Puede reutilizar una cuenta existente o crear una nueva. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Una vez que un grupo de recursos y una cuenta de Data Lake Store estén disponibles, cree una cuenta de Data Lake Analytics.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Información acerca de una cuenta de Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Envío de un trabajo de U-SQL

Cree un archivo de texto con el siguiente script de U-SQL.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Envíe el script.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Supervisión de trabajos de U-SQL

Muestre todos los trabajos de la cuenta. El resultado incluye los trabajos actualmente en ejecución y aquellos que se han completado recientemente.

```
Get-AdlJob -Account $adla
```

Obtenga el estado de un trabajo específico.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

En lugar de llamar a Get-AdlAnalyticsJob una y otra vez hasta que finaliza un trabajo, puede usar el cmdlet Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Después de finalizar el trabajo, compruebe si existe el archivo de salida; para ello, muestre los archivos de una carpeta.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Compruebe la existencia de un archivo.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Carga y descarga de archivos

Descargue la salida del script de U-SQL.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Cargue un archivo para usar como entrada en un script de U-SQL.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Consulte también
* Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).

