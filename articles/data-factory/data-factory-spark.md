---
title: "Invocación de programas Spark desde Azure Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo invocar programas Spark desde Data Factory de Azure mediante la actividad MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 57894bbdd9208f8c32eb65e29f04e2ae723780ca
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocación de programas Spark desde canalizaciones de Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Actividad de Hive](data-factory-hive-activity.md)
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución de Batch de Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad Actualizar recurso de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introducción
La actividad de Spark es una de las [actividades de transformación de datos](data-factory-data-transformation-activities.md) compatibles con Azure Data Factory. Esta actividad ejecuta el programa Spark especificado en el clúster de Apache Spark en Azure HDInsight.    

> [!IMPORTANT]
> - La actividad de Spark no admite clústeres de HDInsight Spark que usan una instancia de Azure Data Lake Store como almacenamiento principal.
> - La actividad de Spark admite solo los clústeres de HDInsight Spark existentes (los suyos propios). No admite un servicio vinculado de HDInsight a petición.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Tutorial: creación de una canalización con actividad de Spark
Estos son los pasos habituales para crear una canalización de Data Factory con una actividad de Spark.  

1. Creación de una factoría de datos.
2. Cree un servicio vinculado de Azure Storage para vincular la instancia de Azure Storage asociada con el clúster de HDInsight Spark a la instancia de Data Factory.     
2. Cree un servicio vinculado de Azure HDInsight para vincular el clúster de Apache Spark en Azure HDInsight con la instancia de Data Factory.
3. Cree un conjunto de datos que haga referencia al servicio vinculado de Azure Storage. Actualmente, debe especificar un conjunto de datos de salida para una actividad incluso si no se produce ninguna salida.  
4. Cree una canalización con la actividad de Spark que haga referencia al servicio vinculado de HDInsight creado en el paso 2. La actividad se configura con el conjunto de datos que creó en el paso anterior como un conjunto de datos de salida. El conjunto de datos de salida es lo que impulsa la programación (cada hora, cada día, etc.). Por lo tanto, debe especificar el conjunto de datos de salida aunque la actividad no produzca realmente una salida.

### <a name="prerequisites"></a>Requisitos previos
1. Cree una **cuenta de Azure Storage de uso general** siguiendo las instrucciones del tutorial: [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).  
2. Cree un **clúster de Apache Spark en Azure HDInsight** siguiendo las instrucciones del tutorial: [Creación de un clúster de Apache Spark en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Asocie la cuenta de Azure Storage creada en el paso 1 con este clúster.  
3. Descargue y revise el archivo de script de Python **test.py** ubicado en: [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Cargue **test.py** en la carpeta **pyFiles** del contenedor **adfspark** de Azure Blob Storage. Cree el contenedor y la carpeta si no existen.

### <a name="create-data-factory"></a>Creación de Data Factory
Comencemos con la creación de la factoría de datos en este paso.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **NUEVO**, en **Datos y Análisis** y en **Data Factory**.
3. En la hoja **Nueva factoría de datos**, escriba **SparkDF** para el nombre.

   > [!IMPORTANT]
   > El nombre de Azure Data Factory debe ser **único de forma global**. Si aparece el error: **El nombre "SparkDF" de factoría de datos no está disponible**. Cambie el nombre de la factoría de datos (por ejemplo, suNombreSparkDFdate) e intente crearla de nuevo. Consulte el tema [Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para conocer las reglas de nomenclatura para los artefactos de Data Factory.   
4. Seleccione la **suscripción de Azure** donde desea crear la factoría de datos.
5. Seleccione un **grupo de recursos** de Azure existente o cree uno nuevo.
6. Seleccione la opción **Anclar al panel**.  
6. Haga clic en **Crear** en la hoja **Nueva fábrica de datos**.

   > [!IMPORTANT]
   > Para crear instancias de Data Factory, es preciso ser miembro del rol [Colaborador de Data Factory](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) en el nivel de grupo de recursos o suscripción.
7. Se ve que la factoría de datos se crea en el **panel** de Azure Portal de la manera siguiente:   
8. Tras crear correctamente la factoría de datos, se ve la página de la factoría de datos, que muestra el contenido de la misma. Si no ve la página de la factoría de datos, haga clic en el icono de la factoría de datos en el panel.

    ![Hoja de la Factoría de datos](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Crear servicios vinculados
En este paso, creará dos servicios vinculados, uno para vincular el clúster de Spark a la factoría de datos y el otro para vincular la instancia de Azure Storage a la factoría de datos.  

#### <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Almacenamiento de Azure con su factoría de datos. Un conjunto de datos creado en un paso más adelante en este tutorial hace referencia a este servicio vinculado. El servicio vinculado de HDInsight que se define en el paso siguiente también hace referencia a este servicio vinculado.  

1. Haga clic en **Crear e implementar** en la hoja **Data Factory** de la factoría de datos. Esto inicia el Editor de Data Factory.
2. Haga clic en **Nuevo almacén de datos** y elija **Azure Storage**.

   ![Nuevo almacén de datos - Azure Storage - menú](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Debería ver el **script JSON** para crear un servicio vinculado de Azure Storage en el editor.

   ![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Reemplace **nombre de cuenta** y **clave de cuenta** con el nombre y la clave de acceso de la cuenta de Azure Storage. Para aprender a obtener la clave de acceso de almacenamiento, consulte la información sobre cómo ver, copiar y regenerar las claves de acceso de almacenamiento en [Administración de la cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#manage-your-storage-account).
5. Para implementar el servicio vinculado, haga clic en **Implementar** en la barra de comandos. Una vez que el servicio vinculado se ha implementado correctamente, la ventana **Draft-1** (Borrador 1) debería desaparecer y, en la vista de árbol de la izquierda, debería aparecer **AzureStorageLinkedService**.

#### <a name="create-hdinsight-linked-service"></a>Creación del servicio vinculado de HDInsight
En este paso, se crea un servicio vinculado de Azure HDInsight para vincular el clúster de Apache Spark con la factoría de datos. El clúster de HDInsight se usa para ejecutar el programa Spark especificado en la actividad de Spark de la canalización en este ejemplo.  

1. Haga clic en **... Más** en la barra de herramientas, haga clic en **Nuevo proceso** y después haga clic en **Clúster de HDInsight**.

    ![Creación del servicio vinculado de HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Copie y pegue el fragmento de código siguiente en la ventana **Borrador 1** . En el Editor de JSON, realice los siguientes pasos:
    1. Especifique el identificador **URI** del clúster de HDInsight Spark. Por ejemplo: `https://<sparkclustername>.azurehdinsight.net/`.
    2. Especifique el nombre del **usuario** que tiene acceso al clúster de Spark.
    3. Especifique la **contraseña** para el usuario.
    4. Especifique el **servicio vinculado de Azure Storage** asociado con el clúster de HDInsight Spark. En este ejemplo, es: **AzureStorageLinkedService**.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - La actividad de Spark no admite clústeres de HDInsight Spark que usan una instancia de Azure Data Lake Store como almacenamiento principal.
    > - La actividad de Spark admite solo el clúster de HDInsight Spark existente (el suyo propio). No admite un servicio vinculado de HDInsight a petición.

    Vea [Servicio vinculado de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obtener más información sobre el servicio vinculado de HDInsight.
3.  Para implementar el servicio vinculado, haga clic en **Implementar** en la barra de comandos.  

### <a name="create-output-dataset"></a>Creación del conjunto de datos de salida
El conjunto de datos de salida es lo que impulsa la programación (cada hora, cada día, etc.). Por lo tanto, debe especificar el conjunto de datos de salida para la actividad de Spark en la canalización aunque la actividad no produzca realmente una salida. Es opcional especificar un conjunto de datos de entrada para la actividad.

1. En el **Editor de Data Factory**, haga clic en **... Más** en la barra de comandos y luego en **Nuevo conjunto de datos**; después, seleccione **Almacenamiento de blobs de Azure**.  
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1. El fragmento de código JSON define un conjunto de datos denominado **OutputDataset**. Además, especifica que los resultados se almacenan en el contenedor de blobs llamado **adfspark** y en la carpeta llamada **pyFiles/output**. Como se mencionó anteriormente, este conjunto de datos es un conjunto de datos ficticio. El programa Spark de este ejemplo no genera ninguna salida. La sección **availability** especifica que el conjunto de datos de salida se genera diariamente.  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. Para implementar el conjunto de datos, haga clic en **Implementar** en la barra de comandos.


### <a name="create-pipeline"></a>Creación de una canalización
En este paso, crea una canalización con una actividad de **HDInsightSpark**. Actualmente, el conjunto de datos de salida es lo que controla la programación, por lo que debe crear un conjunto de datos de salida aunque la actividad no genere ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. Por lo tanto, no se especifica ningún conjunto de datos de entrada en este ejemplo.

1. En **Data Factory Editor**, haga clic en **… Más** en la barra de comandos y después haga clic en **Nueva canalización**.
2. Reemplace el script en la ventana Draft-1 con el siguiente script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Tenga en cuenta los siguientes puntos:
    - La propiedad de **tipo** se establece en **HDInsightSpark**.
    - El valor de **rootPath** se establece en **adfspark\\pyFiles**, donde adfspark es el contenedor de Azure Blob y pyFiles es la carpeta adecuada en ese contenedor. En este ejemplo, la instancia de Azure Blob Storage es la que está asociada con el clúster de Spark. Puede cargar el archivo en un almacenamiento de Azure diferente. Si lo hace, cree un servicio vinculado de Azure Storage para vincular esa cuenta de almacenamiento con la factoría de datos. A continuación, especifique el nombre del servicio vinculado como un valor de la propiedad **sparkJobLinkedService**. Consulte las [propiedades de la actividad de Spark](#spark-activity-properties) para más detalles sobre esta y otras propiedades que admite la actividad de Spark.  
    - El valor de **entryFilePath** se establece en **test.py**, que es el archivo de Python.
    - La propiedad **getDebugInfo** está establecida en **Siempre**, lo que significa que siempre se generan archivos de registro (acierto o error).

        > [!IMPORTANT]
        > Se recomienda que no establezca esta propiedad como `Always` en un entorno de producción, a menos que esté solucionando un problema.
    - La sección de **salida** tiene un conjunto de datos de salida. Debe especificar un conjunto de datos de salida, incluso si el programa de Spark no genera ninguna salida. El conjunto de datos de salida impulsa la programación de la canalización (cada hora, cada día, etc.).  

        Para obtener información detallada sobre las propiedades que la actividad de Spark admite, vea [Propiedades de la actividad de Spark](#spark-activity-properties).
3. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.

### <a name="monitor-pipeline"></a>Supervisión de la canalización
1. Haga clic en el botón **X** para cerrar las hojas de Data Factory Editor y volver a página principal de Data Factory. Haga clic en **Supervisión y administración** para iniciar la aplicación de supervisión en otra pestaña.

    ![Icono Supervisión y administración](media/data-factory-spark/monitor-and-manage-tile.png)
2. Cambie el filtro **Hora de inicio** en la parte superior por **2/1/2017** y haga clic en **Aplicar**.
3. Debería ver solo una ventana de actividad, ya que solo hay un día entre las horas de inicio (2017-02-01) y fin (2017-02-02) de la canalización. Confirme que el estado del segmento de datos es **Listo**.

    ![Supervisar la canalización](media/data-factory-spark/monitor-and-manage-app.png)    
4. Seleccione la **ventana actividad** para ver los detalles sobre la ejecución de la actividad. Si se produce un error, puede ver detalles sobre él en el panel derecho.

### <a name="verify-the-results"></a>Verificación de los resultados

1. Inicie **Jupyter Notebook** para el clúster de HDInsight Spark; para ello, navegue a: https://CLUSTERNAME.azurehdinsight.net/jupyter. También puede iniciar el panel del clúster de HDInsight Spark y después inicie **Jupyter Notebook**.
2. Haga clic en **Nuevo** -> **PySpark** para iniciar un nuevo cuaderno.

    ![Nuevo Jupyter Notebook](media/data-factory-spark/jupyter-new-book.png)
3. Ejecute el siguiente comando; para ello, copie y peque el texto y presione **MAYÚS + ENTRAR** al final de la segunda instrucción.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Confirme que ve los datos de la tabla hvac:  

    ![Resultados de la consulta Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

Vea la sección [Ejecución de una consulta de Spark SQL](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md#run-a-hive-query-using-spark-sql) para obtener instrucciones detalladas. 

### <a name="troubleshooting"></a>Solución de problemas
Puesto que establece **getDebugInfo** en **Siempre**, aparece una subcarpeta de **registro** en la carpeta **pyFiles** del contenedor de Azure Blob. El archivo de registro en la carpeta de registro proporciona detalles adicionales. Este archivo de registro es especialmente útil cuando se produce un error. En un entorno de producción, puede establecerlo en **Error**.

Para solucionar problemas, realice los pasos siguientes:


1. Vaya a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicación de IU de YARN](media/data-factory-spark/yarnui-application.png)  
2. Haga clic en **Registros** para uno de los intentos de ejecución.

    ![Página de aplicación](media/data-factory-spark/yarn-applications.png)
3. Debería ver información adicional sobre el error en la página de registro.

    ![Error de registro](media/data-factory-spark/yarnui-application-error.png)

En las secciones siguientes se proporciona información sobre las entidades de Data Factory para usar un clúster de Apache Spark y una actividad de Spark en su factoría de datos.

## <a name="spark-activity-properties"></a>Propiedades de la actividad de Spark
Esta es la definición JSON de ejemplo de una canalización con la actividad de Spark:    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| name | Nombre de la actividad en la canalización. | Sí |
| Descripción | Texto que describe para qué se usa la actividad. | No |
| type | Esta propiedad debe establecerse en HDInsightSpark. | Sí |
| linkedServiceName | Nombre del servicio vinculado de HDInsight en el que se ejecuta el programa de Spark. | Sí |
| rootPath | Contenedor de blobs de Azure y la carpeta que contiene el archivo de Spark. El nombre del archivo distingue mayúsculas de minúsculas. | Sí |
| entryFilePath | Ruta de acceso relativa a la carpeta raíz del código o el paquete de Spark. | Sí |
| className | Clase principal de Spark o Java de la aplicación. | No |
| argumentos | Lista de argumentos de línea de comandos del programa de Spark. | No |
| proxyUser | Cuenta de usuario de suplantación para ejecutar el programa de Spark. | No |
| sparkConfig | Especifique valores para propiedades de configuración de Spark indicados en el tema [Spark Configuration: Application properties](https://spark.apache.org/docs/latest/configuration.html#available-properties) (Configuración de Spark: Propiedades de aplicación). | No |
| getDebugInfo | Especifica si se copian los archivos de registro de Spark en el almacenamiento de Azure que usa el clúster de HDInsight que especifica sparkJobLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | No |
| sparkJobLinkedService | El servicio vinculado de Azure Storage que contiene los registros, las dependencias y los archivos de trabajos de Spark.  Si no especifica un valor para esta propiedad, se usa el almacenamiento asociado con el clúster de HDInsight. | No |

## <a name="folder-structure"></a>Estructura de carpetas
La actividad de Spark no es compatible con un script en línea, al contrario que las actividades de Pig y Hive. Los trabajos de Spark también son más ampliable que los de Pig y Hive. En los trabajos de Spark, puede proporcionar varias dependencias como paquetes JAR (ubicados en la CLASSPATH de Java), archivos de Python (ubicados en la ruta PYTHONPATH) y cualquier otro archivo.

Cree la siguiente estructura de carpetas en la instancia de Azure Blob Storage a la que hace referencia el servicio vinculado de HDInsight. Luego, cargue los archivos dependientes en las subcarpetas adecuadas de la carpeta raíz que representa **entryFilePath**. Por ejemplo, cargue los archivos de Python en la subcarpeta pyFiles y los archivos JAR en la subcarpeta jars de la carpeta raíz. En el entorno de tiempo de ejecución, el servicio Data Factory espera la siguiente estructura de carpetas en Azure Blob Storage:     

| Ruta de acceso | Descripción | Obligatorio | Escriba |
| ---- | ----------- | -------- | ---- |
| . | Ruta de acceso raíz del trabajo de Spark en el servicio vinculado de almacenamiento.  | Sí | Carpeta |
| &lt;Definida por el usuario&gt; | Ruta de acceso que apunta al archivo de entrada del trabajo de Spark. | Sí | Archivo |
| ./jars | Todos los archivos de esta carpeta se cargan y se colocan en la ruta CLASSPATH de Java del clúster. | No | Carpeta |
| ./pyFiles | Todos los archivos de esta carpeta se cargan y se colocan en la ruta PYTHONPATH del clúster. | No | Carpeta |
| ./files | Todos los archivos de esta carpeta se cargan y se colocan en el directorio de trabajo del ejecutor. | No | Carpeta |
| ./archives | Todos los archivos de esta carpeta están sin comprimir. | No | Carpeta |
| ./logs | Carpeta donde se almacenan los registros del clúster de Spark.| No | Carpeta |

Este es un ejemplo de un almacenamiento que contiene dos archivos de trabajos de Spark en la instancia de Azure Blob Storage a la que hace referencia el servicio vinculado de HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```

