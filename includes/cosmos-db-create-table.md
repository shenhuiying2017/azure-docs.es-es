---
title: archivo de inclusión
description: archivo de inclusión
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 37d7b1d44c2a4b2f3cd2fd3ac881b106d5056279
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429615"
---
Ahora puede usar la herramienta Explorador de datos en Azure Portal para crear una base de datos y una tabla. 

1. Haga clic en **Explorador de datos** > **Nueva tabla**. 
    
    El área **Agregar tabla** se muestra en el extremo derecho, pero es posible que tenga que desplazarse hacia la derecha para verla.

    ![Explorador de datos en Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. En la página **Agregar tabla**, escriba la configuración de la nueva tabla.

    Configuración|Valor sugerido|DESCRIPCIÓN
    ---|---|---
    Id. de tabla|sample-table|Identificador de la nueva tabla. Los nombres de tabla tienen los mismos requisitos de caracteres que los identificadores de base de datos. Los nombres de bases de datos deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final.
    Capacidad de almacenamiento| Fija (10 GB)|Use el valor predeterminado **Fija (10 GB)**. Este valor corresponde a la capacidad de almacenamiento de la base de datos.
    Throughput|400 RU|Cambie el rendimiento a 400 unidades de solicitud por segundo (RU/s). Si quiere reducir la latencia, puede escalar verticalmente el rendimiento más adelante.

    Haga clic en **OK**.

    El Explorador de datos muestra la nueva base de datos y la tabla.

    ![El Explorador de datos de Azure Portal mostrando la nueva base de datos y la colección](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)