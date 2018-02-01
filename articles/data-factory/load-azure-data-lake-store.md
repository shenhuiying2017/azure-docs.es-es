---
title: Carga de datos en Azure Data Lake Store mediante Azure Data Factory | Microsoft Docs
description: Uso de Azure Data Factory para copiar datos en Azure Data Lake Store
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
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Carga de datos en Azure Data Lake Store mediante Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de macrodatos. Azure Data Lake permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos.

Azure Data Factory es un servicio de integración de datos basado en la nube completamente administrado que se puede utilizar para rellenar el lago con los datos del sistema existente, lo que ahorra tiempo durante la creación de soluciones de análisis. Estas son las principales ventajas de cargar datos en Azure Data Lake Store mediante Azure Data Factory:

* **Fácil de configurar**: con un asistente intuitivo en 5 pasos sin necesidad de scripting.
* **Amplia compatibilidad para el almacenamiento de datos**: compatibilidad integrada para un amplio conjunto de almacenes de datos tanto locales como basados en la nube, consulte la lista detallada en la tabla de [almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro y conforme con la normativa**: los datos se transfieren a través de HTTPS o ExpressRoute y la presencia del servicio global garantiza que los datos nunca abandonan el límite geográfico
* **Alto rendimiento**: hasta 1 Gbps de velocidad de carga de datos en Azure Data Lake Store. Obtenga detalles en [Rendimiento de la actividad de copia](copy-activity-performance.md).

En este artículo se muestra cómo utilizar la herramienta para copiar datos de la factoría de datos y **cargar datos de Amazon S3 en Azure Data Lake Store**. Puede seguir los mismos pasos para copiar datos de otros tipos de almacenes de datos.

> [!NOTE]
>  Para obtener información general acerca de las funcionalidades de Data Factory para copiar datos hacia y desde Azure Data Lake Store, consulte el artículo [Copia de datos con Azure Data Lake Store como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md).
>
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>requisitos previos

* **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Azure Data Lake Store**. Si no tiene una cuenta de Data Lake Store, consulte el artículo [Creación de una cuenta de Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) para ver los pasos para su creación.
* **Amazon S3**. En este artículo se muestra cómo copiar datos de Amazon S3. Puede usar otros almacenes de datos siguiendo los mismos pasos.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, haga clic en **NUEVO**, en **Datos y Análisis** y en **Data Factory**. 
   
   ![New->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, proporcione los valores tal como se muestra en la captura de pantalla siguiente: 
      
     ![Página New data factory (Nueva factoría de datos)](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **Nombre**. Escriba un nombre único global para la factoría de datos. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
            `Data factory name "LoadADLSDemo" is not available`

    * **Suscripción**. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
    * **Grupo de recursos**. Seleccione un grupo de recursos existente en la lista desplegable o seleccione la opción **Crear nuevo** y escriba el nombre de un grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version**. Seleccione **V2 (versión preliminar)**.
    * **Ubicación**. Seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos (Azure Data Lake Store, Azure Storage, Azure SQL Database, etc.) que usa la factoría de datos pueden encontrarse en otras ubicaciones o regiones.

3. Haga clic en **Create**(Crear).
4. Una vez completada la creación, vaya a la factoría de datos y verá la página **Data Factory** tal como se muestra en la imagen. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de integración de datos en una pestaña independiente. 
   
   ![Página principal Factoría de datos](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Carga de datos en Azure Data Lake Store

1. En la página de introducción, haga clic en el icono **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Icono de la herramienta Copy Data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades) de la herramienta Copy Data, especifique **CopyFromAmazonS3ToADLS** en **Task name** (Nombre de la tarea) y haga clic **Next** (Siguiente). 

    ![Herramienta Copy Data: página de propiedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **Amazon S3** y haga clic en **Next** (Siguiente).

    ![Página Source data store (Almacén de datos de origen)](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. En la página **Specify Amazon S3 connection** (Especificar conexión de Amazon S3), siga estos pasos: 
    1. Especifique **Access Key ID** (Identificador de clave de acceso).
    2. Especifique **Secret Access Key** (Clave de acceso secreta).
    3. Haga clic en **Next**. 

        ![Especificación de la cuenta de Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. En la página **Choose the input file or folder** (Elegir el archivo o la carpeta de entrada), vaya a la carpeta o el archivo que desea copiar, seleccione y haga clic en **Choose** (Elegir) y luego en **Next** (Siguiente). 

    ![Elegir archivo o carpeta de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure Data Lake Store** y haga clic en **Next** (Siguiente). 

    ![Página Destination data store (Almacén de datos de destino)](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. En esta página, elija el comportamiento de copia seleccionando las opciones **Copy files recursively** (Copiar archivos de forma recursiva) y **Binary copy** (Copia binaria). Haga clic en **Next**.

    ![Especificación de carpeta de salida](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. En la página **Specify Data Lake Store connection** (Especificar conexión de Data Lake Store), siga estos pasos: 

    1. Seleccione su instancia de Data Lake Store para **Data Lake Store account name** (Nombre de la cuenta de Data Lake Store).
    2. Especifique la información de la entidad de servicio, incluido **Tenant** (Inquilino), **Service principal ID** (Identificador de entidad de servicio) y **Service principal key** (Clave de entidad de servicio).
    3. Haga clic en **Next**. 

    > [!IMPORTANT]
    > En este tutorial, utilizará una **entidad de servicio** para autenticar el almacén de Data Lake. Siga la instrucción indicada [aquí](connector-azure-data-lake-store.md#using-service-principal-authentication) y asegúrese de que concede el permiso adecuado de entidad de servicio en Azure Data Lake Store.

    ![Especificación de una cuenta de Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. En la página **Choose the output file or folder** (Elegir el archivo o la carpeta de salida), especifique **copyfroms3** y haga clic en **Next** (Siguiente). 

    ![Especificación de carpeta de salida](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. En la página **Settings** (Configuración), haga clic en **Next** (Siguiente). 

    ![Página Configuración](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. En la página **Summary** (Resumen), revise la configuración y haga clic en **Next** (Siguiente).

    ![Página de resumen](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. En la página **Deployment** (Implementación), haga clic en **Monitor** (Supervisión) para supervisar la canalización (tarea).

    ![Página Deployment (Implementación)](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. En la columna **Actions** (Acciones), se muestran los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización. 

    ![La supervisión de la canalización se ejecuta](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para ver las ejecuciones de actividad asociadas a la de la canalización, haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para volver a la vista de ejecuciones de canalización, haga clic en el vínculo **Pipelines** (Canalizaciones) de la parte superior. Haga clic en **Refresh** (Actualizar) para actualizar la lista. 

    ![Supervisión de las ejecuciones de actividad](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Puede supervisar cada detalle de la ejecución de la actividad de copia haciendo clic en el vínculo **Details** (Detalles) en **Actions** (Acciones) en la vista de supervisión de la actividad. Muestra información, incluidos el volumen de datos copiados desde el origen al receptor, el rendimiento, los pasos que recorre con su duración correspondiente y las configuraciones utilizadas.

    ![Detalles de la supervisión de la ejecución de actividad](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique que los datos se copian en Azure Data Lake Store. 

    ![Verificación de la salida de Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>pasos siguientes

Prosiga en el siguiente artículo para obtener información acerca de la compatibilidad de Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Conector de Azure Data Lake Store](connector-azure-data-lake-store.md)