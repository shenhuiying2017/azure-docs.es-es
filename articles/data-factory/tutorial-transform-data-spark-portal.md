---
title: "Transformación de datos mediante Spark en Azure Data Factory | Microsoft Docs"
description: Este tutorial proporciona instrucciones detalladas para transformar los datos mediante el uso de la actividad de Spark en Azure Data Factory.
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformación de datos en la nube mediante la actividad de Spark en Azure Data Factory
En este tutorial, se usa Azure Portal para crear una canalización de Data Factory que transforma los datos mediante la actividad de Spark y un servicio vinculado a HDInsight a petición. En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos. 
> * Crear una canalización con la actividad de Spark
> * Desencadenamiento de una ejecución de la canalización
> * Supervisión de la ejecución de la canalización

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos
* **Cuenta de Azure Storage**. Debe crear un script de Python y un archivo de entrada y cargarlos en Azure Storage. La salida del programa Spark se almacena en esta cuenta de almacenamiento. El clúster de Spark a petición usa la misma cuenta de almacenamiento que el almacenamiento principal.  
* **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Carga del script de Python en la cuenta de Blob Storage
1. Cree un archivo de Python denominado **WordCount_Spark.py** con el siguiente contenido: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Reemplace **&lt;storageaccountname&gt;** por el nombre de la cuenta de Azure Storage. A continuación, guarde el archivo. 
3. En Azure Blob Storage, cree un contenedor denominado **adftutorial** si no existe. 
4. Cree una carpeta llamada **spark**.
5. Cree una subcarpeta denominada **script** en la carpeta **spark**. 
6. Cargue el archivo **WordCount_Spark.py** a la subcarpeta **script**. 


### <a name="upload-the-input-file"></a>Carga del archivo de entrada
1. Cree un archivo denominado **minecraftstory.txt** con algo de texto. El programa Spark contará el número de palabras de este texto. 
2. Cree una subcarpeta denominada `inputfiles` en la carpeta `spark`. 
3. Cargue `minecraftstory.txt` a la subcarpeta `inputfiles`. 

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Error de nombre no disponible](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
      En algunos de los pasos de esta guía de inicio rápido se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. Actualmente, Data Factory V2 le permite crear factorías de datos solo en la región Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    ![Página principal Factoría de datos](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de la interfaz de usuario de Data Factory en una pestaña independiente.

## <a name="create-linked-services"></a>Crear servicios vinculados
En esta sección, deberá crear dos servicios vinculados: 
    
- Un **servicio vinculado a Azure Storage** que vincule una cuenta de Azure Storage con la factoría de datos. Este almacenamiento lo usa el clúster HDInsight a petición. También contiene el script de Spark que se ejecutará. 
- Un **servicio vinculado de HDInsight a petición**. Azure Data Factory crea automáticamente un clúster de HDInsight, ejecuta el programa Spark y, luego, eliminar el clúster HDInsight después de estar inactivo durante un período de configuración previa. 

### <a name="create-an-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage

1. En la página de **introducción**, cambie a la pestaña **Edit** (Editar) del panel izquierdo tal como se muestra en la siguiente imagen: 

    ![Icono Create pipeline (Crear canalización)](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Haga clic en **Connections** (Conexiones) en la parte inferior de la ventana y haga clic en **+ New** (+ Nuevo). 

    ![Botón para nueva conexión](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure Blob Storage** y haga clic en **Continue** (Continuar). 

    ![Seleccionar Azure Blob Storage](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Seleccione el **nombre de la cuenta de Azure Storage** y haga clic en **Save** (Guardar). 

    ![Especificar cuenta de Blob Storage](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Creación de un servicio vinculado de HDInsight a petición

1. Haga clic en **+ New** (+ Nuevo) una vez más para crear otro servicio vinculado. 
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure HDInsight** y haga clic en **Continue** (Continuar). 

    ![Seleccionar Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

    1. Escriba **AzureHDInsightLinkedService** como **nombre**.
    2. Confirme que **On-demand HDInsight** (HDInsight a petición) está seleccionado en **tipo**.
    3. Seleccione **AzureStorage1** como **Servicio vinculado de Azure Storage**. Este servicio vinculado lo creó anteriormente. Si usó un nombre diferente, especifique el nombre correcto de este campo. 
    4. Seleccione **spark** como **tipo de clúster**.
    5. Escriba el **identificador de la entidad de servicio** que tenga permiso para crear un clúster de HDInsight. Esta entidad de servicio debe ser miembro del rol de colaborador de la suscripción o del grupo de recursos en el que se crea el clúster. Consulte [Creación de una aplicación de Azure Active Directory y una entidad de servicio](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obtener detalles.
    6. Escriba la **clave de la entidad de servicio**. 
    7. Seleccione el mismo grupo de recursos que utilizó al crear la factoría de datos para **Grupo de recursos**. El clúster de Spark se crea en este grupo de recursos. 
    8. Expanda **OS type** (Tipo de SO).
    9. Escriba un **nombre** para el **usuario** del clúster. 
    10. Escriba la **contraseña** del usuario. 
    11. Haga clic en **Save**(Guardar). 

        ![Configuración del servicio vinculado de HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight tiene limitaciones en el número total de núcleos que se pueden utilizar en cada región de Azure que admite. Para el servicio vinculado de HDInsight a petición, el clúster de HDInsight se creará en la misma ubicación que Azure Storage usó como almacenamiento principal. Asegúrese de que dispone de suficientes cuotas de núcleo para que el clúster se cree correctamente. Para obtener más información, consulte [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Crear una canalización

2. Haga clic en el botón + (signo más) y haga clic en **Pipeline** (Canalización) en el menú.

    ![Menú New pipeline (Nueva canalización)](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. En el cuadro de herramientas **Activities** (Actividades), expanda **HDInsight**, arrastre la actividad **Spark** desde allí para colocarla en la superficie del diseñador de canalizaciones. 

    ![Arrastrar y colocar la actividad Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. En las propiedades de la ventana de la actividad **Spark** de la parte inferior, realice los pasos siguientes: 

    1. Vaya a la pestaña **HDI Cluster** (Clúster de HDI).
    2. Seleccione el servicio **AzureHDInsightLinkedService** que creó en el paso anterior. 
        
    ![Especificar servicio vinculado a HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Vaya a la pestaña **Script/Jar** y realice los pasos siguientes: 

    1. Seleccione **AzureStorage1** como **servicio vinculado de trabajo**.
    2. Haga clic en **Browse Storage** (Examinar almacenamiento). 
    3. Vaya a la carpeta **adftutorial/spark/script** , seleccione **WordCount_Spark.py** y haga clic en **Finalizar**.      

    ![Especificar script de Spark](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. Para comprobar la canalización, haga clic en el botón **Validate** (Comprobar) en la barra de herramientas. Haga clic en el botón **flecha derecha** (>>) para cerrar la ventana de comprobación. 
    
    ![Botón Validate (Comprobar)](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Haga clic en **Publicar**. La interfaz de usuario de Data Factory permite publicar entidades (servicios vinculados y canalizaciones) en el servicio Azure Data Factory. 
    
    ![Botón Publicar](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización
Haga clic en **Trigger** (Desencadenar) en la barra de herramientas y en **Trigger Now** (Desencadenar ahora). 

![Trigger now (Desencadenar ahora)](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Vaya a la pestaña **Monitor** (Supervisar). Confirme que ve una ejecución de canalización. Se tardan veinte minutos aproximadamente en crear un clúster de Spark. 

    ![La supervisión de la canalización se ejecuta](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Haga clic en **Actualizar** periódicamente para comprobar el estado de la ejecución de canalización. 

    ![Estados de la ejecución de canalización](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. Para ver las ejecuciones de actividad asociadas con la de esta canalización, haga clic en la acción **View Activity Runs** (Ver ejecuciones de actividad) de la columna Actions (Acciones). Puede volver a la vista de ejecuciones de canalización. Para ello haga clic en el vínculo **Pipelines** (Canalizaciones) en la parte superior.

    ![Ver ejecuciones de actividad](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Comprobación del resultado
Compruebe que se crea el archivo de salida en la carpeta spark/otuputfiles/wordcount del contenedor adftutorial. 

![Comprobar salida](./media/tutorial-transform-data-spark-portal/verity-output.png)

El archivo debe incluir todas las palabras del archivo de texto de entrada y el número de veces que cada palabra aparecía en el archivo. Por ejemplo:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo permite transformar datos mediante una actividad de Spark y un servicio vinculado de HDInsight a petición. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos. 
> * Crear una canalización con la actividad de Spark
> * Desencadenamiento de una ejecución de la canalización
> * Supervisión de la ejecución de la canalización

Pase al siguiente tutorial para obtener información sobre cómo puede transformar datos mediante la ejecución del script de Hive en un clúster de Azure HDInsight que se encuentra en una red virtual. 

> [!div class="nextstepaction"]
> [Tutorial: transformación de datos con Hive en Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).





