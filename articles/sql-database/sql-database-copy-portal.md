---
title: Copia de una base de datos de Azure SQL mediante Azure Portal | Microsoft Docs
description: "Creación de una copia de una base de datos SQL de Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 752dcb97101f4a8288125e3e41167095620d130f
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copia de una instancia de Base de datos SQL de Azure mediante el Portal de Azure

En los siguientes pasos se muestra cómo copiar una base de datos SQL con [Azure Portal](https://portal.azure.com) en el mismo servidor o en otro distinto. 

> [!NOTE]
> También puede copiar una base de datos de SQL Database mediante [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) o [Transact-SQL](sql-database-copy-transact-sql.md).
>

Para copiar una base de datos SQL, necesita lo siguiente:

* Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
* Una base de datos SQL para copiar. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).

## <a name="copy-your-sql-database"></a>Copiar la base de datos SQL
Abra la página de base de datos SQL correspondiente a la base de datos que desea copiar:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Más servicios** > **Bases de datos SQL** y, luego, en la base de datos que quiera.
3. En la página de base de datos SQL, haga clic en **Copiar**:
   
   ![Base de datos SQL](./media/sql-database-copy-portal/sql-database-copy.png)
4. En la página **Copiar** , se proporciona un nombre predeterminado para la base de datos. Escriba un nombre diferente si quiere (todas las bases de datos de un servidor deben tener nombres únicos).
5. Seleccione un **Servidor de destino**. El servidor de destino es donde se creará la copia de la base de datos. Puede crear la copia de la base de datos en el mismo servidor o en otro distinto. Puede crear un nuevo servidor o seleccionar un servidor existente en la lista. 
6. Después de seleccionar el **Servidor de destino**, se habilitarán las opciones **Grupo elástico** y **Plan de tarifa**. Si el servidor tiene un grupo, puede copiar la base de datos en él.
7. Haga clic en **Aceptar** para iniciar el proceso de copia.
   
   ![Base de datos SQL](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>Supervisar el progreso de la operación de copia
* Después de iniciar la copia, haga clic en la notificación de portal para obtener información.
  
    ![notificación][3]
  
    ![monitor][4]

## <a name="verify-the-database-is-live-on-the-server"></a>Comprobar que la base de datos está disponible en el servidor
* Haga clic en **Más servicios** > **Bases de datos SQL** y compruebe que la nueva base de datos está **en línea**.

## <a name="resolve-logins"></a>Resolución de inicios de sesión
Para resolver los inicios de sesión una vez completada la operación de copia, consulte [Resolución de inicios de sesión](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente, consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) .
* Para exportar una base de datos a un archivo BACPAC mediante Azure Portal, vea [Export an Azure SQL database to a BACPAC file using the Azure portal](sql-database-export-portal.md) (Exportación de una base de datos de Azure SQL a un archivo BACPAC mediante Azure Portal).
* [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
* [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->

[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


