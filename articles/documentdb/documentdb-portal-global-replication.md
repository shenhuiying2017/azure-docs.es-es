---
title: "Replicación global de bases de datos de DocumentDB | Microsoft Docs"
description: "Obtenga información sobre cómo administrar la replicación global de su cuenta de DocumentDB mediante el Portal de Azure."
services: documentdb
keywords: "base de datos global, replicación"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 691e2e9156a825e64890f035b23cb2526d502107
ms.openlocfilehash: c7d8cea9a15ec79356c89f628ef9d8e8ccbaaec3


---
# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Replicación global de bases de datos de DocumentDB mediante el Portal de Azure

Aprenda a usar el Portal de Azure para replicar datos en varias regiones para la disponibilidad global de los datos en Azure DocumentDB.

Para más información acerca de cómo funciona la replicación global de bases de datos en DocumentDB, consulte [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md). Para más información acerca de cómo realizar la replicación global de bases de datos mediante programación, consulte [Desarrollo con cuentas de DocumentDB de varias regiones](documentdb-developing-with-multiple-regions.md).

> [!NOTE]
> La distribución global de las bases de datos de DocumentDB está disponible con carácter general y habilitada automáticamente para cualquier cuenta de DocumentDB que se haya creado recientemente. Estamos trabajando para habilitar la distribución global en todas las cuentas existentes, pero, mientras tanto, si quiere que la habilitemos ahora en la suya, [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

## <a name="a-idaddregionaadd-global-database-regions"></a><a id="addregion"></a>Incorporación de regiones a la base de datos global
DocumentDB está disponible en la mayoría de las [regiones de Azure][azureregions]. Tras seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos, puede asociar una o varias regiones (según la elección del nivel de coherencia y las necesidades de distribución global).

1. En la barra de accesos directos de [Azure Portal](https://portal.azure.com/), haga clic en **NoSQL (DocumentDB)**.
2. En la hoja **NoSQL (DocumentDB)**, seleccione la cuenta de base de datos para modificar.
3. En la hoja de cuentas, haga clic en el menú **Replicar datos globalmente**.
4. En la hoja **Replicar datos globalmente**, seleccione las regiones para agregar o quitar y, luego, haga clic en **Guardar**. Agregar regiones conlleva un costo; consulte la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/) o el artículo [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md) para obtener más información.
   
    ![Haga clic en las regiones en el mapa para agregarlas o quitarlas][1]
    
Cuando agrega una segunda región, se habilita la opción **Conmutación por error manual** en el hoja **Replicate data locally** (Replicar datos localmente) del portal. Puede usar esta opción para probar el proceso de conmutación por error. Cuando agrega una tercera región, la opción **Prioridades de conmutación por error** se habilita en la misma hoja para que pueda cambiar el orden de la conmutación por error en las lecturas.  

### <a name="selecting-global-database-regions"></a>Selección de regiones de la base de datos global
Al configurar dos o más regiones, se recomienda que las regiones se seleccionen en función de los pares de regiones descritos en el artículo [Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure][bcdr].

En concreto, cuando se configuran varias regiones, asegúrese de seleccionar el mismo número de regiones (+/-1 para par o impar) de cada una de las columnas de regiones emparejadas. Por ejemplo, si quiere implementar en 4 regiones de Estados Unidos, seleccione 2 regiones de Estados Unidos en la columna izquierda y 2 en la derecha. Este sería un conjunto adecuado: oeste de EE. UU., este de EE. UU., centro-norte de EE. UU. y centro-sur de EE. UU.

Cuando solo hay dos regiones configuradas para escenarios de recuperación ante desastres, es importante seguir estas instrucciones. Cuando hay más de dos regiones, se recomienda seguir estas instrucciones, aunque no es obligatorio siempre y cuando algunas de las regiones seleccionadas respeten este emparejamiento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **NoSQL (DocumentDB)** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a name="a-idnextanext-steps"></a><a id="next"></a>Pasos siguientes
Para aprender a administrar la coherencia de la cuenta global replicada, lea [Niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).

Para más información acerca de cómo funciona la replicación global de bases de datos en DocumentDB, consulte [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md). Para más información acerca de cómo realizar la replicación de bases de datos en varias regiones mediante programación, consulte [Desarrollo con cuentas de DocumentDB de varias regiones](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO4-->


