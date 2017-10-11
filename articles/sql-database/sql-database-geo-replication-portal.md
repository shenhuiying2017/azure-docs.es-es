---
title: "Azure Portal: replicación geográfica de SQL Database | Microsoft Docs"
description: "Configuración de replicación geográfica para Azure SQL Database en Azure Portal e inicio de la conmutación por error"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/06/2016
ms.author: carlrab
ms.openlocfilehash: db90fad2fe397f0c8466db6bdc1bd8c8d1cf8f15
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configuración de replicación geográfica activa para Azure SQL Database en Azure Portal e inicio de la conmutación por error

En este artículo se muestra cómo configurar la replicación geográfica activa para SQL Database en [Azure Portal](http://portal.azure.com) y cómo iniciar la conmutación por error.

Para iniciar una conmutación por error planeada con el Portal de Azure, consulte [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md).

Para configurar la replicación geográfica activa mediante Azure Portal, necesita el siguiente recurso:

* Una instancia de Azure SQL Database: la base de datos principal que quiere replicar en una región geográfica diferente.

> [!Note]
La replicación geográfica activa debe producirse entre bases de datos de la misma suscripción.

## <a name="add-a-secondary-database"></a>Agregar una base de datos secundaria
Los pasos siguientes crean otra base de datos secundaria en una asociación de replicación geográfica.  

Para agregar una base de datos secundaria, debe ser el propietario o copropietario de la suscripción.

La base de datos secundaria tiene el mismo nombre que la base de datos principal y, de forma predeterminada, presentan el mismo nivel de servicio. La base de datos secundaria puede ser una base de datos única o una de un grupo elástico. Para obtener más información, consulte el artículo sobre [niveles de servicio](sql-database-service-tiers.md).
Después de crear e inicializar la base de datos secundaria, los datos comienzan a replicarse desde la base de datos principal a la nueva base de datos secundaria.

> [!NOTE]
> Si la base de datos del asociado ya existe (por ejemplo, como resultado de la terminación de una relación de replicación geográfica anterior), se produce un error en el comando.
> 

1. En [Azure Portal](http://portal.azure.com), vaya a la base de datos que desea configurar para replicación geográfica.
2. En la página de SQL Database, seleccione **Replicación geográfica** y, luego, seleccione la región para crear la base de datos secundaria. Puede seleccionar cualquier región menos la que hospeda la base de datos principal, pero se recomienda la [región emparejada](../best-practices-availability-paired-regions.md).
   
    ![Configuración de la replicación geográfica](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Seleccione o configure el servidor y el plan de tarifa de la base de datos secundaria.
   
    ![Configuración de la secundaria](./media/sql-database-geo-replication-portal/create-secondary.png)
4. También puede agregar una base de datos secundaria a un grupo elástico. Para crear una base de datos secundaria en un grupo, haga clic en **Grupo elástico** y seleccione un grupo del servidor de destino. Ya debe existir un grupo en el servidor de destino. Este flujo de trabajo no crea un grupo.
5. Haga clic en **Crear** para agregar la base de datos secundaria.
6. Se crea la base de datos secundaria y comienza el proceso de inicialización.
   
    ![Configuración de la secundaria](./media/sql-database-geo-replication-portal/seeding0.png)
7. Cuando se completa el proceso de propagación, la base de datos secundaria muestra su estado.
   
    ![Propagación completa](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Inicio de una conmutación por error

La base de datos secundaria se puede cambiar para convertirse en la principal.  

1. En [Azure Portal](http://portal.azure.com), vaya a la base de datos principal de la asociación de replicación geográfica.
2. En la hoja SQL Database, seleccione **All settings** (Toda la configuración)  > **Replicación geográfica**.
3. En la lista **SECUNDARIAS**, seleccione la base de datos que quiere convertir en la nueva base de datos principal y haga clic en **Conmutación por error**.
   
    ![Conmutación por error](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Haga clic en **Sí** para iniciar la conmutación por error.

El comando cambiará inmediatamente la base de datos secundaria al rol principal. 

Hay un breve período durante el que ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. Si la base de datos principal tiene varias bases de datos secundarias, el comando reconfigura automáticamente las demás secundarias para conectarse a la nueva principal. En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse. 

> [!NOTE]
> Este comando está diseñado para una rápida recuperación de la base de datos en el caso de una interrupción. Desencadena una conmutación por error sin sincronización de datos (conmutación por error forzada).  Si la principal está conectada y confirmando transacciones cuando se emite el comando, es posible que produzca alguna pérdida de datos. 
> 
> 

## <a name="remove-secondary-database"></a>Elimine una base de datos secundaria
Esta operación termina de forma permanente la replicación en la base de datos secundaria y el rol de la base de datos secundaria cambia al de una base de datos de lectura y escritura normal. Si se interrumpe la conectividad con la base de datos secundaria, el comando se ejecuta correctamente, pero la base de datos secundaria no pasa a ser de lectura y escritura hasta después de restaurarse la conectividad.  

1. En [Azure Portal](http://portal.azure.com), vaya a la base de datos principal de la asociación de replicación geográfica.
2. En la página de SQL Database, seleccione **Replicación geográfica**.
3. En la lista **SECUNDARIAS**, seleccione la base de datos que desee quitar de la asociación de replicación geográfica.
4. Haga clic en **Detener replicación**.
   
    ![Quitar secundaria](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Se abrirá una ventana de confirmación. Haga clic en **Sí** para quitar la base de datos de la asociación de replicación geográfica. (Establezca el valor en una base de datos de lectura y escritura que no forme parte de ninguna replicación).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la replicación geográfica activa, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).

