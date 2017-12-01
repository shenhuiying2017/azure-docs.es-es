---
title: Uso de Azure Database Migration Service para migrar bases de datos de SQL Server a Azure SQL Database | Microsoft Docs
description: "En este artículo, se describe información sobre cómo migrar bases de datos de instancias locales de SQL Server a Azure SQL Database mediante Azure Database Migration Service."
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/17/2017
ms.openlocfilehash: 3938af29caec99f076452529cbc5d93cf2c8802b
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Migración de SQL Server a Azure SQL Database
Puede usar Azure Database Migration Service para migrar las bases de datos de una instancia de SQL Server local a Azure SQL Database. En este tutorial, migrará la base de datos **Adventureworks2012** restaurada en una instancia local de SQL Server 2016 (o posterior) a una instancia de Azure SQL Database mediante Azure Database Migration Service.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Evaluar la base de datos local mediante Data Migration Assistant
> * Migrar el esquema de ejemplo mediante Data Migration Assistant
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración mediante Azure Database Migration Service.
> * Ejecutar la migración.
> * Supervisar la migración

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesita:

- Descargar e instalar [SQL Server 2016 o versiones posteriores](https://www.microsoft.com/sql-server/sql-server-downloads) (cualquier edición).
- Habilitar el protocolo TCP/IP, que se deshabilita de forma predeterminada durante la instalación de SQL Server Express, siguiendo las instrucciones del artículo [Habilitar o deshabilitar un protocolo de red de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Configurar su [Firewall de Windows para acceder al motor de base de datos](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Crear una instancia de Azure SQL Database siguiendo la información del artículo sobre cómo [crear una instancia de Azure SQL Database en Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Descargar e instalar [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) versión 3.3 o posterior.
- Crear una red virtual para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Asegurarse de que las credenciales usadas para conectarse a la instancia de SQL Server de origen tenga permisos [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Asegurarse de que las credenciales usadas para conectarse a la instancia de Azure SQL Database de destino tengan permisos CONTROL DATABASE en las bases de datos SQL de Azure de destino.
- Abrir Firewall de Windows para permitir que Azure Database Migration Service acceda a la instancia de SQL Server de origen.

## <a name="assess-your-on-premises-database"></a>Evaluación de una base de datos local
Antes de poder migrar datos de una instancia de SQL Server local a Azure SQL Database, debe evaluar la base de datos de SQL Server por si hay cualquier error de bloqueo que impida que se realice la migración. En Data Migration Assistant v3.3 o versiones posteriores, siga los pasos descritos en el artículo sobre cómo [realizar una evaluación de migración de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) para completar la evaluación de la base de datos local. A continuación, se muestra un resumen de los pasos necesarios:
1.  En Data Migration Assistant, seleccione el icono de Nuevo (+) y, luego, seleccione el tipo de proyecto **Evaluación**.
2.  Especifique un nombre de proyecto. En el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **SQL Server** y, luego, en el cuadro de texto **Target server type** (Tipo de servidor de destino), elija **Azure SQL Database**.
3.  Seleccione **Crear** para crear el proyecto.

    Cuando esté evaluando la base de datos de SQL Server de origen que se migrará a Azure SQL Database, puede elegir uno o ambos de los siguientes tipos de informes de evaluación:
    - Check database compatibility (Comprobar compatibilidad de bases de datos)
    - Check feature parity (Comprobar paridad de características)

    De forma predeterminada, se seleccionan los dos tipos de informes.
4.  En Data Migration Assistant, en la pantalla **Opciones**, seleccione **Siguiente**.
5.  En la pantalla **Seleccionar orígenes**, cuadro de diálogo **Conectar a un servidor**, proporcione los detalles de conexión en su instancia de SQL Server y, luego, seleccione **Conectar**.
6.  En el cuadro de diálogo **Agregar orígenes**, seleccione **AdventureWorks2012**, seleccione **Agregar** y, por último, seleccione **Iniciar evaluación**.

    Una vez completada la evaluación, los resultados se muestran tal y como se muestra en el siguiente gráfico:

    ![Evaluación de la migración de datos](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    En Azure SQL Database, las evaluaciones identifican problemas de bloqueo de migración y de paridad de características.

7.  Revise los resultados de evaluación correspondientes a los problemas de bloqueo de migración y los de paridad de características seleccionando las opciones correspondientes.
    - La categoría de **paridad de características de SQL Server** proporciona un conjunto completo de recomendaciones, alternativas disponibles en Azure y pasos de mitigación para ayudarlo a planear el trabajo en los proyectos de migración.
    - La categoría de **problemas de compatibilidad** proporciona características no compatibles o parcialmente compatibles que reflejan los problemas de compatibilidad que podrían bloquear la migración de bases de datos de SQL Server locales a bases de datos de Azure SQL Database. También se proporcionan recomendaciones para ayudarlo a resolver esos problemas.


## <a name="migrate-the-sample-schema"></a>Migración del esquema de ejemplo
Cuando se haya familiarizado con la evaluación y piense que la base de datos seleccionada es una buena candidata para la migración a Azure SQL Database, use Data Migration Assistant para migrar el esquema a Azure SQL Database.

> [!NOTE]
> Antes de crear un proyecto de migración en Data Migration Assistant, asegúrese de que ya ha proporcionado una base de datos de Azure SQL Database, tal y como se mencionó en los requisitos previos. Para los fines de este tutorial, se da por hecho que el nombre de la base de datos de Azure SQL Database es **AdventureWorksAzure**, pero puede asignarle otro si desea.

Para migrar el esquema de **AdventureWorks2012** a Azure SQL Database, siga estos pasos:

1.  En Data Migration Assistant, seleccione el icono de Nuevo (+) y, en **Tipo de proyecto**, seleccione el tipo de proyecto **Migración**.
3.  Especifique un nombre de proyecto. En el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **SQL Server** y, luego, en el cuadro de texto **Target server type** (Tipo de servidor de destino), elija **Azure SQL Database**.
4.  En **Migration Scope** (Ámbito de la migración), seleccione **Solo esquema**.

    Después de realizar los pasos anteriores, la interfaz de Data Migration Assistant debe aparecer como se muestra en el siguiente gráfico:
    
    ![Creación de proyecto en Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Seleccione **Crear** para crear el proyecto.
6.  En Data Migration Assistant, especifique los detalles de conexión de origen de SQL Server, seleccione **Conectar** y, luego, elija la base de datos **AdventureWorks2012**.

    ![Detalles de conexión de origen en Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Seleccione **Siguiente** en **Connect to target server** (Conectar a servidor de destino), especifique los detalles de conexión de destino de la base de datos de Azure SQL Database, seleccione **Conectar** y, luego, elija la base de datos **AdventureWorksAzure** que había aprovisionado previamente en Azure SQL Database.

    ![Detalles de conexión de destino en Data Migration Assistant](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Seleccione **Siguiente** para avanzar a la pantalla **Seleccionar objetos**, en la que puede especificar los objetos de esquema de la base de datos **AdventureWorks2012** que deben implementarse en Azure SQL Database.

    De forma predeterminada, se seleccionan todos los objetos.

    ![Generación de scripts SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Seleccione **Generar script SQL** para crear los scripts SQL y, luego, revíselos para ver si hay errores.

    ![Script de esquema](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Seleccione **Deploy schema** (Implementar esquema) para implementar el esquema en Azure SQL Database. Luego, una vez implementado, compruebe el servidor de destino por si hay cualquier anomalía.

    ![Implementación del esquema](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registro del proveedor de recursos Microsoft.DataMigration
1. Inicie sesión en Azure Portal, seleccione **Todos los servicios** y, después, **Suscripciones**.
 
   ![Mostrar suscripciones en el portal](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Seleccione la suscripción en la que quiere crear la instancia de Azure Database Migration Service y después seleccione **Proveedores de recursos**.
 
    ![Mostrar los proveedores de recursos](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  Busque la migración y después, a la derecha de **Microsoft.DataMigration**, seleccione **Registrar**.
 
    ![Registro del proveedor de recursos](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>Creación de una instancia
1.  En Azure Portal, seleccione **+ Crear un recurso**, busque Azure Database Migration Service y, luego, seleccione **Azure Database Migration Service** en la lista desplegable.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  En la pantalla **Azure Database Migration Service (preview)** [Azure Database Migration Service (versión preliminar)], seleccione **Crear**.
 
    ![Creación de una instancia de Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  En la pantalla **Database Migration Service**, especifique un nombre para el servicio, la suscripción, una red virtual y el plan de tarifa.

    Para más información sobre los costos y planes de tarifa, vea la [página de precios](https://aka.ms/dms-pricing).

     ![Configuración de la instancia de Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Seleccione **Crear** para crear el servicio.

## <a name="create-a-migration-project"></a>Creación de un proyecto de migración
Después de crear el servicio, búsquelo en Azure Portal y, luego, cree un proyecto de migración.
1. En Azure Portal, seleccione **Todos los servicios**, busque Azure Database Migration Service y, luego, elija **Azure Database Migration Services**.
 
      ![Búsqueda de todas las instancias de Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. En la pantalla **Azure Database Migration Services**, busque el nombre de la instancia de Azure Database Migration Services creada y, luego, elija la instancia.
 
     ![Busque la instancia de Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Seleccione **+ New Migration Project** (+ Nuevo proyecto de migración).
4. En la pantalla **New Migration Project** (Nuevo proyecto de migración), especifique un nombre para el proyecto. En el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **SQL Server** y, luego, en el cuadro de texto **Target server type** (Tipo de servidor de destino), elija **Azure SQL Database**.

    ![Creación de un proyecto de Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Seleccione **Crear** para crear el proyecto.


## <a name="specify-source-details"></a>Especificación de los detalles de origen
1. En la pantalla **Detalles del origen**, especifique los detalles de conexión de la instancia de SQL Server de origen.

    ![Selección del origen](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Seleccione **Guardar** y, luego, elija la base de datos **AdventureWorks2012** para la migración.

    ![Selección de la base de datos de origen](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Especificación de los detalles de destino
1. Seleccione **Guardar** y, luego, en la pantalla **Detalles del destino**, especifique los detalles de conexión del destino, que es la base de datos de Azure SQL Database que se aprovisionó previamente, en la que el esquema de **AdventureWorks2012** se implementó mediante Data Migration Assistant.

    ![Selección del destino](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Seleccione **Guardar** para guardar el proyecto.
3. En la pantalla **Migration summary** (Resumen de migración), revise y compruebe los detalles relacionados con el proyecto de migración.

    ![Resumen de Database Migration Services](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Seleccione **Guardar**.

## <a name="run-the-migration"></a>Ejecución de la migración
1.  Seleccione el proyecto guardado recientemente, elija **+ Nueva actividad** y, luego, elija **Run Data Migration** (Ejecutar migración de datos).

    ![Nueva actividad](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  Cuando se le solicite, escriba las credenciales de los servidores de origen y de destino y, luego, seleccione **Guardar**.
3.  En la pantalla **Map to target databases** (Asignar a bases de datos de destino), asigne la base de datos de origen y de destino para la migración.

    Si la base de datos de destino contiene el mismo nombre de base de datos que la de origen, Azure Database Migration Services selecciona la base de datos de destino de forma predeterminada.

    ![Asignación a bases de datos de destino](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Seleccione **Guardar**, en la pantalla **Seleccionar tablas**, expanda la lista de tabla y revise la lista de campos afectados.

    ![Selección de tablas](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Seleccione **Guardar**, en la pantalla **Migration summary** (Resumen de migración) y, en el cuadro de texto **Nombre de actividad**, especifique un nombre para la actividad de migración.

    En esta pantalla, también se puede expandir la pantalla **Choose validation option** (Elegir opción de validación), que se puede utilizar para especificar que se valide la base de datos migrada con respecto a estas opciones:
    - Esquema
    - Data Consistency (Coherencia de datos)
    - Query Correctness and Performance (Rendimiento y corrección de las consultas)

    ![Selección de la opción de validación](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Seleccione **Guardar**, y revise el resumen para asegurarse de que los detalles de origen y de destino coinciden con los especificado anteriormente.

    ![Resumen de migración](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Seleccione **Run migration** (Ejecutar migración) para iniciar la actividad de migración y, luego, elija **Actualizar** para revisar el estado actual.

    ![Estado de la actividad](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Supervisión de la migración
1. Seleccione la actividad de migración para revisar el estado de la actividad.
2. Compruebe la base de datos de Azure SQL Database de destino una vez completada la migración.

    ![Completed](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
