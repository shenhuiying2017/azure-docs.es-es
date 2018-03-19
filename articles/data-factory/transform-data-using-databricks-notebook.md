---
title: "Ejecución de un cuaderno de Databricks con la actividad Notebook de Databricks en Azure Data Factory"
description: "Aprenda cómo puede utilizar la actividad Notebook de Databricks en una instancia de Azure Data Factory para ejecutar un cuaderno de Databricks en el clúster de trabajos de Databricks."
services: data-factory
documentationcenter: 
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: d1dcec26529c747a209dd10fcefbbadaa40365a3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Ejecución de un cuaderno de Databricks con la actividad Notebook de Databricks en Azure Data Factory

En este tutorial, va a utilizar Azure Portal para crear una canalización de Azure Data Factory que ejecuta un cuaderno de Databricks en el clúster de trabajos de Databricks. También pasa parámetros de Azure Data Factory al cuaderno de Databricks durante la ejecución.

En este tutorial, realizará los siguientes pasos:

  - Creación de una factoría de datos.

  - Creación de una canalización que utiliza la actividad Notebook de Databricks

  - Desencadenamiento de una ejecución de la canalización

  - Supervisión de la ejecución de la canalización

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

  - **Área de trabajo de Azure Databricks**. [Cree un área de trabajo de Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) o use una existente. Va a crear un cuaderno de Python en el área de trabajo de Azure Databricks. Después, va a ejecutar el cuaderno y le pasará parámetros mediante Azure Data Factory.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1.  Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.

2.  Seleccione **Nuevo** en el menú de la izquierda, seleccione **Datos y análisis** y, después, seleccione **Data Factory**.

    ![Creación de una factoría de datos](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  En el panel **Nueva factoría de datos**, escriba **ADFTutorialDataFactory** en **Nombre**.

    El nombre de Azure Data Factory debe ser *único de forma global*. Si ve el siguiente error, cambie el nombre de la factoría de datos. (Por ejemplo, utilice **\<SuNombre\>ADFTutorialDataFactory**). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte el artículo [Azure Data Factory: reglas de nomenclatura](https://docs.microsoft.com/en-us/azure/data-factory/naming-rules).

    ![Proporcionar un nombre para la nueva factoría de datos](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  En **Suscripción**, seleccione la suscripción de Azure donde desea crear la factoría de datos.

5.  Para **Grupo de recursos**, realice uno de los siguientes pasos:
    
    - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable.
    
    - Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos.

    En algunos de los pasos de esta guía de inicio rápido se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1.  En **Versión**, seleccione **V2 (versión preliminar)**.

2.  En **Ubicación**, seleccione la ubicación de la factoría de datos.

    Actualmente, Data Factory V2 le permite crear factorías de datos solo en la región Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (como Azure Storage y Azure SQL Database) y los procesos (como HDInsight) que usan Data Factory pueden encontrarse en otras regiones.

3.  Seleccione **Anclar al panel**.

4.  Seleccione **Crear**.

5.  En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory):

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  Una vez completada la creación, verá la página **Data Factory**. Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente.

    ![Inicio de la aplicación de interfaz de usuario de Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Crear servicios vinculados

En esta sección, va a crear un servicio vinculado de Databricks. Este servicio vinculado contiene la información de conexión al clúster de Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Creación de un servicio vinculado de Azure Databricks

1.  En la página de **introducción**, cambie a la pestaña **Edit** (Editar) del panel izquierdo.

    ![Modificar el nuevo servicio vinculado](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  Seleccione **Connections** (Conexiones) en la parte inferior de la ventana y seleccione **+ New** (+ Nuevo).
    
    ![Crear una conexión](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Data Store** (Almacén de datos) \> **Azure Blob Storage** y después **Continue** (Continuar).
    
    ![Especificar un servicio vinculado de Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:
    
    1.  En **Name** (Nombre) escriba ***AzureDatabricks\_LinkedService***.
    
    2.  En **Cluster** (Clúster), seleccione un **nuevo clúster**.
    
    3.  En **Domain/Region** (Dominio/región), seleccione la región donde se encuentra el área de trabajo de Azure Databricks.
    
    4.  En **Cluster node type** (Tipo de nodo de clúster), seleccione **Standard\_D3\_v2** para este tutorial.
    
    5.  En **Access Token** (Token de acceso), genérelo en el área de trabajo de Azure Databricks. Puede encontrar los pasos [aquí](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  En **Cluster version** (Versión de clúster), seleccione **4.0 Beta** (última versión).
    
    7.  En **Number of worker nodes** (Número de nodos de trabajo), escriba **2**.
    
    8.  Seleccione **Finish** (Finalizar).

        ![Finalizar la creación del servicio vinculado](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Crear una canalización

1.  Seleccione el botón **+** (Más) y seleccione **Pipeline** (Canalización) en el menú.

    ![Botones para crear una canalización](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Cree un **parámetro** para su uso en la **canalización**. Más adelante va a pasar este parámetro a la actividad Notebook de Databricks. En la canalización vacía, haga clic en la pestaña **Parameters** (Parámetros), después en **New** (Nuevo) y asígnele el nombre de '**name**'.

    ![Crear un nuevo parámetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Crear el parámetro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  En el cuadro de herramientas **Activities** (Actividades), expanda **Databricks**. Arrastre la actividad **Notebook** del cuadro de herramientas **Activities** (Actividades) a la superficie del diseñador de canalizaciones.

    ![Arrastrar Notebook a la superficie del diseñador](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  En las propiedades de la ventana de actividad **Notebook** de **Databricks** de la parte inferior, realice los pasos siguientes:

    a. Cambie a la pestaña **Configuración** .

    b. Seleccione el servicio **myAzureDatabricks\_LinkedService** que creó en el procedimiento anterior.

    c. Seleccione una **ruta de acceso del cuaderno** de Databricks. Vamos a crear un cuaderno y especifique aquí la ruta de acceso. Para obtener la ruta de acceso del cuaderno, siga los pasos siguientes.

       1. Inicie el área de trabajo de Azure Databricks.

       2. Cree una **carpeta** en el área de trabajo y llámela **adftutorial**.

          ![Crear una carpeta](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Cree un nuevo cuaderno](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), al que va a llamar **mynotebook** en la carpeta **adftutorial** **,** haga clic en **Crear**.

          ![Crear un nuevo cuaderno](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Establecer las propiedades del nuevo cuaderno](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. En el cuaderno "mynotebook" recién creado, agregue el código siguiente:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Crear widgets para los parámetros](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. La **ruta de acceso del cuaderno** en este caso es **/adftutorial/mynotebook**.

5.  Vuelva a la **herramienta de creación de interfaz de usuario de Data Factory**. Vaya a la pestaña **Configuración** en la **actividad Notebook1**. 
    
    a.  **Agregue un parámetro** a la actividad Notebook. Utilice el mismo parámetro que se ha agregado antes a la **canalización**.

       ![Agregar un parámetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  Llame al parámetro **input** y proporcione el valor como expresión **@pipeline().parameters.name**.

6.  Para comprobar la canalización, seleccione el botón **Validate** (Comprobar) en la barra de herramientas. Para cerrar la ventana de validación, seleccione el botón **\>\>** (flecha derecha).

    ![Comprobación de la canalización](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Seleccione **Publish All** (Publicar todo). La interfaz de usuario de Data Factory permite publicar entidades (servicios vinculados y canalizaciones) en el servicio Azure Data Factory.

    ![Publicar las nuevas entidades de Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización

Seleccione **Trigger** (Desencadenar) en la barra de herramientas y, después, seleccione **Trigger Now** (Desencadenar ahora).

![Seleccionar el comando Trigger Now (Desencadenar ahora)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

El cuadro de diálogo **Pipeline Run** (Ejecución de canalización) solicita el parámetro **name**. Utilice **/path/filename** como parámetro aquí. Haga clic en **Finish** (Finalizar).

![Proporcionar un valor para los parámetros de nombre](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1.  Vaya a la pestaña **Monitor** (Supervisar). Confirme que ve una ejecución de canalización. Se tarda aproximadamente entre 5 y 8 minutos crear un clúster de trabajo de Databricks, donde se ejecuta el cuaderno.

    ![Supervisar la canalización](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Seleccione **Actualizar** periódicamente para comprobar el estado de la ejecución de canalización.

3.  Para ver las ejecuciones de actividad asociadas con la ejecución de esta canalización, seleccione **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones).

    ![Ver las ejecuciones de actividad](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Puede volver a la vista de ejecuciones de canalización. Para ello seleccione el vínculo **Pipelines** (Canalizaciones) en la parte superior.

## <a name="verify-the-output"></a>Comprobación del resultado

Puede iniciar sesión en el **área de trabajo de Azure Databricks**, ir a **Clusters** (Clústeres) y ver el estado del **trabajo** como *ejecución pendiente, en ejecución o finalizado*.

![Ver el clúster de trabajo y el trabajo](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Puede hacer clic en el **nombre del trabajo** y desplazarse para ver más detalles. Si la ejecución se realiza correctamente, puede validar los parámetros pasados y la salida del cuaderno de Python.

![Ver los detalles de ejecución y la salida](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Pasos siguientes

La canalización de este ejemplo desencadena una actividad Notebook de Databricks y le pasa un parámetro. Ha aprendido a:

  - Creación de una factoría de datos.

  - Creación de una canalización que utiliza la actividad Notebook de Databricks.

  - Desencadenamiento de una ejecución de la canalización

  - Supervisión de la ejecución de la canalización
