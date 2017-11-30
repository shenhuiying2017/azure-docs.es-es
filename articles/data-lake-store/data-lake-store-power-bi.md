---
title: "Análisis de datos en Data Lake Store con Power BI | Microsoft Docs"
description: "Uso de Power BI para analizar datos almacenados en el Almacén de Azure Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 0c77ccd29e3e22005c3339ed4e89dccfed725fa0
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Análisis de datos en Almacén de Data Lake mediante Power BI
En este artículo aprenderá a usar Power BI Desktop para analizar y visualizar los datos almacenados en el Almacén de Azure Data Lake.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md). En este artículo se supone que ya creó una cuenta de Data Lake Store denominada **mybidatalakestore** y le cargó un archivo de datos de ejemplo (**Drivers.txt**). Este archivo de ejemplo está disponible para su descarga en [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)(Repositorio Git de Azure Data Lake).
* **Power BI Desktop**. Puede descargarla del [Centro de descarga de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Creación de un informe en Power BI Desktop
1. Inicie Power BI Desktop en el equipo.
2. En la cinta **Inicio**, haga clic en **Obtener datos** y luego en Más. En el cuadro de diálogo **Obtener datos**, haga clic en **Azure**, en **Azure Data Lake Store** y luego en **Conectar**.
   
    ![Conectar a Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Conectar a Data Lake Store")
3. Si ve un cuadro de diálogo que indica que el conector está en fase de desarrollo, opte por continuar.
4. En el cuadro de diálogo **Microsoft Azure Data Lake Store**, indique la dirección URL a su cuenta de Data Lake Store y haga clic en **Aceptar**.
   
    ![Dirección URL de Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Dirección URL de Data Lake Store")
5. En el siguiente cuadro de diálogo, haga clic en **Iniciar sesión** para iniciar sesión en la cuenta del Almacén de Data Lake. Se le redirigirá a la página de inicio de sesión de su organización. Siga las indicaciones para iniciar sesión en la cuenta.
   
    ![Iniciar sesión en Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Iniciar sesión en Data Lake Store")
6. Cuando haya iniciado sesión correctamente, haga clic en **Conectar**.
   
    ![Conectar a Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Conectar a Data Lake Store")
7. El siguiente cuadro de diálogo muestra el archivo que cargó a su cuenta del Almacén de Data Lake. Compruebe la información y haga clic en **Cargar**.
   
    ![Cargar datos desde Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Cargar datos desde Data Lake Store")
8. Después de cargar correctamente los datos en Power BI, verá los siguientes campos en la pestaña **Campos** .
   
    ![Campos importados](./media/data-lake-store-power-bi/imported-fields.png "Campos importados")
   
    Pero, para visualizar y analizar los datos, es preferible que estos estén disponibles por los campos siguientes.
   
    ![Campos deseados](./media/data-lake-store-power-bi/desired-fields.png "Campos deseados")
   
    En los pasos siguientes, se actualizará la consulta para convertir los datos importados al formato deseado.
9. En la cinta **Inicio**, haga clic en **Editar consultas**.
   
    ![Editar consultas](./media/data-lake-store-power-bi/edit-queries.png "Editar consultas")
10. En el Editor de consultas, en la columna **Contenido**, haga clic en **Binario**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query1.png "Editar consultas")
11. Verá un icono de archivo, que representa el archivo **Drivers.txt** que cargó. Haga clic en el archivo y luego en **CSV**.    
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query2.png "Editar consultas")
12. Debería ver una salida como la siguiente. Los datos están ahora disponibles en un formato que puede usar para crear visualizaciones.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query3.png "Editar consultas")
13. En la cinta **Inicio**, haga clic en **Cerrar y aplicar** y luego en **Cerrar y aplicar**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/load-edited-query.png "Editar consultas")
14. Después de actualizar la consulta, la pestaña **Campos** mostrará los nuevos campos disponibles para su visualización.
    
    ![Campos actualizados](./media/data-lake-store-power-bi/updated-query-fields.png "Campos actualizados")
15. Vamos a crear un gráfico circular para representar los conductores de cada ciudad de un país determinado. Para ello, realice las selecciones siguientes.
    
    1. En la pestaña Visualizaciones, haga clic en el símbolo de un gráfico circular.
       
        ![Crear gráfico circular](./media/data-lake-store-power-bi/create-pie-chart.png "Crear gráfico circular")
    2. Las columnas que se van a usar son **Columna 4** (nombre de la ciudad) y **Columna 7** (nombre del país). Arrastre estas columnas de la pestaña **Campos** a la pestaña **Visualizaciones**, tal y como se muestra a continuación.
       
        ![Creación de visualizaciones](./media/data-lake-store-power-bi/create-visualizations.png "Creación de visualizaciones")
    3. El gráfico circular ahora debe ser similar al que se muestra a continuación.
       
        ![Gráfico circular](./media/data-lake-store-power-bi/pie-chart.png "Crear visualizaciones")
16. Si selecciona un país específico en los filtros de nivel de página, ahora puede ver el número de conductores de cada ciudad del país seleccionado. Por ejemplo, en la pestaña **Visualizaciones**, en **Filtros de nivel de página**, seleccione **Brasil**.
    
    ![Seleccionar un país](./media/data-lake-store-power-bi/select-country.png "Seleccionar un país")
17. El gráfico circular se actualiza automáticamente para mostrar los conductores de las ciudades de Brasil.
    
    ![Controladores en un país](./media/data-lake-store-power-bi/driver-per-country.png "Controladores por país")
18. En el menú **Archivo**, haga clic en **Guardar** para guardar la visualización como archivo de Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicación del informe en el servicio Power BI
Después de crear las visualizaciones en Power BI Desktop, puede compartirlas con otros usuarios publicándolas en el servicio Power BI. Para obtener instrucciones sobre cómo hacerlo, consulte [Publicar desde Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Otras referencias
* [Análisis de datos en el Almacén de Data Lake con Análisis de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

