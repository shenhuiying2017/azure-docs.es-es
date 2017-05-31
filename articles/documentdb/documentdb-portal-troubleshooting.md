---
title: "Solución de problemas de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información para resolver problemas de Azure Cosmos DB en Azure Portal."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 36ad9bf4-2617-4347-ba29-edebf62fc3ec
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 307b1d0edc1aa76654cc96c71cccc39c2a11d40c
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-portal-troubleshooting-tips"></a>Sugerencias para solucionar problemas del portal de Azure Cosmos DB
En este artículo se describe cómo solucionar problemas de Azure Cosmos DB en Azure Portal. 

## <a name="resources-are-missing"></a>Faltan recursos
**Síntoma**: faltan colecciones o bases de datos en las hojas del portal.

**Solución**: reduzca el uso de la aplicación para trabajar con la cuota de rendimiento máximo de la colección. 

**Explicación**: el portal es una aplicación como cualquier otra, ya que realiza llamadas a la colección y la base de datos de Cosmos DB. Si las solicitudes están limitadas actualmente a causa de llamadas que se realizan desde una aplicación independiente, el portal también quedará limitado, con lo que recursos no se mostrarán en el portal. Para resolver el problema, solucione la causa del elevado uso de rendimiento y, después, actualice la hoja del portal. Se puede encontrar información sobre cómo medir y reducir el uso de rendimiento en la sección de [rendimiento](documentdb-performance-tips.md#throughput) del artículo [Sugerencias de rendimiento](documentdb-performance-tips.md).

## <a name="pages-or-blades-wont-load"></a>No se cargan ni páginas ni hojas
**Síntoma**: las páginas y hojas del portal no se muestran.

**Solución**: reduzca el uso de la aplicación para trabajar con la cuota de rendimiento máximo de la colección. 

**Explicación**: el portal es una aplicación como cualquier otra, ya que realiza llamadas a la colección y la base de datos de Cosmos DB. Si las solicitudes están limitadas actualmente a causa de llamadas que se realizan desde una aplicación independiente, el portal también quedará limitado, con lo que recursos no se mostrarán en el portal. Para resolver el problema, solucione la causa del elevado uso de rendimiento y, después, actualice la hoja del portal. Se puede encontrar información sobre cómo medir y reducir el uso de rendimiento en la sección de [rendimiento](documentdb-performance-tips.md#throughput) del artículo [Sugerencias de rendimiento](documentdb-performance-tips.md).

## <a name="add-collection-button-is-disabled"></a>El botón Agregar colección está deshabilitado
**Síntoma**: en hoja Base de datos, el botón **Agregar colección** está deshabilitado.

**Explicación**: si su suscripción de Azure está asociada con créditos de ventajas, como créditos gratuitos ofrecidos desde una suscripción de MSDN, y ha utilizado todos sus créditos del mes, no se pueden crear más colecciones en Cosmos DB.

**Solución**: quite el límite de gasto de la cuenta.

1. En Azure Portal, en la barra de accesos, haga clic en **Suscripciones**, haga clic en la suscripción asociada con la base de datos de Cosmos DB y, luego, en la hoja **Suscripción**, haga clic en **Administrar**. 
    ![Azure Cosmos DB ofrece varios modelos de coherencia bien definida (progresivos) entre los que elegir](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. En la nueva ventana del explorador, verá que no quedan créditos. Haga clic en el botón **Quitar el límite del gasto** para quitar el gasto solo del periodo de facturación actual o de manera indefinida. Después, complete el Asistente para agregar o confirmar la información de tarjeta de crédito. 
    ![Azure Cosmos DB ofrece varios modelos de coherencia bien definida (progresivos) entre los que elegir](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## <a name="query-explorer-completes-with-errors"></a>El Explorador de consultas termina con errores
Consulte la sección de [solución de problemas del Explorador de consultas](documentdb-query-collections-query-explorer.md#troubleshoot).

## <a name="no-data-available-in-monitoring-tiles"></a>No hay datos disponibles en los iconos de supervisión
Consulte la sección de [solución de problemas de los iconos de supervisión](documentdb-monitor-accounts.md#troubleshooting).

## <a name="no-documents-returned-in-document-explorer"></a>No se devuelven documentos en el Explorador de documentos
Consulte la sección de [solución de problemas del Explorador de documentos](documentdb-view-json-document-explorer.md#troubleshoot).

## <a name="next-steps"></a>Pasos siguientes
Si sigue experimentando problemas en el portal, envíe un correo electrónico [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para solicitar asistencia, o bien rellene una solicitud de asistencia en el portal haciendo clic en **Examinar**, **Ayuda y soporte técnico** y, después, en **Crear una solicitud de soporte técnico**.


