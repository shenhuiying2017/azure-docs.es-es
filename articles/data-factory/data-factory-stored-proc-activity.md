---
title: Actividad de procedimiento almacenado de SQL Server
description: "Sepa cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una Base de datos SQL de Azure o en un Almacenamiento de datos SQL de Azure desde una canalización de Factoría de datos."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 1b2514e1e6f39bb3ce9d8a46f4af01835284cdcc
ms.openlocfilehash: 3510b0446cf3c1a7ffb3ff02a4d84d24ead1cae7


---
# <a name="sql-server-stored-procedure-activity"></a>Actividad de procedimiento almacenado de SQL Server
> [!div class="op_single_selector"]
> [Hive](data-factory-hive-activity.md)  
> [Pig](data-factory-pig-activity.md)  
> [MapReduce](data-factory-map-reduce.md)  
> [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> [Procedimiento almacenado](data-factory-stored-proc-activity.md)
> [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> [.NET personalizado](data-factory-use-custom-activities.md)
>
>

Puede usar la actividad de procedimiento almacenado de SQL Server en una [canalización](data-factory-create-pipelines.md) de Data Factory para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos:

* Base de datos SQL de Azure
* Almacenamiento de datos SQL de Azure  
* Base de datos de SQL Server en la empresa o en una máquina virtual de Azure. Data Management Gateway se debe instalar en la misma máquina que hospeda la base de datos o en una máquina independiente, con el fin de evitar la competencia por los recursos con la base de datos. Data Management Gateway es un software que conecta orígenes de datos locales u orígenes de datos hospedados en máquinas virtuales de Azure con servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.

Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

## <a name="walkthrough"></a>Tutorial
### <a name="sample-table-and-stored-procedure"></a>Procedimiento almacenado y tabla de ejemplo
1. Cree la siguiente **tabla** en la Base de datos SQL de Azure con SQL Server Management Studio o cualquier otra herramienta que le resulte cómoda. La columna datetimestamp indica la fecha y la hora en que se generó el identificador correspondiente.

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    Id es el identificador único y la columna datetimestamp indica la fecha y la hora en que se generó el identificador correspondiente.
    ![Datos de ejemplo](./media/data-factory-stored-proc-activity/sample-data.png)

   > [!NOTE]
   > En este ejemplo, se usa Base de datos SQL de Azure, pero funciona de la misma manera con Almacenamiento de datos SQL de Azure y Base de datos de SQL Server.
   >
   >
2. Cree el siguiente **procedimiento almacenado**, que inserta datos en **sampletable**.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS

        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

   > [!IMPORTANT]
   > El **nombre** y el **uso de mayúsculas y minúsculas** en el parámetro (DateTime en este ejemplo) tienen que coincidir con los del parámetro especificado en el código JSON de la canalización o actividad. En la definición del procedimiento almacenado, asegúrese de que se usa **@** como prefijo del parámetro.
   >
   >

### <a name="create-a-data-factory"></a>Crear una factoría de datos
1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **NUEVO**, en **Inteligencia y análisis** y en **Data Factory**.

    ![Nueva factoría de datos](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. En la hoja **Nueva factoría de datos**, escriba **SProcDF** para el nombre. Los nombres de Azure Data Factory son **únicos globalmente**. Es necesario agregar su nombre como prefijo al nombre de la factoría de datos para permitir la creación correcta de la factoría.

   ![Nueva factoría de datos](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Seleccione la **suscripción de Azure**.
5. Para el **grupo de recursos**, realice uno de los siguientes pasos:
   1. Haga clic en **Crear nuevo** y escriba un nombre para el grupo de recursos.
   2. Haga clic en **Usar existente** y seleccione un grupo de recursos existente.  
6. Seleccione la **ubicación** de Data Factory.
7. Seleccione **Anclar al panel** de manera que pueda ver la factoría de datos en el panel la próxima vez que inicie sesión.
8. Haga clic en **Crear** en la hoja **Nueva fábrica de datos**.
9. Verá la factoría de datos que se crea en el **panel** de Azure Portal. Tras crear correctamente la factoría de datos, se ve la página de la factoría de datos, que muestra el contenido de la misma.
   ![Página principal de Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Crear un servicio vinculado SQL de Azure.
Después de crear la factoría de datos, cree un servicio vinculado SQL de Azure que vincule la Base de datos SQL de Azure a la factoría de datos. Esta base de datos contiene la tabla sampletable y el procedimiento almacenado sp_sample.

1. En la hoja **Crear e implementar**, haga clic en la hoja **Data Factory** para que **SProcDF** inicie Data Factory Editor.
2. Haga clic en **Nuevo almacén de datos** en la barra de comandos y elija **Azure SQL Database**. Debería ver el script JSON para crear un servicio vinculado SQL de Azure en el editor.

   ![Nuevo almacén de datos](media/data-factory-stored-proc-activity/new-data-store.png)
3. Realice los siguientes cambios en el script JSON:

   1. Reemplace **&lt;servername&gt;** por el nombre del servidor de Azure SQL Database.
   2. Reemplace **&lt;databasename&gt;** por la base de datos en la que ha creado la tabla y el procedimiento almacenado.
   3. Reemplace **&lt;username@servername&gt;** por la cuenta de usuario que tiene acceso a la base de datos.
   4. Reemplace **&lt;password&gt;** por la contraseña de la cuenta de usuario.

      ![Nuevo almacén de datos](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado. Confirme que AzureSqlLinkedService aparece en la vista de árbol a la izquierda.

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida
1. Haga clic en **... Más** en la barra de herramientas, haga clic en **Nuevo conjunto de datos** y en **Azure SQL**. Haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Azure SQL**.

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie y pegue el siguiente script JSON en el editor de JSON.

        {                
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos. Confirme que el conjunto de datos aparece en la vista de árbol.

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Crear una canalización con una actividad SqlServerStoredProcedure
Ahora, vamos a crear una canalización con una actividad SqlServerStoredProcedure.

1. Haga clic en **... Más** en la barra de comandos y en **Nueva canalización**.
2. Copie y pegue el siguiente fragmento de código JSON. El valor de **storedProcedureName** se establece en **sp_sample**. El nombre y el uso de mayúsculas y minúsculas en el parámetro **DateTime** deben coincidir con los del parámetro de la definición del procedimiento almacenado.  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                 "start": "2016-08-02T00:00:00Z",
                 "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    Si necesita pasar null para un parámetro, use la sintaxis: "param1": null (todo en minúsculas).
3. Haga clic en **Implementar** en la barra de herramientas para implementar la canalización.  

### <a name="monitor-the-pipeline"></a>Supervisar la canalización
1. Haga clic en el botón **X** para cerrar las hojas del Editor de Data Factory y volver a la hoja de Data Factory. A continuación, haga clic en **Diagrama**.

    ![icono Diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. En la **Vista de diagrama**, se ve información general de las canalizaciones y los conjuntos de datos empleados en este tutorial.

    ![icono Diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. En la Vista de diagrama, haga doble clic en el conjunto de datos **sprocsampleout**. Verá los segmentos con estado Listo. Debería haber cinco segmentos, porque se genera un segmento para cada hora entre la hora de inicio y de finalización del código JSON.

    ![icono Diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Cuando un segmento tiene el estado **Listo**, ejecute una consulta **select * from sampletable** en Azure SQL Database para comprobar que el procedimiento almacenado ha insertado los datos en la tabla.

   ![Datos de salida](./media/data-factory-stored-proc-activity/output.png)

   Vea [Supervisar la canalización](data-factory-monitor-manage-pipelines.md) para obtener información detallada sobre cómo supervisar las canalizaciones de Factoría de datos de Azure.  

> [!NOTE]
> En este ejemplo, SprocActivitySample no tiene entradas. Si quiere encadenar esta actividad a una actividad de nivel superior (es decir, que se procesa antes), las salidas de la actividad de nivel superior pueden usarse como entradas en esta actividad. En este caso, esta actividad no se ejecuta hasta que se completa la actividad de nivel superior y las salidas de dichas actividades están disponibles (en estado Listo). Las entradas no se pueden usar directamente como un parámetro para la actividad de procedimiento almacenado.
>
>

## <a name="json-format"></a>Formato JSON
    {
        "name": "SQLSPROCActivity",
        "description": "description",
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## <a name="json-properties"></a>Propiedades JSON
| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| name |Nombre de la actividad |Sí |
| Descripción |Texto que describe para qué se usa la actividad. |No |
| type |SqlServerStoredProcedure |Sí |
| inputs |Opcional. Si especifica un conjunto de datos de entrada, debe estar disponible (en estado "Listo") para que se ejecute la actividad de procedimiento almacenado. El conjunto de datos de entrada no se puede usar en el procedimiento almacenado como parámetro. Solo se utiliza para comprobar la dependencia antes de iniciar la actividad de procedimiento almacenado. |No |
| outputs |Debe especificar un conjunto de datos para una actividad de procedimiento almacenado. El conjunto de datos de salida especifica la **programación** para la actividad de procedimiento almacenada (por hora, semanal, mensual, etc.). <br/><br/>El conjunto de datos de salida debe utilizar un **servicio vinculado** que haga referencia a una Base de datos SQL de Azure, un Almacenamiento de datos SQL o una base de datos de SQL Server donde desee que el procedimiento almacenado se ejecute. <br/><br/>El conjunto de datos de salida puede usarse como una forma de pasar el resultado del procedimiento almacenado para su posterior procesamiento por otra actividad ([encadenamiento de actividades](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence)) en la canalización. Sin embargo, Data Factory no escribe automáticamente la salida de un procedimiento almacenado en este conjunto de datos. Es el procedimiento almacenado el que escribe en una tabla SQL a la que apunta el conjunto de datos de salida. <br/><br/>En algunos casos, el conjunto de datos de salida puede ser un **conjunto de datos ficticio**, que solo se utilice para especificar la programación para ejecutar la actividad de procedimiento almacenado. |Sí |
| storedProcedureName |Especifique el nombre del procedimiento almacenado en la Base de datos SQL de Azure o en el Almacenamiento de datos SQL de Azure que se representa mediante el servicio vinculado que usa la tabla de salida. |Sí |
| storedProcedureParameters |Especifique valores para los parámetros del procedimiento almacenado. Si necesita pasar null para un parámetro, use la sintaxis: "param1": null (todo en minúsculas). Vea el ejemplo siguiente para aprender el uso de esta propiedad. |No |

## <a name="passing-a-static-value"></a>Pasar un valor estático
Ahora, pensemos en agregar otra columna denominada 'Escenario' en la tabla que contiene un valor estático denominado 'Ejemplo de documento'.

![Datos de ejemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)

    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

Ahora, pase el parámetro Escenario y el valor de la actividad de procedimiento almacenado. La sección typeProperties del ejemplo anterior es similar al siguiente fragmento de código:

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters":
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }



<!--HONumber=Nov16_HO3-->


