---
title: Copia de datos mediante la herramienta Copy Data de Azure | Microsoft Docs
description: "Cree una factoría de datos de Azure y, luego, use la herramienta Copy Data para copiar los datos de una carpeta de una instancia de Azure Blob Storage a otra carpeta."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: aa9cdba4f4e891d5321eb8af6349d8b141faee03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Uso de la herramienta Copy Data para copiar datos 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](quickstart-create-data-factory-copy-data-tool.md)

En esta guía de inicio rápido, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copy Data para crear una canalización que copia datos de una carpeta de una instancia de Azure Blob Storage a otra carpeta. 

> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introduction to Azure Data Factory](data-factory-introduction.md) antes de seguir los pasos de esta guía de inicio rápido. 
>
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio, que está disponible con carácter general, consulte [la introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Seleccione **Nuevo** en el menú de la izquierda, seleccione **Datos y análisis** y, después, seleccione **Data Factory**. 
   
   ![Selección de la factoría de datos en el panel Nuevo](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, escriba **ADFTutorialDataFactory** en **Nombre**. 
      
   ![Página de la nueva factoría de datos](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser *único de forma global*. Si ve el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, **&lt;suNombre&gt;ADFTutorialDataFactory**) e intente crearlo de nuevo. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   ![Mensaje de error cuando un nombre no está disponible](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. En **Suscripción**, seleccione la suscripción de Azure donde desea crear la factoría de datos. 
4. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista. 
   - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
   Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. En **Versión**, seleccione **V2 (versión preliminar)**.
5. En **Ubicación**, seleccione la ubicación de la factoría de datos. 

   La lista muestra únicamente las ubicaciones admitidas. Los almacenes de datos (como Azure Storage y Azure SQL Database) y los procesos (como Azure HDInsight) que usa Data Factory pueden encontrarse en otras ubicaciones o regiones.

6. Seleccione **Anclar al panel**.     
7. Seleccione **Crear**.
8. En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory): 

    ![Icono "Deploying Data Factory" (Implementando Data Factory)](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory**. Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente.
   
   ![Página principal de la factoría de datos, con el icono Author & Monitor (Creación y supervisión)](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Inicio de la herramienta Copy Data

1. En la página **Let's get started** (Comencemos), seleccione el icono **Copy Data** para iniciar la herramienta Copy Data. 

   ![Icono Copy Data](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. En la página **Properties** (Propiedades) de la herramienta Copy Data, seleccione **Next** (Siguiente). Puede especificar un nombre para la canalización y su descripción en esta página. 

   ![Página de propiedades](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. En la página **Source data store** (Almacén de datos de origen), seleccione **Azure Blob Storage** y después seleccione **Next** (Siguiente).

   ![Página del almacén de datos de origen](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. En la página **Specify the Azure Blob storage account** (Especificar la cuenta de Azure Blob Storage), seleccione el **nombre de la cuenta de Storage** y, después, seleccione **Next** (Siguiente). 

   ![Página de la especificación de la cuenta de Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. En la página **Choose the input file or folder** (Elegir el archivo o la carpeta de entrada), complete los siguientes pasos:

   a. Vaya a la carpeta **adftutorial/input**.

   b. Seleccione el archivo **emp.txt**.

   c. Seleccione **Choose**(Elegir). Puede hacer doble clic **emp.txt** para omitir este paso.

   d. Seleccione **Siguiente**. 

   ![Página de elección del archivo o la carpeta de entrada](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. En la página **File format settings** (Configuración de formato de archivo), observe que la herramienta detecta automáticamente los delimitadores de columna y fila, y seleccione **Next** (Siguiente). También puede obtener una vista previa de los datos y ver el esquema de los datos de entrada en esta página. 

   ![Página de la configuración de formato de archivo](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. En la página **Destination data store** (Almacén de datos de destino), seleccione **Azure Blob Storage** y después **Next** (Siguiente). 

   ![Página del almacén de datos de destino](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. En la página **Specify the Azure Blob storage account** (Especificar la cuenta de Azure Blob Storage), seleccione la cuenta de Azure Blob Storage y, después, **Next** (Siguiente). 

   ![Página de la especificación de la cuenta de Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. En la página **Choose the output file or folder** (Elegir el archivo o la carpeta de salida), complete los siguientes pasos: 

   a. Escriba **adftutorial/output** como la ruta de acceso de la carpeta.

   b. Escriba **emp.txt** como nombre de archivo.

   c. Seleccione **Siguiente**. 

   ![Página de elección del archivo o la carpeta de salida](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. En la página **File format settings** (Configuración de formato de archivo), seleccione **Next** (Siguiente). 

    ![Página de la configuración de formato de archivo](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. En la página **Settings** (Configuración), seleccione **Next** (Siguiente). 

    ![Página de configuración](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Revise toda la configuración en la página **Summary** (Resumen) y seleccione **Next** (Siguiente). 

    ![Página de resumen](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. En la página **Deployment complete** (Implementación finalizada), haga clic en **Monitor** (Supervisión) para supervisar la canalización que ha creado. 

    ![Página de implementación completada](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. La aplicación cambia a la pestaña **Monitor** (Supervisión). En esta pestaña verá el estado de la canalización. Seleccione **Refresh** (Actualizar) para actualizar la lista. 
    
    ![Pestaña para supervisar las ejecuciones de la canalización, con el botón Actualizar](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en la columna **Actions** (Acciones). La canalización tiene una única actividad de tipo **Copy** (Copia). 

    ![Lista de ejecuciones de actividad](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Para más información sobre la operación de copia, seleccione el vínculo **Details** (Detalles) (imagen de gafas) de la columna **Actions** (Acciones). Para más información sobre las propiedades, consulte [Introducción a la actividad de copia](copy-activity-overview.md). 

    ![Detalles de la operación de copia](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Compruebe que se crea el archivo **emp.txt** en la carpeta **output** del contenedor **adftutorial**. Si no existe la carpeta de salida, el servicio Data Factory la crea automáticamente. 
18. Cambie a la pestaña **Edit** (Editar) para editar los servicios vinculados, los conjuntos de datos y las canalizaciones. Para saber cómo editarlos en la interfaz de usuario de Data Factory, consulte [Crear una factoría de datos mediante Azure Portal](quickstart-create-data-factory-portal.md).

    ![Pestaña Edit (Editar)](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Para más información sobre el uso de Data Factory en otros escenarios, consulte los siguientes [tutoriales](tutorial-copy-data-portal.md). 