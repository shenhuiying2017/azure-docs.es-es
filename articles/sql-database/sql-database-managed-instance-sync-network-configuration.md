---
title: Azure App Service - Sincronización de la configuración de red | Microsoft Docs
description: En este artículo se describe cómo sincronizar la configuración de red para el plan de hospedaje de Azure App Service.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 5b139b1279776acfca63def25a9fdae0f627a727
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronización de la configuración de red para el plan de hospedaje de Azure App Service

Puede suceder que aunque haya [integrado la aplicación con una red de Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md) no pueda establecer conexión con Instancia administrada. Puede intentar actualizar la configuración de red para el plan de servicio que tenga. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Sincronización de la configuración de red para el plan de hospedaje de App Service

Para ello, sigue estos pasos:  

1. Vaya al plan de App Service de las aplicaciones web.
 
   ![plan de App Service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Haga clic en **Redes** y, a continuación, en **Haga clic aquí para administrar**.
 
   ![administración del plan de servicio](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Seleccione **Red virtual** y haga clic en **Sincronizar red**. 
 
   ![sincronización de red](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Espere hasta que se realice la sincronización.
  
   ![sincronización acabada](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Ya está preparado para intentar volver a establecer la conexión con su instancia administrada.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo configurar la red virtual para Instancia administrada, consulte [Managed Instance VNet configuration](sql-database-managed-instance-vnet-configuration.md) (Configuración de una red virtual de una instancia administrada).
