---
title: "Creación de una instancia de Azure Database Migration Service mediante Azure Portal | Microsoft Docs"
description: Utilice Azure Portal para crear una instancia de Azure Database Migration Service
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 12/13/2017
ms.openlocfilehash: 9dea80b0a6848bd69541aa9f7e0a0fe111fa0a28
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Creación de una instancia de Azure Database Migration Service mediante Azure Portal
En esta guía de inicio rápido, puede usar Azure Portal para crear una instancia de Azure Database Migration Service.  Después de crear el servicio, puede usarlo para migrar datos de SQL Server local a una base de datos de Azure SQL Database.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Abra el explorador web, vaya a [Microsoft Azure Portal](https://portal.azure.com/) y, a continuación, escriba sus credenciales para iniciar sesión en el portal.

La vista predeterminada es el panel del servicio.

## <a name="register-the-resource-provider"></a>Registrar el proveedor de recursos
Registre el proveedor de recursos Microsoft.DataMigration antes de crear su primera instancia de Database Migration Service.

1. En Azure Portal, seleccione **Todos los servicios** y, después, **Suscripciones**.

2. Seleccione la suscripción en la que quiere crear la instancia de Azure Database Migration Service y después seleccione **Proveedores de recursos**.

3. Busque la migración y después, a la derecha de Microsoft.DataMigration, seleccione **Registrar**.

![Registro del proveedor de recursos](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Creación de una instancia del servicio
1. Haga clic en **+ Crear un recurso** para crear una instancia de Azure Database Migration Service, que actualmente está en versión preliminar.

2. Busque "migration" en Marketplace, seleccione **Azure Database Migration Service** y, en la pantalla **Azure Database Migration Service (versión preliminar)**, haga clic en **Crear**.

3. En la pantalla **Database Migration Service**: 

    - Elija un **nombre del servicio** que sea fácil de recordar y único para identificar la instancia de Azure Database Migration Service.
    - Seleccione la **suscripción** de Azure donde desea crear la instancia.
    - Cree una nueva **red** con un nombre único.
    - Elija la **ubicación** más cercana a su servidor de origen o de destino.
    - Seleccione Básico: 1 vCore para el **plan de tarifa**.

    ![Creación del servicio de migración](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. Seleccione **Crear**.

Transcurridos unos instantes, se crea la instancia de Azure Database Migration Service y estará lista para usarse. Database Migration Service se muestra tal como aparece en la imagen siguiente:

![Servicio de migración creado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
Puede limpiar los recursos creados en esta guía de inicio rápido mediante la eliminación del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md).  Para eliminar el grupo de recursos, navegue a la instancia de Azure Database Migration Service que creó. Seleccione el nombre del **grupo de recursos** y, luego, seleccione **Eliminar grupo de recursos**.  Esta acción elimina todos los activos del grupo de recursos, así como el mismo grupo.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de SQL Server local a Azure SQL Database](tutorial-sql-server-to-azure-sql.md)