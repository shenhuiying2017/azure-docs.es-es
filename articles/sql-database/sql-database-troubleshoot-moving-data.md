---
title: "Movimiento de bases de datos de un servidor a otro, de una suscripción a otra, y dentro y fuera de Azure."
description: "Pasos rápidos para copiar, mover y migrar datos y bases de datos en Base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Movimiento de bases de datos de un servidor a otro, de una suscripción a otra, y dentro y fuera de Azure
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover una base de datos a un servidor diferente en la misma suscripción
* En [Azure Portal](https://portal.azure.com), haga clic en **Bases de datos SQL**, seleccione una base de datos de la lista y haga clic en **Copiar**. Para más información, consulte [Copia de una Base de datos SQL de Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover una base de datos de una suscripción a otra
* En el [Portal de Azure](https://portal.azure.com), haga clic en **Servidores SQL Server** y, en la lista, seleccione el servidor que hospeda la base de datos. Haga clic en **Mover**y seleccione los recursos que va a mover y la suscripción a la que los va a mover.

## <a name="to-migrate-a-sql-database-into-azure"></a>Para migrar una base de datos SQL a Azure
* Determine la compatibilidad de la base de datos y seleccione el método de migración más adecuado según sus necesidades. Siga las instrucciones y las opciones que se describen en [Migración de una base de datos de SQL Server a una Base de datos SQL en la nube](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Para crear una copia de una base de datos para su uso fuera de Azure
* [Exportación de un archivo BACPAC.](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


