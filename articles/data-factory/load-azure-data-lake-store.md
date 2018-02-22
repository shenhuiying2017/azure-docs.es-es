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
ms.openlocfilehash: 4446f83563293d0834f241dcca382ccf6ea99403
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Carga de datos en Azure Data Lake Store mediante Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de macrodatos. Azure Data Lake permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta. Los datos se capturan en un único lugar para análisis operativos y exploratorios.

Azure Data Factory es un servicio de integración de datos en la nube totalmente administrado. Puede utilizar el servicio para rellenar la instancia de Data Lake Store con datos del sistema actual y ahorrar tiempo al compilar las soluciones de análisis.

Azure Data Factory ofrece las siguientes ventajas para cargar datos en Azure Data Lake Store:

* **Fácil de configurar**: con un asistente intuitivo en cinco pasos sin necesidad de scripting.
* **Amplia compatibilidad para el almacenamiento de datos**: compatibilidad integrada para un amplio conjunto de almacenes de datos tanto locales como en la nube. Para una lista detallada, consulte la tabla de [almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro y compatible**: los datos se transfieren a través de HTTPS o ExpressRoute. La presencia del servicio global garantiza que los datos nunca abandonan el límite geográfico.
* **Alto rendimiento**: hasta 1 GB/s de velocidad de carga de datos en Azure Data Lake Store. Para más información, consulte el [rendimiento de la actividad de copia](copy-activity-performance.md).

En este artículo se muestra cómo utilizar la herramienta Copy Data de Data Factory para _cargar datos de Amazon S3 en Azure Data Lake Store_. Puede seguir los mismos pasos para copiar datos de otros tipos de almacenes de datos.

> [!NOTE]
> Para más información, consulte [Copia de datos con Azure Data Lake Store como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md).
>
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general (GA), consulte [Rendimiento de la actividad de copia en Azure Data Factory versión 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>requisitos previos

* Suscripción a Azure: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Azure Data Lake Store: si no tiene una cuenta de Data Lake Store, consulte las instrucciones de [Creación de una cuenta de Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: en este artículo se muestra cómo copiar datos de Amazon S3. Puede usar otros almacenes de datos siguiendo los mismos pasos.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú izquierdo, seleccione **Nuevo** > **Datos y análisis** > **Data Factory**:
   
   ![Creación de una factoría de datos](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, proporcione los valores de los campos que se muestran en la imagen siguiente: 
      
   ![Página New data factory (Nueva factoría de datos)](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nombre**: escriba un nombre único global para la factoría de datos de Azure. Si recibe el error "El nombre de la factoría de datos \"LoadADLSDemo\" no está disponible", escriba uno diferente. Por ejemplo, podría utilizar el nombre _**suNombre**_**ADFTutorialDataFactory**. Intente crear de nuevo la factoría de datos. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Data Factory: reglas de nomenclatura](naming-rules.md).
    * **Suscripción**: seleccione la suscripción de Azure donde desea crear la factoría de datos. 
    * **Grupo de recursos**: seleccione un grupo de recursos existente en la lista desplegable o la opción **Crear nuevo** y escriba el nombre de un grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versión**: seleccione **V2 (versión preliminar)**.
    * **Ubicación**: seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos que las factorías de datos usan pueden estar en otras ubicaciones y regiones. Estos almacenes de datos incluyen Azure Data Lake Store, Azure Storage, Azure SQL Database, etc.

3. Seleccione **Crear**.
4. Una vez completada la creación, vaya a la factoría de datos. Verá la página principal de **Factoría de datos**, tal y como se muestra en la siguiente imagen: 
   
   ![Página principal Factoría de datos](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de integración de datos en una pestaña independiente.

## <a name="load-data-into-azure-data-lake-store"></a>Carga de datos en Azure Data Lake Store

1. En la página **Get started** (Introducción), seleccione el icono **Copy Data** (Copiar datos) para iniciar la herramienta para copiar datos: 

   ![Icono de la herramienta Copy Data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades), especifique **CopyFromAmazonS3ToADLS** en el campo **Task name** (Nombre de la tarea) y seleccione **Next** (Siguiente):

    ![Página de propiedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **Amazon S3** y **Next** (Siguiente):

    ![Página Source data store (Almacén de datos de origen)](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. En la página **Specify Amazon S3 connection** (Especificar conexión de Amazon S3), siga estos pasos: 
   1. Especifique el valor de **Access Key ID** (Identificador de clave de acceso).
   2. Especifique el valor de **Secret Access Key** (Clave de acceso secreta).
   3. Seleccione **Siguiente**.
   
   ![Especificación de la cuenta de Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. En la página **Choose the input file or folder** (Elegir archivo o carpeta de entrada), vaya a la carpeta y el archivo que desea copiar. Seleccione la carpeta o el archivo, **Choose** (Elegir) y **Next** (Siguiente):

    ![Elegir archivo o carpeta de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure Data Lake Store** y **Next** (Siguiente):

    ![Página Destination data store (Almacén de datos de destino)](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Elija el comportamiento de copia seleccionando las opciones **Copy files recursively** (Copiar archivos de forma recursiva) y **Binary copy** (Copia binaria). Seleccione **Next** (Siguiente):

    ![Especificación de carpeta de salida](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. En la página **Specify Data Lake Store connection** (Especificar conexión de Data Lake Store), siga estos pasos: 

   1. Seleccione su instancia de Data Lake Store para **Data Lake Store account name** (Nombre de la cuenta de Data Lake Store).
   2. Especifique la información de la entidad de servicio, **Tenant** (Inquilino), **Service principal ID** (Identificador de entidad de servicio) y **Service principal key** (Clave de entidad de servicio).
   3. Seleccione **Siguiente**.
   
   > [!IMPORTANT]
   > En este tutorial, utilizará una _entidad de servicio_ para autenticar Data Lake Store. Asegúrese de conceder a la entidad de servicio los permisos adecuados en Azure Data Lake Store con [estas instrucciones](connector-azure-data-lake-store.md#using-service-principal-authentication).
   
   ![Especificación de una cuenta de Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. En la página **Choose the output file or folder** (Elegir archivo o carpeta de salida) escriba **copyfroms3** como nombre de la carpeta de salida y seleccione **Next** (Siguiente): 

    ![Especificación de carpeta de salida](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. En la página **Settings** (Configuración), seleccione **Next** (Siguiente):

    ![Página Configuración](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. En la página **Summary** (Resumen), revise la configuración y seleccione **Next** (Siguiente):

    ![Página de resumen](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización (tarea):

    ![Página Deployment (Implementación)](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. La columna **Actions** (Acciones) incluye los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización:

    ![La supervisión de la canalización se ejecuta](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para ver las ejecuciones de actividad asociadas con la ejecución de canalización, seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en la columna **Actions** (Acciones). Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **Pipelines** (Canalizaciones) de la parte superior. Seleccione **Refresh** (Actualizar) para actualizar la lista. 

    ![Supervisión de las ejecuciones de actividad](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para supervisar los detalles de la ejecución de cada actividad de copia, seleccione el vínculo **Details** (Detalles) en **Actions** (Acciones) en la vista de supervisión de la actividad. Puede supervisar detalles como el volumen de datos copiados desde el origen al receptor, el rendimiento de los datos, los pasos de ejecución con su duración correspondiente y las configuraciones que se utilizan:

    ![Detalles de la supervisión de la ejecución de actividad](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique que los datos se copian en Azure Data Lake Store: 

    ![Verificación de la salida de Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>pasos siguientes

Prosiga en el siguiente artículo para obtener información acerca de la compatibilidad de Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Conector de Azure Data Lake Store](connector-azure-data-lake-store.md)