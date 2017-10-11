---
title: Carga de datos de SQL Server en Azure SQL Data Warehouse (SSIS) | Microsoft Docs
description: "Se muestra cómo crear un paquete de SQL Server Integration Services (SSIS) para mover datos desde una gran variedad de orígenes de datos a Almacenamiento de datos SQL."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Cree un paquete de SQL Server Integration Services (SSIS) para cargar datos de SQL Server en Almacenamiento de datos SQL de Azure. Opcionalmente, puede reestructurar, transformar y limpiar los datos cuando pasen por el flujo de datos de SSIS.

En este tutorial, aprenderá lo siguiente:

* Crear un proyecto de Integration Services en Visual Studio.
* Conectarse a orígenes de datos, incluidos SQL Server (como origen) y Almacenamiento de datos SQL (como destino).
* Diseñar un paquete de SSIS que carga datos desde el origen en el destino.
* Ejecutar el paquete de SSIS para cargar los datos.

En este tutorial se utiliza SQL Server como origen de datos. SQL Server se puede estar ejecutando en el entorno local o en una máquina virtual de Azure.

## <a name="basic-concepts"></a>Conceptos básicos
El paquete es la unidad de trabajo de SSIS. Los paquetes relacionados se agrupan en proyectos. Los proyectos se crean y los paquetes se diseñan en Visual Studio con SQL Server Data Tools. El proceso de diseño es un proceso visual en el que se arrastran componentes desde el cuadro de herramientas y se colocan en la superficie de diseño, se conectan y se establecen sus propiedades. Después de finalizar el paquete, puede implementarlo opcionalmente en SQL Server para disponer de funcionalidad completa de administración, supervisión y seguridad.

## <a name="options-for-loading-data-with-ssis"></a>Opciones para cargar datos con SSIS
SQL Server Integration Services (SSIS) es un conjunto flexible de herramientas que proporciona un abanico de opciones para conectarse a datos y cargarlos en Almacenamiento de datos SQL.

1. Use un destino de ADO.NET para conectarse a Almacenamiento de datos SQL. En este tutorial se usa un destino de ADO.NET porque es el que tiene menos opciones de configuración.
2. Use un destino de OLE DB para conectarse a Almacenamiento de datos SQL. Esta opción puede proporcionar un rendimiento algo mejor que el destino de ADO.NET.
3. Utilice la tarea de carga de blobs de Azure para copiar los datos provisionalmente al Almacenamiento de blobs de Azure. Después, use la tarea Ejecutar SQL de SSIS para iniciar un script de Polybase que carga los datos en Almacenamiento de datos SQL. Esta opción proporciona el mejor rendimiento de las tres que se muestran aquí. Para obtener la tarea de carga de blobs de Azure, tiene que descargar el paquete [Microsoft SQL Server 2016 Integration Services Feature Pack for Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure] (Microsoft SQL Server 2016 Integration Services Feature Pack para Azure). Para aprender más sobre Polybase, consulte [PolyBase Guide][PolyBase Guide] (Guía de PolyBase).

## <a name="before-you-start"></a>Antes de comenzar
Para seguir paso a paso este tutorial, necesita:

1. **SQL Server Integration Services (SSIS)**. SSIS es un componente de SQL Server y requiere una versión de evaluación o una versión con licencia de SQL Server. Para conseguir una versión de evaluación de SQL Server 2016 Preview, consulte [Evaluaciones de SQL Server][SQL Server Evaluations].
2. **Visual Studio**. Para conseguir de forma gratuita Visual Studio Community Edition, vaya a [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools para Visual Studio (SSDT)**. Para obtener SQL Server Data Tools para Visual Studio, vaya a [Descargar SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Datos de ejemplo**. En este tutorial, se usan datos de ejemplo almacenados en SQL Server (en la base de datos de ejemplo AdventureWorks) como datos de origen que se cargan en Almacenamiento de datos SQL. Para conseguir la base de datos de ejemplo AdventureWorks, consulte [AdventureWorks 2014 Sample Databases][AdventureWorks 2014 Sample Databases] (Bases de datos de ejemplo AdventureWorks 2014).
5. **Una base de datos de Almacenamiento de datos SQL y permisos**. En este tutorial se conecta a una instancia de Almacenamiento de datos SQL y se cargan datos en ella. Necesita permisos para crear una tabla y para cargar datos.
6. **Una regla de firewall**. Tendrá que crear una regla de firewall en Almacenamiento de datos SQL con la dirección IP del equipo local antes de cargar datos en Almacenamiento de datos SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Paso 1: Creación de un proyecto de Integration Services
1. Inicie Visual Studio.
2. En el menú **Archivo**, seleccione **Nuevo | Proyecto**.
3. Vaya a los tipos de proyecto **Instalado | Plantillas | Business Intelligence | Integration Services** .
4. Seleccione **Proyecto de Integration Services**. Proporcione los valores de **Nombre** y **Ubicación**, y seleccione **Aceptar**.

Se abre Visual Studio y se crea un proyecto de Integration Services (SSIS). Después, en Visual Studio se abre el diseñador para el nuevo paquete de SSIS (Package.dtsx) en el proyecto. Verá las siguientes áreas en la pantalla:

* A la izquierda, el **cuadro de herramientas** con los componentes de SSIS.
* En el centro, la superficie de diseño, con varias pestañas. Normalmente las que menos se usan son **Flujo de control** y **Flujo de datos**.
* A la derecha, verá los paneles **Explorador de soluciones** y **Propiedades**.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Paso 2: Creación del flujo de datos básico
1. Arrastre una tarea Flujo de datos del cuadro de herramientas hasta el centro de la superficie de diseño (en la pestaña **Flujo de control** ).
   
    ![][02]
2. Haga doble clic en la tarea Flujo de datos para cambiar a la pestaña Flujo de datos.
3. En la lista Otros orígenes del cuadro de herramientas, arrastre un origen de ADO.NET a la superficie de diseño. Con el adaptador de origen aún seleccionado, cambie su nombre a **Origen de SQL Server** en el panel **Propiedades**.
4. En la lista Otros destinos del cuadro de herramientas, arrastre un destino de ADO.NET a la superficie de diseño bajo el origen de ADO.NET. Con el adaptador de destino aún seleccionado, cambie su nombre a **Destino de SQL DW** en el panel **Propiedades**.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Paso 3: Configuración del adaptador de origen
1. Haga doble clic en el adaptador de origen para abrir el **Editor de orígenes de ADO.NET**.
   
    ![][03]
2. En la pestaña **Administrador de conexiones** del **Editor de orígenes de ADO.NET**, haga clic en el botón **Nuevo** junto a la lista **Administrador de conexiones de ADO.NET** para abrir el cuadro de diálogo **Configurar el administrador de conexiones ADO.NET** y cree la configuración de conexión para la base de datos de SQL Server desde la que se cargan los datos en este tutorial.
   
    ![][04]
3. En el cuadro de diálogo **Configurar el administrador de conexiones ADO.NET**, haga clic en el botón **Nuevo** para abrir el cuadro de diálogo **Administrador de conexiones y cree una conexión de datos**.
   
    ![][05]
4. En el cuadro de diálogo **Administrador de conexiones** , realice lo siguiente.
   
   1. Para **Proveedor**, seleccione el proveedor de datos SqlClient.
   2. Para **Nombre del servidor**, especifique el nombre del servidor SQL Server.
   3. En la sección **Iniciar sesión en el servidor** , seleccione o especifique la información de autenticación.
   4. En la sección **Conectar con una base de datos** , seleccione la base de datos de ejemplo AdventureWorks.
   5. Haga clic en **Probar conexión**.
      
       ![][06]
   6. En el cuadro de diálogo que muestra los resultados de la prueba de conexión, haga clic en **Aceptar** para volver al cuadro de diálogo **Administrador de conexiones**.
   7. En el cuadro de diálogo **Administrador de conexiones**, haga clic en **Aceptar** para volver al cuadro de diálogo **Configurar el administrador de conexiones ADO.NET**.
5. En el cuadro de diálogo **Configurar el administrador de conexiones ADO.NET**, haga clic en **Aceptar** para volver al **Editor de orígenes de ADO.NET**.
6. En el **Editor de orígenes de ADO.NET**, en la lista **Nombre de la tabla o la vista**, seleccione la tabla **Sales.SalesOrderDetail**.
   
    ![][07]
7. Haga clic en **Vista previa** para ver las 200 primeras filas de datos de la tabla de origen en el cuadro de diálogo **Vista previa de los resultados de la consulta**.
   
    ![][08]
8. En el cuadro de diálogo **Vista previa de los resultados de la consulta**, haga clic en **Cerrar** para volver a la **Editor de orígenes de ADO.NET**.
9. En el **Editor de orígenes de ADO.NET**, haga clic en **Aceptar** para finalizar la configuración del origen de datos.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Paso 4: Conexión del adaptador de origen al adaptador de destino
1. Seleccione el adaptador de origen en la superficie de diseño.
2. Seleccione la flecha azul que se extiende desde el adaptador de origen y arrástrela al editor de destino hasta que ajuste en su lugar.
   
    ![][10]
   
    En un paquete de SSIS típico, se usan otros componentes del cuadro de herramientas de SSIS entre el origen y el destino para reestructurar, transformar y limpiar los datos cuando pasan por el flujo de datos de SSIS. Para no complicar este ejemplo, vamos a conectar el origen directamente al destino.

## <a name="step-5-configure-the-destination-adapter"></a>Paso 5: Configuración del adaptador de destino
1. Haga doble clic en el adaptador de destino para abrir el **Editor de destinos de ADO.NET**.
   
    ![][11]
2. En la pestaña **Administrador de conexiones** del **Editor de destinos de ADO.NET**, haga clic en el botón **Nuevo** junto a la lista **Administrador de conexiones** para abrir el cuadro de diálogo **Configurar el administrador de conexiones ADO.NET** y cree la configuración de conexión para la base de datos de Azure SQL Data Warehouse en la que se cargan los datos en este tutorial.
3. En el cuadro de diálogo **Configurar el administrador de conexiones ADO.NET**, haga clic en el botón **Nuevo** para abrir el cuadro de diálogo **Administrador de conexiones y cree una conexión de datos**.
4. En el cuadro de diálogo **Administrador de conexiones** , realice lo siguiente.
   1. Para **Proveedor**, seleccione el proveedor de datos SqlClient.
   2. Para **Nombre del servidor**, escriba el nombre de Almacenamiento de datos SQL.
   3. En la sección **Iniciar sesión en el servidor**, seleccione **Usar autenticación de SQL Server** y escriba la información de autenticación.
   4. En la sección **Conectar con una base de datos** , seleccione una base de datos de Almacenamiento de datos SQL existente.
   5. Haga clic en **Probar conexión**.
   6. En el cuadro de diálogo que muestra los resultados de la prueba de conexión, haga clic en **Aceptar** para volver al cuadro de diálogo **Administrador de conexiones**.
   7. En el cuadro de diálogo **Administrador de conexiones**, haga clic en **Aceptar** para volver al cuadro de diálogo **Configurar el administrador de conexiones ADO.NET**.
5. En el cuadro de diálogo **Configurar el administrador de conexiones ADO.NET**, haga clic en **Aceptar** para volver al **Editor de destinos de ADO.NET**.
6. En el **Editor de destinos de ADO.NET**, haga clic en **Nuevo** junto a la lista **Usar una tabla o vista** para abrir el cuadro de diálogo **Crear tabla** y crear una tabla de destino con una lista de columnas que coincida con la tabla de origen.
   
    ![][12a]
7. En el cuadro de diálogo **Crear tabla** , realice lo siguiente.
   
   1. Cambie el nombre de la tabla de destino a **SalesOrderDetail**.
   2. Quite la columna **rowguid** . El tipo de datos **uniqueidentifier** no se admite en Almacenamiento de datos SQL.
   3. Cambie el tipo de datos de la columna **LineTotal** a **money**. El tipo de datos **decimal** no se admite en Almacenamiento de datos SQL. Para información sobre los tipos de datos compatibles, consulte [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)] [CREATE TABLE (SQL Data Warehouse de Azure, Almacenamiento de datos paralelos)].
      
       ![][12b]
   4. Haga clic en **Aceptar** para crear la tabla y volver al **Editor de destinos de ADO.NET**.
8. En el **Editor de destinos de ADO.NET**, seleccione la pestaña **Asignaciones** para ver cómo se asignan las columnas del origen a las del destino.
   
    ![][13]
9. Haga clic en **Aceptar** para finalizar la configuración del origen de datos.

## <a name="step-6-run-the-package-to-load-the-data"></a>Paso 6: Ejecución del paquete de SSIS para cargar los datos
Para ejecutar el paquete, haga clic en el botón **Iniciar** de la barra de herramientas o seleccione una de las opciones de **Ejecutar** en el menú **Depurar**.

Cuando el paquete comience a ejecutarse, verá que las ruedas amarillas giran para indicar actividad, así como el número de filas procesadas hasta el momento.

![][14]

Cuando se termine de ejecutar el paquete, verá marcas de verificación verdes para indicar que el proceso se ha completado correctamente, así como el número total de filas de datos que se han cargado desde el origen en el destino.

![][15]

¡Enhorabuena! Ha usado correctamente SQL Server Integration Services para cargar datos en Almacenamiento de datos SQL de Azure.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda más sobre el flujo de datos de SSIS. Comience aquí: [Flujo de datos][Data Flow].
* Aprenda a depurar paquetes y a solucionar problemas relacionados directamente en el entorno de diseño. Comience aquí: [Herramientas para solucionar problemas con el desarrollo de paquetes][Troubleshooting Tools for Package Development].
* Aprenda a implementar sus proyectos y paquetes de SSIS en un servidor de Integration Services o en otra ubicación de almacenamiento. Comience aquí: [Implementación de proyectos y paquetes][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
