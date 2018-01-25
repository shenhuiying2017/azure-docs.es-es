---
title: Carga de terabytes de datos en SQL Data Warehouse | Microsoft Docs
description: "Muestra cómo se puede cargar 1 TB de datos en Azure SQL Data Warehouse en 15 minutos con Azure Data Factory"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3350645d4f173a6d0d007ff9095bb3115600a13b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Carga de 1 TB en Azure SQL Data Warehouse en 15 minutos con Data Factory
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea el artículo sobre la [copia de datos a Azure SQL Data Warehouse o desde Azure SQL Data Warehouse mediante Data Factory, versión 2](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) es una base de datos de escalado horizontal y basada en la nube que es capaz de procesar volúmenes masivos de datos (tanto relacionales como no relacionales).  Basado en nuestra arquitectura de procesamiento paralelo masivo (MPP), SQL Data Warehouse está mejorado para controlar las cargas de trabajo empresariales.  Ofrece elasticidad en la nube con la flexibilidad para escalar almacenamiento y proceso de forma independiente.

La introducción a Azure SQL Data Warehouse es ahora más fácil que nunca usando **Azure Data Factory**.  Azure Data Factory es un servicio de integración de datos basado en la nube completamente administrado que se puede utilizar para rellenar una instancia de SQL Data Warehouse con los datos del sistema existente, lo que ahorra tiempo durante la evaluación de SQL Data Warehouse y la creación de soluciones de análisis. Estos son los beneficios claves de cargar datos en Azure SQL Data Warehouse usando Azure Data Factory:

* **Fácil de configurar**: con un asistente intuitivo en 5 pasos sin necesidad de scripting.
* **Amplia compatibilidad para el almacenamiento de datos**: compatibilidad integrada para un amplio conjunto de almacenes de datos tanto locales como basados en la nube.
* **Seguro y conforme con la normativa**: los datos se transfieren a través de HTTPS o ExpressRoute y la presencia del servicio global garantiza que los datos nunca abandonan el límite geográfico
* **Rendimiento sin precedentes mediante PolyBase**: el uso de Polybase es la forma más eficiente de mover datos a Azure SQL Data Warehouse. Mediante la característica de blob de almacenamiento provisional, puede alcanzar velocidades de carga altas para todos los tipos de almacenes de datos además de Azure Blob Storage, que es compatible con Polybase de forma predeterminada.

En este artículo se muestra cómo utilizar el Asistente para copia de Data Factory para cargar 1 TB de datos de Azure Blob Storage a Azure SQL Data Warehouse en menos de 15 minutos, con un rendimiento superior a 1,2 GB por segundo.

Este artículo proporciona instrucciones paso a paso para mover datos a Azure SQL Data Warehouse mediante el Asistente para copia.

> [!NOTE]
>  Para obtener información general acerca de las funcionalidades de Data Factory para el movimiento de datos hacia y desde Azure SQL Data Warehouse, consulte el artículo [Movimiento de datos hacia y desde SQL Data Warehouse mediante Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md).
>
> También puede crear canalizaciones utilizando Azure Portal, Visual Studio, PowerShell, etc. Consulte [Tutorial: Copia de datos de Azure Blob Storage en Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para ver un tutorial rápido con instrucciones detalladas para usar la actividad de copia en Azure Data Factory.  
>
>

## <a name="prerequisites"></a>requisitos previos
* Azure Blob Storage: este experimento usa Azure Blob Storage (GRS) para almacenar un conjunto de datos de prueba TPC-H.  Si no dispone de una cuenta de Azure Storage, infórmese sobre [cómo crear una cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Datos [TPC-H](http://www.tpc.org/tpch/): vamos a usar TPC-H como conjunto de datos de prueba.  Para ello, tiene que utilizar `dbgen` desde el Kit de herramientas de TPC-H, lo que le ayudará a generar el conjunto de datos.  Puede descargar código fuente para `dbgen` en las [herramientas de TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) y compilarlo usted mismo, o descargar el binario compilado desde [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Ejecute dbgen.exe con los siguientes comandos para generar un archivo sin formato de 1 TB para la tabla `lineitem` propagada a través de 10 archivos:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Ahora, copie los archivos generados Azure Blob.  Consulte [Movimiento de datos hacia el sistema de archivos local y desde él con Azure Data Factory](data-factory-onprem-file-system-connector.md) para saber cómo hacer esto usando copia de ADF.    
* Azure SQL Data Warehouse: este experimento carga datos en una instancia de Azure SQL Data Warehouse creada con 6.000 DWU

    Consulte [Creación de una instancia de Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para obtener instrucciones detalladas sobre cómo crear una base de datos de SQL Data Warehouse.  Para obtener el mejor rendimiento posible de la carga en SQL Data Warehouse mediante Polybase, elegimos el número máximo de unidades de almacenamiento de datos (DWU) permitidas en la configuración de rendimiento, que es 6.000 DWU.

  > [!NOTE]
  > Al cargar desde Azure Blob, el rendimiento de carga de los datos es directamente proporcionales al número de DWU que se configura en SQL Data Warehouse:
  >
  > Cargar 1 TB en SQL Data Warehouse de 1000 DWU tarda 87 minutos (con un rendimiento aproximado de 200 MB por segundo) Cargar 1 TB en SQL Data Warehouse de 2000 DWU tarda 46 minutos (con un rendimiento aproximado de 380 MB por segundo) Cargar 1 TB en SQL Data Warehouse de 6000 DWU tarda 14 minutos (con un rendimiento aproximado de 1,2 GB por segundo)
  >
  >

    Para crear una instancia de SQL Data Warehouse con 6.000 DWU, mueva el control deslizante de rendimiento hasta el tope a la derecha:

    ![Control deslizante de rendimiento](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para una base de datos existente que no esté configurada con 6.000 DWU, puede escalarla verticalmente utilizando Azure Portal.  Vaya a la base de datos en Azure Portal donde encontrará un botón **Escala** en el panel de **información general** que se muestra en la siguiente imagen:

    ![Botón de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Haga clic en el botón **Escala** para abrir el siguiente panel, mueva el control deslizante al valor máximo y haga clic en el botón **Guardar**.

    ![Cuadro de diálogo Escala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Este experimento carga datos en Azure SQL Data Warehouse usando la clase de recursos `xlargerc`.

    Para obtener el mejor rendimiento posible, la copia tiene que realizarse utilizando el usuario de SQL Data Warehouse que pertenece a la clase de recursos `xlargerc`.  Infórmese acerca de cómo hacerlo siguiendo las instrucciones en [Cambio de ejemplo de clase de recursos de usuario](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Cree el esquema de la tabla de destino en Azure SQL Data Warehouse, ejecutando la siguiente instrucción DDL:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
Con los pasos previos completados, ahora estamos preparados configurar la actividad de copia mediante el Asistente para copia.

## <a name="launch-copy-wizard"></a>Inicio del Asistente para copia
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **+ NUEVO** en la esquina superior izquierda, después en **Inteligencia y análisis** y en **Data Factory**.
3. En la hoja **Nueva factoría de datos** :

   1. Escriba **LoadIntoSQLDWDataFactory** para el **nombre**.
       El nombre de la factoría de datos de Azure debe ser único global. Si recibe el error: **El nombre de factoría de datos "LoadIntoSQLDWDataFactory" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, sunombreLoadIntoSQLDWDataFactory) e intente crearla de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.  
   2. Selección la **suscripción**de Azure.
   3. Para el grupo de recursos, realice uno de los siguientes pasos:
      1. Seleccione en primer lugar **Usar existente** y después un grupo de recursos existente.
      2. Seleccione **Crear nuevo** y escriba un nombre para un grupo de recursos.
   4. Seleccione una **ubicación** para la factoría de datos.
   5. Seleccione la casilla **Anclar al panel** en la parte inferior de la hoja.  
   6. Haga clic en **Create**(Crear).
4. Una vez completada la creación, puede ver la hoja **Data Factory** como se muestra en la siguiente imagen:

   ![Página principal Factoría de datos](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. En la página principal de Data Factory, haga clic en el icono **Copiar datos** para iniciar el **Asistente para copia**.

   > [!NOTE]
   > Si ve que el explorador web está atascado en "Autorizando...", deshabilite o desactive la opción **Bloquear cookies y datos de sitios de terceros** o déjela habilitada y cree una excepción para **login.microsoftonline.com** e intente iniciar de nuevo el asistente.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Paso 1: Configuración de la programación de carga de datos
El primer paso es configurar la programación de carga de datos.  

En la página **Propiedades** :

1. Escriba **CopyFromBlobToAzureSqlDataWarehouse** en **Nombre de tarea**
2. Seleccione la opción **Ejecutar una vez ahora**.   
3. Haga clic en **Next**.  

    ![Asistente para copia: Página Propiedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Paso 2: Configuración del origen
Esta sección muestra los pasos necesarios para configurar el origen: Esta sección muestra los pasos necesarios para configurar el origen: instancia de Azure Blob que contiene los archivos del elemento de línea 1-TB TPC-H.

1. Seleccione **Azure Blob Storage** como almacén de datos y haga clic en **Siguiente**.

    ![Asistente para copia: Selección de página de origen](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Rellene la información de conexión para la cuenta de Azure Blob Storage y haga clic en **Siguiente**.

    ![Asistente para copia: Información de conexión de origen](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Elija la **carpeta** que contiene los archivos de elemento de línea TPC-H y haga clic en **Siguiente**.

    ![Asistente para copia: Selección de carpeta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Al hacer clic en **Siguiente**, se detectan automáticamente los ajustes de formato de archivo.  Asegúrese de que el delimitador de columnas es " | " en lugar del ajuste predeterminado de la coma ",".  Haga clic en **Siguiente** después de realizar una vista previa de los datos.

    ![Herramienta de copia: Ajustes de formato de archivo](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Paso 3: Configuración del destino
En esta sección muestra cómo configurar el destino: tabla `lineitem` en la base de datos de Azure SQL Data Warehouse.

1. Elija **Azure SQL Data Warehouse** como el destino de almacenamiento y haga clic en **Siguiente**.

    ![Asistente para copia: Selección del almacén de datos de destino](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Rellene la información de conexión para Azure SQL Data Warehouse.  Asegúrese de especificar el usuario que sea miembro del rol `xlargerc` (consulte la sección **Requisitos previos** para obtener instrucciones detalladas) y haga clic en **Siguiente**.

    ![Asistente para copia: Información de conexión de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Elija la tabla de destino y haga clic en **Siguiente**.

    ![Asistente para copia: Página de asignación de tabla](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. En la página de asignación de esquemas, deje desactivada la opción "Apply column mapping" (Aplicar asignación de columna) y haga clic en **Siguiente**.

## <a name="step-4-performance-settings"></a>Paso 4: Configuración de rendimiento

La opción **Allow polybase** (Permitir Polybase) está activada de forma predeterminada.  Haga clic en **Next**.

![Asistente para copia: Página de asignación de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Paso 5: Implementación y supervisión de los resultados de carga
1. Haga clic en el botón **Finalizar** para implementar.

    ![Asistente para copiar: Página de resumen](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Una vez completada la implementación, haga clic en `Click here to monitor copy pipeline` para supervisar el progreso de la ejecución de la copia. Seleccione la canalización de copia que creó en la lista **Activity Windows** (Ventanas de actividad).

    ![Asistente para copiar: Página de resumen](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Puede ver los detalles de la ejecución de la copia en **Activity Window Explorer** (Explorador de la ventana de actividad) en el panel derecho, esta información incluye el volumen de datos leídos en el origen y escritos en el destino, y el rendimiento medio de la ejecución.

    Como puede ver en la captura de pantalla siguiente, se tardaron 14 minutos en copiar 1 TB de Azure Blob Storage en SQL Data Warehouse, lo que quiere decir que se alcanzó el rendimiento de 1,22 GB por segundo.

    ![Asistente para copia: Cuadro de diálogo de éxito en la operación](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Procedimientos recomendados
Estas son algunos de los procedimientos recomendados para la ejecución de la base de datos de Azure SQL Data Warehouse:

* Use una clase de recurso más grande al cargar en un ÍNDICE AGRUPADO DE ALMACÉN DE COLUMNAS.
* Para las combinaciones más eficaces, considere el uso de distribución hash con una columna seleccionada, en lugar de la distribución round robin predeterminada.
* Para conseguir una mayor velocidad de carga, considere la posibilidad de utilizar un montón para los datos transitorios.
* Cree estadísticas después de finalizar la carga de Azure SQL Data Warehouse.

Para información más detallada consulte [Procedimientos recomendados para Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

## <a name="next-steps"></a>pasos siguientes
* [Asistente para copia de Data Factory](data-factory-copy-wizard.md): este artículo proporciona detalles sobre el Asistente para copia.
* [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md): este artículo contiene la guía de optimización y medidas de rendimiento de la actividad de copia.
