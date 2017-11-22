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
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>Creación de una instancia de Azure Database Migration Service mediante Azure Portal
En esta guía de inicio rápido, va a usar Azure Portal para crear una instancia de Azure Database Migration Service.  Después de crear el servicio, podrá usarlo para migrar datos de SQL Server local a una base de datos de Azure SQL Database.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Abra el explorador web y vaya a [Microsoft Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="create-azure-database-migration-service"></a>Creación de Azure Database Migration Service
1. Haga clic en **+** para crear un nuevo servicio.  Database Migration Service todavía está en versión preliminar.  

1. Busque en Marketplace "migration", seleccione "Database Migration Service (preview)" y haga clic en **Crear**.

    ![Creación del servicio de migración](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Elija un **nombre del servicio** que sea fácil de recordar y único para identificar la instancia de Azure Database Migration Service.
    - Seleccione la **suscripción** de Azure donde desea crear la instancia de Database Migration Service.
    - Cree una nueva **red** con un nombre único.
    - Elija la **ubicación** más cercana a su servidor de origen o de destino.
    - Seleccione Básico: 1 vCore para el **plan de tarifa**.

1. Haga clic en **Crear**.

Transcurridos unos instantes, el servicio Azure Database Migration Service se crea y estará listo para usarse.  Podrá ver la instancia de Database Migration Service tal como se muestra en la imagen.

![Servicio de migración creado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
Puede limpiar los recursos que ha creado en la guía de inicio rápido mediante la eliminación del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md).  Para eliminar el grupo de recursos, navegue a la instancia de Database Migration Service que ha creado, haga clic en el nombre del **grupo de recursos** y después seleccione **Eliminar grupo de recursos**.  Esta acción elimina todos los activos del grupo de recursos, así como el mismo grupo.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de SQL Server local a Azure SQL Database](tutorial-sql-server-to-azure-sql.md)