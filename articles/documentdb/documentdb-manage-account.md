---
title: "Administrar una cuenta de DocumentDB a través de Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo administración su cuenta de DocumentDB mediante el Portal de Azure. Guía sobre cómo usar el Portal de Azure para ver, copiar, eliminar y obtener acceso a cuentas."
keywords: Portal de Azure, documentdb, azure, Microsoft azure
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 3dbf57923e104715d34d1131d16acb6bc6c51637
ms.openlocfilehash: ee1668dce71a1f5d7b91e839670b69c9527c4892


---
# <a name="how-to-manage-a-documentdb-account"></a>Administración de una cuenta de DocumentDB
Aprenda a establecer la coherencia global, trabajar con claves y eliminar una cuenta de DocumentDB en el Portal de Azure.

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>Administración de la configuración de coherencia de DocumentDB
La selección del nivel de coherencia adecuado depende de la semántica de su aplicación. Para familiarizarse con los niveles de coherencia disponibles en DocumentDB, consulte [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB][consistency]. DocumentDB ofrece garantías de coherencia, disponibilidad y rendimiento, en cada nivel de coherencia disponible para la cuenta de base de datos. La configuración de la cuenta de base de datos con un nivel de coherencia alto requiere que los datos estén confinados en una sola región de Azure y que no estén disponibles globalmente. Por otro lado, los niveles de coherencia moderados: uso vinculado, sesión o posible, le permiten asociar un número cualquiera de regiones de Azure con su cuenta de base de datos. Estos sencillos pasos le muestran cómo seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Para especificar la coherencia predeterminada de una cuenta de DocumentDB:
1. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.
2. En la hoja Cuenta, haga clic en **Coherencia predeterminada**.
3. En la hoja **Coherencia predeterminada**, seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.
    ![Sesión de coherencia predeterminada][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>Visualización, copia y regeneración de las claves de acceso
Cuando se crea una cuenta de DocumentDB, el servicio genera dos claves de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de DocumentDB. Al proporcionar dos claves de acceso, DocumentDB permite regenerar las claves sin interrupción en la cuenta de esta base de datos. 

En el [Azure Portal](https://portal.azure.com/), acceda a la sección **Claves** del menú de recursos de la hoja **Cuenta de DocumentDB** para ver, copiar y regenerar las claves de acceso que se usan para obtener acceso a la cuenta de DocumentDB.

![Captura de pantalla del Portal de Azure, hoja Claves](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> La hoja **Claves** también incluye cadenas de conexión principal y secundaria que se pueden utilizar para conectarse a su cuenta desde la [Herramienta de migración de datos](documentdb-import-data.md).
> 
> 

Las claves de solo lectura también están disponibles en esta hoja. Las lecturas y consultas son operaciones de solo lectura, mientras que las operaciones de creación, eliminación y reemplazo no lo son.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copia de una clave de acceso en el Portal de Azure
En la hoja **Claves**, haga clic en el botón **Copiar**, a la derecha de la clave que quiere copiar.

![Visualización y copia de una clave de acceso en el Portal de Azure, hoja Claves](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Regenerar las claves de acceso
Cambie las claves de acceso de la cuenta de DocumentDB de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de DocumentDB, de modo que puede usar una clave de acceso mientras regenera la otra.

> [!WARNING]
> La regeneración de las claves de acceso afecta a las aplicaciones que dependen de una clave actual. Todos los clientes que usan la clave de acceso para obtener acceso a la cuenta de DocumentDB deben estar actualizados para usar la nueva clave.
> 
> 

Si cuenta con aplicaciones o servicios en la nube que usan la cuenta de DocumentDB y regenera las claves, perderá las conexiones. Para evitarlo, sustituya las claves. Los siguientes pasos describen el proceso de distribución de las claves.

1. Actualice la clave de acceso en el código de aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de DocumentDB.
2. Vuelva a generar la clave de acceso primaria para su cuenta de DocumentDB. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.
3. En la hoja **Cuenta de DocumentDB**, haga clic en **Claves**.
4. En la hoja **Claves**, haga clic en el botón para regenerar y en **Aceptar** para confirmar que quiere generar una clave nueva.
    ![Regenerar las claves de acceso](./media/documentdb-manage-account/regenerate-keys.png)
5. Tras comprobar que la clave nueva está disponible para su uso (aproximadamente 5 minutos después de la regeneración), actualice la clave de acceso en el código de aplicación para hacer referencia a la nueva clave de acceso principal.
6. Vuelva a generar la clave de acceso secundaria.
   
    ![Regenerar las claves de acceso](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Pueden pasar varios minutos antes de poder obtener acceso a la cuenta de DocumentDB con la clave que acaba de crear.
> 
> 

## <a name="get-the--connection-string"></a>Obtener la cadena de conexión
Realice lo siguiente para recuperar la cadena de conexión: 

1. En el [Azure Portal](https://portal.azure.com), obtenga acceso a su cuenta de DocumentDB.
2. En el menú de recursos, haga clic en **Claves**.
3. Haga clic en el botón **Copiar** situado junto a los cuadros **Cadena de conexión principal** o **Cadena de conexión secundaria**. 

Si está utilizando la cadena de conexión en la [herramienta de migración de base de datos de DocumentDB](documentdb-import-data.md), anexe el nombre de la base de datos al final de la cadena de conexión. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> Eliminación de una cuenta de DocumentDB
Para quitar una cuenta de DocumentDB que ya no usa en Azure Portal, haga clic con el botón derecho en el nombre de cuenta y haga clic en **Eliminar cuenta**.

![Eliminación de una cuenta de DocumentDB en el Portal de Azure](./media/documentdb-manage-account/deleteaccount.png)

1. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a la cuenta de DocumentDB que quiere eliminar.
2. En la hoja **Cuenta de DocumentDB**, haga clic en la cuenta y, después, en **Eliminar cuenta**. 
3. En la hoja de confirmación que aparece, escriba el nombre de la cuenta de DocumentDB para confirmar que quiere eliminarla.
4. Haga clic en el botón **Eliminar** .

![Eliminación de una cuenta de DocumentDB en el Portal de Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>Pasos siguientes
Descubra cómo [dar sus primeros pasos con una cuenta de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Feb17_HO3-->


