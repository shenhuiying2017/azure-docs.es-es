---
title: Desarrollo de scripts U-SQL mediante Data Lake Tools para Visual Studio | Microsoft Docs
description: Aprenda a instalar Data Lake Tools para Visual Studio y a desarrollar y probar scripts U-SQL.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2017
ms.author: saveenr, yanacai
ms.openlocfilehash: a48ce209bf3d5b7e5060acf2850144df5418828d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desarrollo de scripts U-SQL mediante Data Lake Tools para Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Aprenda a usar Visual Studio para crear cuentas de Azure Data Lake Analytics, definir trabajos en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos al servicio Data Lake Analytics. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).


## <a name="prerequisites"></a>Requisitos previos

* **Visual Studio**: se admiten todas las ediciones, excepto Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK para .NET** versión 2.7.1, o posterior.  Instálelo con el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* Una cuenta de **Data Lake Analytics**. Para crear una cuenta, consulte [Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalación de Herramientas de Azure Data Lake para Visual Studio 

Descargue Azure Data Lake Tools para Visual Studio [desde el Centro de descarga](http://aka.ms/adltoolsvs) e instálelo. Después de la instalación tenga en cuenta que:
* El nodo de **Explorador de servidores** > **Azure** contiene un nodo de **Data Lake Analytics**. 
* El menú **Herramientas** tiene un elemento de **Data Lake**.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Conexión a una cuenta de Azure Data Lake Analytics

1. Abra Visual Studio.
2. Abra el Explorador de servidores, para lo que debe seleccionar **Vista** > **Explorador de servidores**.
3. Haga clic con el botón derecho en **Azure**. Después, seleccione **Conectar a la suscripción de Microsoft Azure** y siga las instrucciones.
4. En el Explorador de servidores, seleccione **Azure** > **Data Lake Analytics**. Verá una lista de las cuentas de Data Lake Analytics.


## <a name="write-your-first-u-sql-script"></a>Escriba el primer script U-SQL

El siguiente texto es un script U-SQL simple. Define un conjunto de datos pequeño y lo escribe en la instancia predeterminada de Data Lake Store como un archivo denominado `/data.csv`.

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

### <a name="submit-a-data-lake-analytics-job"></a>Envío de un trabajo de Análisis de Data Lake

1. Seleccione **Archivo** > **Nuevo** > **Proyecto**.

2. Seleccione el tipo **Proyecto U-SQL** y haga clic en **Aceptar**. Visual Studio crea una solución con un archivo **Script.usql**.

3. Pegue el script anterior en la ventana de **Script.usql**.

4. En la esquina superior izquierda de la ventana de **Script.usql**, especifique la cuenta de Data Lake Analytics.

    ![Enviar proyecto U-SQL de Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. En la esquina superior izquierda de la ventana de **Script.usql**, seleccione **Enviar**.
6. Compruebe la **cuenta de Analytics**y seleccione **Enviar**. Los resultados del envío están disponibles en la ventana de resultados de Data Lake Tools para Visual Studio cuando se completa el envío.

    ![Enviar proyecto U-SQL de Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. Para ver el estado del trabajo más reciente y actualizar la pantalla, haga clic en **Actualizar**. Si el trabajo se completa correctamente, aparece **Grafo de trabajo**, **Operaciones de metadatos**, **Historial de estado** y **Diagnóstico**:

    ![Gráfico de rendimiento del trabajo de Data Lake Analytics de U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Resumen del trabajo** muestra el resumen del trabajo.   
   * **Detalles del trabajo** muestra información más específica acerca del trabajo, lo que incluye el script, los recursos y los vértices.
   * **Grafo de trabajo** muestra el progreso del trabajo.
   * **Operaciones de metadatos** muestra todas las acciones que se realizaron en el catálogo de U-SQL.
   * **Datos** muestra todas las entradas y salidas.
   * **Diagnósticos** proporciona un análisis avanzado para la optimización de rendimiento y la ejecución del trabajo.

### <a name="to-check-job-state"></a>Para comprobar el estado del trabajo

1. En el Explorador de servidores, seleccione **Azure** > **Data Lake Analytics**. 
2. Expanda el nombre de la cuenta de Data Lake Analytics.
3. Haga doble clic en **Trabajos**.
4. Seleccione el trabajo que envió anteriormente.

### <a name="to-see-the-output-of-a-job"></a>Para ver la salida de un trabajo

1. En el Explorador de servidores, busque el trabajo que ha enviado.
2. Haga clic en la pestaña **Datos** .
3. En el pestaña **Salidas de trabajo**, seleccione el archivo `"/data.csv"`.

## <a name="next-steps"></a>Pasos siguientes

* [Prueba y depuración de trabajos U-SQL mediante la ejecución local y el SDK de U-SQL para Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
* [Depuración de código de C# en los trabajos de U-SQL con las Herramientas de Azure Data Lake para Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Uso de Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
