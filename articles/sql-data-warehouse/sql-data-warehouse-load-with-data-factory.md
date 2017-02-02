---

title: 'Carga de datos en Azure SQL Data Warehouse: Data Factory | Microsoft Docs'
description: En este tutorial se cargan datos en Azure SQL Data Warehouse mediante Data Factory de Azure y se utiliza una base de datos de SQL Server como origen de datos.
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5c8b3ef69cd2bc663d6ee744c2351da9c22adb94
ms.openlocfilehash: 24b9e5c2f423f7ef8bb29ab18359318f93c1d88c


---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Carga de datos en SQL Data Warehouse con Data Factory

Puede usar Azure Data Factory para cargar datos en Azure SQL Data Warehouse desde cualquiera de los [almacenes de datos de origen compatibles](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats). Por ejemplo, puede cargar datos desde una base de datos SQL de Azure o una base de datos de Oracle en una instancia de SQL Data Warehouse mediante Data Factory. El tutorial de este artículo muestra cómo cargar datos desde una base de datos de SQL Server local en una instancia de SQL Data Warehouse.

**Tiempo estimado**: una vez que haya cumplido los requisitos previos, tardará en completar este tutorial aproximadamente entre 10 y 15 minutos.

## <a name="prerequisites"></a>Requisitos previos

- Una base de datos de SQL con tablas que contienen los datos que se copiarán en la instancia de SQL Data Warehouse.  

- Debe tener una cuenta de Azure Storage. Puede [abrir una cuenta gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

- Necesita una instancia en línea de SQL Data Warehouse. Si no dispone de un almacén de datos, aprenda a [crear una instancia de Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md). El rendimiento mejora si la cuenta de almacenamiento y el almacenamiento de datos se encuentran en la misma región de Azure.

- Prepare el almacenamiento de datos para recibir los datos entrantes creando uno o varios esquemas de tabla. Puede usar la [utilidad de migración de SQL Data Warehouse](sql-data-warehouse-migrate-migration-utility.md) para crear un script de los esquemas. 

## <a name="configure-a-new-data-factory"></a>Configuración de una nueva factoría de datos
1. Inicie sesión en el [Portal de Azure][].
2. Localice el almacenamiento de datos y haga clic en él para abrirlo. 
3. En la hoja **Propiedades**, haga clic en **Cargar datos > Data Factory de Azure**.

    ![Inicio del Asistente para cargar datos](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)
4. Si no tiene una factoría de datos en la suscripción de Azure, verá un cuadro de diálogo **Nueva factoría de datos** en una pestaña independiente del explorador. Rellene la información solicitada y haga clic en **Crear**. Una vez que se crea la factoría de datos, el cuadro de diálogo **Nueva factoría de datos** se cierra y aparece el cuadro de diálogo **Seleccionar factoría de datos**.

    Si ya tiene una o varias factorías de datos en la suscripción de Azure, verá el cuadro de diálogo **Seleccionar factoría de datos**. En este cuadro de diálogo, puede elegir una factoría de datos existente o hacer clic en **Crear la nueva factoría de datos** para crear una nueva. 
5. En el cuadro de diálogo **Select Data Factory** (Seleccione la factoría de datos), la opción **Cargar datos** está activada de forma predeterminada. Haga clic en **Siguiente** para empezar a crear una tarea de carga de datos. 

## <a name="configure-the-data-factory-properties"></a>Configuración de las propiedades de la factoría de datos
Ahora que ha creado una factoría de datos, el siguiente paso consiste en configurar la programación de carga de datos. 

1. Seleccione **Propiedades** y rellene la información solicitada.
2. En **Nombre de la tarea**, escriba **DWLoadData-fromSQLServer**.
3. Haga clic en **Siguiente**.

    ![Configuración de la programación de carga](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-data-factory-source-and-gateway"></a>Configuración de la puerta de enlace y el origen de la factoría de datos
Ahora, indicaremos a Data Factory la base de datos de SQL Server local desde la que se van a cargar los datos.

1. Haga clic en **Origen**.
2. Elija **SQL Server** en el catálogo de almacenes de datos compatibles y haga clic en **Siguiente**.

    ![Selección del origen de SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

3. Se mostrará el cuadro de diálogo **Specify the on-premises SQL Server database** (Especifique la base de datos de SQL Server local). Rellene todos los campos obligatorios:

    - **Nombre de conexión**: especifique un nombre nuevo para la conexión.
    - **Nombre del servidor**: el nombre del servidor SQL local.
    - **Nombre de la base de datos**: la base de datos de SQL Server.
    - **Cifrado de credenciales**: seleccione Ninguno. 
    - **Tipo de autenticación**: elija el tipo de autenticación que usa.
    - **Nombre de usuario** y **contraseña**: escriba el nombre de usuario y la contraseña de un usuario que tenga permisos para copiar los datos.

4. El último campo pide el nombre de la puerta de enlace. Si el almacén de datos de origen es local o se encuentra en una máquina virtual de Azure IaaS, la puerta de enlace es necesaria. Si usa una factoría de datos existente que ya tiene una puerta de enlace, puede reutilizar esta puerta de enlace. Para ello, selecciónela en la lista desplegable. Haga clic en el vínculo **Crear puerta de enlace** para crear una puerta de enlace de administración de datos.  

    > [!NOTE]
    > Una puerta de enlace tiene una relación de 1 a 1 con una factoría de datos. No se puede usar desde otra factoría de datos, pero sí se puede usar para varias tareas de carga de datos con la misma factoría de datos. Una puerta de enlace se puede usar para conectarse con varios almacenes de datos cuando se ejecutan tareas de carga de datos.
    > 
    > Para información más detallada sobre la puerta de enlace, consulte el artículo [Data Management Gateway](../data-factory/data-factory-data-management-gateway.md). 

5. Se mostrará el cuadro de diálogo **Crear puerta de enlace**. En Nombre, escriba **GatewayForDWLoading**y haga clic en **Crear**.

6. Se mostrará el cuadro de diálogo **Configure Gateway** (Configurar puerta de enlace).  
    ![Inicio de la configuración rápida](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

7. Haga clic en **Iniciar la configuración rápida en este equipo** para descargar, instalar y registrar Data Management Gateway en la máquina actual (la máquina que usa para tener acceso al portal). Si la máquina no se puede conectar con el almacén de datos, puede [descargar e instalar manualmente la puerta de enlace](https://www.microsoft.com/download/details.aspx?id=39717) en una máquina que se puede conectar con el almacén de datos y, luego, usar la clave para registrarse. El progreso se muestra en una ventana emergente.

    > [!NOTE]
    > La configuración rápida funciona de forma nativa con Microsoft Edge e Internet Explorer. Si usa Google Chrome, instale primero la extensión de ClickOnce desde Chrome Web Store. 

8. Espere a que termine de configurarse la puerta de enlace. Cuando la puerta de enlace se haya registrado correctamente y esté en línea, se cerrará la ventana emergente y se mostrará la nueva puerta de enlace en el campo correspondiente. Haga clic en **Siguiente**.

9. El siguiente paso consiste en elegir las tablas desde las que se copiarán los datos. Puede filtrar las tablas usando palabras clave. Además, puede obtener una vista previa del esquema de datos y tablas en el panel inferior. Después de finalizar la selección, haga clic en **Siguiente**.

    ![Selección de tablas](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Configuración de la instancia de SQL Data Warehouse de destino

1. Haga clic en **Destino**. La información de conexión de SQL Data Warehouse se rellenará automáticamente.
2. Escriba la contraseña del nombre de usuario y haga clic en **Siguiente**.

    ![Configuración del destino](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

3. Se muestra una asignación de tabla inteligente que asigna las tablas de origen y de destino basándose en nombres similares. Ajuste
4.  Agregue la asignación de cualquier tabla y el resto se asignarán automáticamente aprendiendo del ejemplo. 
5. Revise la información y haga clic en **Siguiente**.

    ![Asignación de tablas](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

6. Revise la asignación de esquemas y busque mensajes de error. La asignación inteligente se basa en el nombre de las columnas. Si hay una conversión de tipos de datos no compatibles entre las columnas de origen y de destino, verá un mensaje de error junto a la tabla correspondiente.

    ![Esquema de asignación](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

7. Haga clic en **Next**.

## <a name="configure-the-performance-settings"></a>Configuración de las opciones de rendimiento
En las opciones de rendimiento, configure una cuenta de Azure Storage para usarla con el fin de almacenar provisionalmente los datos antes de que se carguen en SQL Data Warehouse.

1. Haga clic en **Rendimiento**. 
2. Seleccione una cuenta de Azure Storage y haga clic en **Siguiente**.

    ![Configuración del blob de almacenamiento provisional](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Revisión de la información de resumen e implementación de la canalización

1. Haga clic en **Resumen** y revise la información.
2. Haga clic en el botón **Finalizar** para implementar la canalización.

    ![Implementación de la factoría de datos](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Supervisión del progreso de carga de datos

Cuando termine la implementación, se mostrará la opción **Implementación** en el menú izquierdo. 

1. Haga clic en **Implementación**.
2. Para supervisar el progreso de carga de datos, haga clic en el vínculo **Click here to monitor copy pipeline** (Haga clic aquí para supervisar la canalización de copia).

    ![Supervisión de la canalización](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

3. En el **Explorador de recursos**, expanda los nodos de canalizaciones y haga clic en la canalización de carga de datos **DWLoadData-fromSQL
4. Server** que creó en este tutorial.

    ![Visualización de la canalización](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

5. Haga clic en la canalización para ver el estado detallado de cada tabla que se asigna a una actividad.

    ![Visualización de la actividad de tabla](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

6. Haga clic en una actividad y vea los detalles de carga de datos en el panel derecho, entre ellos, el tamaño de los datos, las filas, el rendimiento, etc.

    ![Visualización de la información de actividad de tabla](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

7. Para iniciar esta vista de supervisión más adelante, vaya a su instancia de SQL Data Warehouse, haga clic en **Cargar datos > Data Factory de Azure**, seleccione la fábrica y elija **Monitor existing loading tasks** (Supervisar tareas de carga existentes).

## <a name="next-steps"></a>Pasos siguientes

Para migrar la base de datos a SQL Data Warehouse, consulte el artículo de [introducción a la migración](sql-data-warehouse-overview-migrate.md).

Para más información sobre Azure Data Factory y sus funcionalidades de movimientos de datos, consulte los artículos siguientes: 

- [Introducción al servicio Data Factory de Azure](../data-factory/data-factory-introduction.md)
- [Movimiento de datos con la actividad de copia](../data-factory/data-factory-data-movement-activities.md)
- [Movimiento de datos hacia y desde Azure SQL Data Warehouse mediante Azure Data Factory](../data-factory/data-factory-azure-sql-data-warehouse-connector.md)

Para explorar los datos en SQL Data Warehouse, consulte los artículos siguientes: 

- [Conexión a SQL Data Warehouse con Visual Studio y SSDT](sql-data-warehouse-query-visual-studio.md) 
- [Datos visuales con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Portal de Azure]: https://portal.azure.com 


<!--HONumber=Dec16_HO2-->


