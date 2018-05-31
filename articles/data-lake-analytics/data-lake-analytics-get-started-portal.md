---
title: Introducción a Azure Data Lake Analytics mediante Azure Portal
description: Aprenda a usar Azure Portal para crear una cuenta de Data Lake Analytics, crear un trabajo de Data Lake Analytics mediante U-SQL y enviar el trabajo.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.topic: hero-article
ms.workload: big-data
ms.date: 03/21/2017
ms.openlocfilehash: 63fc4272dc4b26e9eb4846118bc57cd6aa5bf361
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358292"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Introducción al uso de Azure Portal por parte de Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar Azure Portal para crear cuentas de Azure Data Lake Analytics, definir trabajos en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos al servicio Data Lake Analytics. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>requisitos previos

Para comenzar este tutorial, es preciso tener una **suscripción a Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake

Ahora, las cuentas de Data Lake Analytics y Data Lake Store se crean al mismo tiempo.  Este paso es muy simple y en 60 segundos se ha realizado.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **Crear un recurso** >  **Datos y análisis** > **Data Lake Analytics**.
3. Seleccione los valores de los siguientes elementos:
   * **Nombre**: nombre de la cuenta de Data Lake Analytics (solo se permiten letras minúsculas y números).
   * **Suscripción**: seleccione la suscripción de Azure usada para la cuenta de Analytics.
   * **Grupo de recursos**. seleccione un grupo de recursos de Azure existente o cree uno nuevo.
   * **Ubicación**. seleccione un centro de datos de Azure para la cuenta de Análisis de Data Lake.
   * **Data Lake Store**: siga las instrucciones para crear una nueva cuenta de Data Lake Store o seleccione una existente. 
4. Si lo desea, seleccione un plan de tarifa para la cuenta de Data Lake Analytics.
5. Haga clic en **Create**(Crear). 


## <a name="your-first-u-sql-script"></a>El primer script U-SQL

El siguiente texto es un script U-SQL muy simple. Todo lo que hace es definir un pequeño conjunto de datos del script y, después, escribirlo en la instancia predeterminada de Data Lake Store en forma de archivo denominado `/data.csv`.

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

## <a name="submit-a-u-sql-job"></a>Envío de un trabajo de U-SQL

1. Desde la cuenta de Data Lake Analytics, seleccione **Nuevo trabajo**.
2. Pegue el texto del script U-SQL anterior. Asigne un nombre al trabajo. 
3. Seleccione el botón **Enviar** para iniciar el trabajo.   
4. Supervise el **estado** del trabajo y espere a que este cambie a **Correcto**.
5. Seleccione la pestaña **Datos** y, después, seleccione la pestaña **Salidas**. Seleccione el archivo de salida denominado `data.csv` y vea los datos de salida.

## <a name="see-also"></a>Otras referencias

* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
