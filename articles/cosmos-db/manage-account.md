---
title: "Administrar una cuenta de Azure Cosmos DB a través de Azure Portal | Microsoft Docs"
description: "Obtenga información sobre la administración de su cuenta de Azure Cosmos DB mediante Azure Portal. Guía sobre cómo usar Azure Portal para ver, copiar, eliminar y obtener acceso a cuentas."
keywords: Azure Portal, documentdb, azure, Microsoft azure
services: cosmos-db
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: kirillg
ms.openlocfilehash: 86b43b312bf7ce52ab75855424cc5db473245159
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Administración de una cuenta de Azure Cosmos DB
Aprenda a establecer la coherencia global, trabajar con claves y eliminar una cuenta de Azure Cosmos DB en Azure Portal.

## <a id="consistency"></a>Administración de la configuración de coherencia de Azure Cosmos DB
La selección del nivel de coherencia adecuado depende de la semántica de su aplicación. Si quiere familiarizarse con los niveles de coherencia disponibles en Azure Cosmos DB, consulte [Usar los niveles de coherencia para maximizar la disponibilidad y el rendimiento en Azure Cosmos DB][consistency]. Azure Cosmos DB ofrece garantías de coherencia, disponibilidad y rendimiento, en cada nivel de coherencia disponible para la cuenta de base de datos. La configuración de la cuenta de base de datos con un nivel de coherencia alto requiere que los datos estén confinados en una sola región de Azure y que no estén disponibles globalmente. Por otro lado, están los niveles de coherencia moderados: de obsolescencia limitada, de sesión o eventuales. Estos niveles le permiten asociar un número cualquiera de las regiones de Azure con su cuenta de base de datos. Estos sencillos pasos le muestran cómo seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Para especificar la coherencia predeterminada de una cuenta de Azure Cosmos DB
1. Vaya a [Azure Portal](https://portal.azure.com/) y acceda a su cuenta de Azure Cosmos DB.
2. En la página Cuenta, haga clic en **Coherencia predeterminada**.
3. En la página **Coherencia predeterminada**, seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.
    ![Sesión de coherencia predeterminada][5]

## <a id="keys"></a>Visualización, copia y regeneración de las claves de acceso
Cuando se crea una cuenta de Azure Cosmos DB, el servicio genera dos claves de acceso maestras que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de Azure Cosmos DB. Al proporcionar dos claves de acceso, Azure Cosmos DB permite regenerar las claves sin interrupción en la cuenta de Azure Cosmos DB. 

En [Azure Portal](https://portal.azure.com/), acceda a la página **Claves** del menú de recursos de la página **Cuenta de Azure Cosmos DB** para ver, copiar y regenerar las claves de acceso que se usan para obtener acceso a la cuenta de Azure Cosmos DB.

![Captura de pantalla de Azure Portal, página Claves](./media/manage-account/keys.png)

> [!NOTE]
> Igualmente, la página **Claves** también incluye cadenas de conexión principal y secundaria que se pueden utilizar para conectarse a la cuenta desde la [Herramienta de migración de datos](import-data.md).
> 
> 

Las claves de solo lectura también están disponibles en esta página. Las lecturas y consultas son operaciones de solo lectura, mientras que las operaciones de creación, eliminación y reemplazo no lo son.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiar una clave de acceso en Azure Portal
En la página **Claves**, haga clic en el botón **Copiar** que se encuentra a la derecha de la clave que quiere copiar.

![Visualización y copia de una clave de acceso en Azure Portal, página Claves](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Regenerar las claves de acceso
Cambie periódicamente las claves de acceso de la cuenta de Azure Cosmos DB para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de Azure Cosmos DB, de modo que puede usar una clave de acceso mientras regenera la otra.

> [!WARNING]
> La regeneración de las claves de acceso afecta a las aplicaciones que dependen de una clave actual. Todos los clientes que usan la clave de acceso para obtener acceso a la cuenta de Azure Cosmos DB deben estar actualizados para usar la nueva clave.
> 
> 

Si cuenta con aplicaciones o servicios en la nube que usan la cuenta de Azure Cosmos DB y regenera las claves, perderá las conexiones. Para evitarlo, distribuya las claves. Los siguientes pasos describen el proceso de distribución de las claves.

1. Actualice la clave de acceso en el código de aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de Azure Cosmos DB.
2. Vuelva a generar la clave de acceso primaria para su cuenta de Azure Cosmos DB. Vaya a [Azure Portal](https://portal.azure.com/) y acceda a su cuenta de Azure Cosmos DB.
3. En la página **Cuenta de Azure Cosmos DB**, haga clic en **Claves**.
4. En la página **Claves**, haga clic en el botón Regenerar y en **Aceptar** para confirmar que quiere generar una clave nueva.
    ![Regenerar las claves de acceso](./media/manage-account/regenerate-keys.png)
5. Tras comprobar que la clave nueva está disponible para su uso (tarda aproximadamente cinco minutos después de la regeneración), actualice la clave de acceso en el código de aplicación para que haga referencia a la nueva clave de acceso principal.
6. Vuelva a generar la clave de acceso secundaria.
   
    ![Regenerar las claves de acceso](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Pueden pasar varios minutos antes de poder obtener acceso a la cuenta de Azure Cosmos DB con la clave que acaba de crear.
> 
> 

## <a name="get-the--connection-string"></a>Obtener la cadena de conexión
Realice lo siguiente para recuperar la cadena de conexión: 

1. Vaya a [Azure Portal](https://portal.azure.com) y acceda a su cuenta de Azure Cosmos DB.
2. En el menú de recursos, haga clic en **Claves**.
3. Haga clic en el botón **Copiar** situado junto a los cuadros **Cadena de conexión principal** o **Cadena de conexión secundaria**. 

Si está utilizando la cadena de conexión en la [herramienta de migración de base de datos de Azure Cosmos DB](import-data.md), anexe el nombre de la base de datos al final de la cadena de conexión. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Eliminar una cuenta de Azure Cosmos DB
Para quitar una cuenta de Azure Cosmos DB que ya no use de Azure Portal, haga clic con el botón derecho en el nombre de la cuenta y haga clic en **Eliminar cuenta**.

![Cómo eliminar una cuenta de Azure Cosmos DB de Azure Portal](./media/manage-account/deleteaccount.png)

1. En [Azure Portal](https://portal.azure.com/), acceda a la cuenta de Azure Cosmos DB que quiere eliminar.
2. En la página **Cuenta de Azure Cosmos DB**, haga clic con el botón derecho en la cuenta y, a continuación, en **Eliminar cuenta**. 
3. En la página de confirmación que aparece, escriba el nombre de la cuenta de Azure Cosmos DB para confirmar que quiere eliminarla.
4. Haga clic en el botón **Eliminar** .

![Cómo eliminar una cuenta de Azure Cosmos DB de Azure Portal](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Pasos siguientes
Descubra cómo [comenzar a usar una cuenta de Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
