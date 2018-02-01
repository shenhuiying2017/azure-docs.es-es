---
title: Carga de datos en Azure SQL Data Warehouse mediante Azure Data Factory | Microsoft Docs
description: Uso de Azure Data Factory para copiar datos en Azure SQL Data Warehouse
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Carga de datos en Azure SQL Data Warehouse mediante Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) es una base de datos de escalado horizontal y basada en la nube que es capaz de procesar volúmenes masivos de datos (tanto relacionales como no relacionales).  Basado en nuestra arquitectura de procesamiento paralelo masivo (MPP), SQL Data Warehouse está mejorado para controlar las cargas de trabajo empresariales.  Ofrece elasticidad en la nube con la flexibilidad para escalar almacenamiento y proceso de forma independiente.

La introducción a Azure SQL Data Warehouse es ahora más fácil que nunca usando **Azure Data Factory**.  Azure Data Factory es un servicio de integración de datos basado en la nube completamente administrado que se puede utilizar para rellenar una instancia de SQL Data Warehouse con los datos del sistema existente, lo que ahorra tiempo durante la evaluación de SQL Data Warehouse y la creación de soluciones de análisis. Estos son los beneficios claves de cargar datos en Azure SQL Data Warehouse usando Azure Data Factory:

* **Fácil de configurar**: con un asistente intuitivo en 5 pasos sin necesidad de scripting.
* **Amplia compatibilidad para el almacenamiento de datos**: compatibilidad integrada para un amplio conjunto de almacenes de datos tanto locales como basados en la nube, consulte la lista detallada en la tabla de [almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro y conforme con la normativa**: los datos se transfieren a través de HTTPS o ExpressRoute y la presencia del servicio global garantiza que los datos nunca abandonan el límite geográfico
* **Rendimiento sin precedentes mediante PolyBase**: el uso de Polybase es la forma más eficaz de mover datos a Azure SQL Data Warehouse. Mediante la característica de blob de almacenamiento provisional, puede alcanzar velocidades de carga altas para todos los tipos de almacenes de datos además de Azure Blob Storage y Data Lake Store, que es compatible con Polybase de forma predeterminada. Obtenga detalles en [Rendimiento de la actividad de copia](copy-activity-performance.md).

En este artículo se muestra cómo usar la herramienta para copiar datos de Data Factory para **cargar datos de Azure SQL Database en Azure SQL Data Warehouse**. Puede seguir los mismos pasos para copiar datos de otros tipos de almacenes de datos.

> [!NOTE]
>  Para información general sobre las funcionalidades de Data Factory para la copia de datos hacia Azure SQL Data Warehouse y desde él, consulte el artículo [Copia de datos con Azure SQL Data Warehouse como origen o destino mediante Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>requisitos previos

* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Azure SQL Data Warehouse**. Este almacén de datos contiene los datos que se copian de SQL Database. Si no tiene ninguna instancia de Azure SQL Data Warehouse, consulte el artículo [Creación de una instancia de SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para ver los pasos para su creación.
* **Azure SQL Database**. En este tutorial se copian datos de una instancia de Azure SQL Database con los datos de ejemplo de Adventure Works LT y puede crear una con la información que aparece en el artículo [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md). 
* **Cuenta de Azure Storage**. Azure Storage se usa como blob de **almacenamiento provisional** en la operación de copia masiva. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, haga clic en **NUEVO**, en **Datos y Análisis** y en **Data Factory**. 
   
   ![New->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, proporcione los valores tal como se muestra en la captura de pantalla siguiente:
      
     ![Página New data factory (Nueva factoría de datos)](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **Nombre**. Escriba un nombre único global para la factoría de datos. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **Suscripción**. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
    * **Grupo de recursos**. Seleccione un grupo de recursos existente en la lista desplegable o seleccione la opción **Crear nuevo** y escriba el nombre de un grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versión**. Seleccione **V2 (versión preliminar)**.
    * **Ubicación**. Seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) que usa la factoría de datos pueden encontrarse en otras ubicaciones o regiones. 

3. Haga clic en **Create**(Crear).
4. Una vez completada la creación, vaya a la factoría de datos y verá la página **Data Factory** tal como se muestra en la imagen. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de integración de datos en una pestaña independiente.
   
   ![Página principal Factoría de datos](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carga de datos en Azure SQL Data Warehouse

1. En la página de introducción, haga clic en el icono **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Icono de la herramienta Copy Data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades) de la herramienta Copy Data, especifique **CopyFromSQLToSQLDW** en **Task name** (Nombre de la tarea) y haga clic **Next** (Siguiente). 

    ![Herramienta Copy Data: página de propiedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **Azure SQL Database** y haga clic en **Next** (Siguiente).

    ![Página Source data store (Almacén de datos de origen)](./media/load-azure-sql-data-warehouse/specify-source.png)
4. En la página **Specify the Azure SQL database** (Especificar la base de datos SQL de Azure), lleve a cabo los siguientes pasos: 
    1. Seleccione el servidor de Azure SQL Server en **Server name** (Nombre del servidor).
    2. Seleccione la base de datos SQL de Azure en **Database name** (Nombre de la base de datos).
    3. Especifique el nombre de usuario en **User name** (Nombre de usuario).
    4. Especifique la contraseña del usuario en **Password** (Contraseña).
    5. Haga clic en **Next**. 

        ![Especificación de la base de datos SQL de Azure](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. En la página **Select tables from which to copy the data or use a custom query** (Seleccionar tablas de donde copiar los datos o usar una consulta personalizada), filtre las tablas mediante la especificación de **SalesLT** en el cuadro de entrada y, luego, active la casilla de verificación **(Select all)** (Seleccionar todo) para seleccionar todas las tablas y haga clic en **Next** (Siguiente). 

    ![Elegir archivo o carpeta de entrada](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure SQL Data Warehouse** y haga clic en **Next** (Siguiente). 

    ![Página Destination data store (Almacén de datos de destino)](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. En la página **Specify the Azure SQL Data Warehouse** (Especificar la instancia de Azure SQL Data Warehouse), lleve a cabo los siguientes pasos: 

    1. Seleccione el servidor de Azure SQL Server en **Server name** (Nombre del servidor).
    2. Seleccione la instancia de Azure SQL Data Warehouse en el campo **Database name** (Nombre de la base de datos).
    3. Especifique el nombre de usuario en **User name** (Nombre de usuario).
    4. Especifique la contraseña del usuario en **Password** (Contraseña).
    5. Haga clic en **Next**. 

        ![Especificación de la instancia de Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. En la página **Table mapping** (Asignación de tabla), revise y haga clic en **Next** (Siguiente). Se muestra una asignación de tabla inteligente que asigna las tablas de origen y de destino basándose en nombres de tablas. Si la tabla no existe en el destino, Azure Data Factory crea una con el mismo nombre de manera predeterminada. También puede optar por la asignación a una tabla existente. 

    > [!NOTE]
    > La creación automática de tabla para el receptor de SQL Data Warehouse se aplica cuando el origen es SQL Server o Azure SQL Database. Si copia datos desde otros almacenes de datos de origen, debe crear previamente el esquema en la instancia de Azure SQL Data Warehouse receptora antes de copiar los datos.

    ![Página Table mapping (Asignación de tabla)](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. En la página **Schema mapping** (Asignación de esquema), revise y haga clic en **Next** (Siguiente). La asignación inteligente se basa en el nombre de las columnas. Si opta por permitir que Data Factory cree automáticamente las tablas, puede producirse la conversión de tipos de datos adecuada en caso de que haga falta corregir la incompatibilidad entre los almacenes de origen y de destino. Si hay una conversión de tipos de datos no compatibles entre las columnas de origen y de destino, verá un mensaje de error junto a la tabla correspondiente.

    ![Página Schema mapping (Asignación de esquema)](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. En la página **Settings** (Configuración), seleccione una instancia de Azure Storage en la lista desplegable **Storage account name** (Nombre de la cuenta de almacenamiento). Se usa para almacenar provisionalmente los datos antes de cargarlos en SQL Data Warehouse mediante PolyBase. Una vez que se realiza la copia, los datos provisionales en almacenamiento se limpiarán automáticamente. 

    En "Advanced settings" (Configuración avanzada), desactive la opción "Use type default" (Usar tipo predeterminado).

    ![Página Configuración](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. En la página **Summary** (Resumen), revise la configuración y haga clic en **Next** (Siguiente).

    ![Página de resumen](./media/load-azure-sql-data-warehouse/summary-page.png)
13. En la página **Deployment** (Implementación), haga clic en **Monitor** (Supervisión) para supervisar la canalización (tarea).

    ![Página Deployment (Implementación)](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. En la columna **Actions** (Acciones), se muestran los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización. 

    ![La supervisión de la canalización se ejecuta](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Para ver las ejecuciones de actividad asociadas a la de la canalización, haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Hay diez actividades de copia en la canalización y cada una de ellas copia una tabla de datos. Para volver a la vista de ejecuciones de canalización, haga clic en el vínculo **Pipelines** (Canalizaciones) de la parte superior. Haga clic en **Refresh** (Actualizar) para actualizar la lista. 

    ![Supervisión de las ejecuciones de actividad](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Puede supervisar cada detalle de la ejecución de la actividad de copia haciendo clic en el vínculo **Details** (Detalles) en **Actions** (Acciones) en la vista de supervisión de la actividad. Muestra información, incluidos el volumen de datos copiados desde el origen al receptor, el rendimiento, los pasos que recorre con su duración correspondiente y las configuraciones usadas.

    ![Detalles de la supervisión de la ejecución de actividad](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>pasos siguientes

Continúe en el artículo siguiente para información sobre la compatibilidad de Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Copia de datos con Azure SQL Data Warehouse como origen o destino mediante Azure Data Factory](connector-azure-sql-data-warehouse.md)