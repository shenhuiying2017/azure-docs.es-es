---
title: Solución de problemas de DocumentDB en el Portal de Azure | Microsoft Docs
description: Obtenga información para resolver problemas de DocumentDB en el Portal de Azure.
services: documentdb
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: monicar

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: mimig

---
# Sugerencias para solucionar problemas del Portal de Azure DocumentDB
En este artículo se describe cómo solucionar problemas de DocumentDB en el Portal de Azure.

## Faltan recursos
**Síntoma**: faltan colecciones o bases de datos en las hojas del portal.

**Solución**: reduzca el uso de la aplicación para trabajar con la cuota de rendimiento máximo de la colección.

**Explicación**: el portal es una aplicación como cualquier otra, ya que realiza llamadas a la colección y la base de datos de DocumentDB. Si las solicitudes están limitadas actualmente a causa de llamadas que se realizan desde una aplicación independiente, el portal también quedará limitado, con lo que recursos no se mostrarán en el portal. Para resolver el problema, solucione la causa del elevado uso de rendimiento y, después, actualice la hoja del portal. Se puede encontrar información sobre cómo medir y reducir el uso de rendimiento en la sección de [rendimiento](documentdb-performance-tips.md#throughput) del artículo [Sugerencias de rendimiento](documentdb-performance-tips.md).

## No se cargan ni páginas ni hojas
**Síntoma**: las páginas y hojas del portal no se muestran.

**Solución**: reduzca el uso de la aplicación para trabajar con la cuota de rendimiento máximo de la colección.

**Explicación**: el portal es una aplicación como cualquier otra, ya que realiza llamadas a la colección y la base de datos de DocumentDB. Si las solicitudes están limitadas actualmente a causa de llamadas que se realizan desde una aplicación independiente, el portal también quedará limitado, con lo que recursos no se mostrarán en el portal. Para resolver el problema, solucione la causa del elevado uso de rendimiento y, después, actualice la hoja del portal. Se puede encontrar información sobre cómo medir y reducir el uso de rendimiento en la sección de [rendimiento](documentdb-performance-tips.md#throughput) del artículo [Sugerencias de rendimiento](documentdb-performance-tips.md).

## El botón Agregar colección está deshabilitado
**Síntoma**: en hoja de Base de datos, el botón **Agregar colección** está deshabilitado.

**Explicación**: si su suscripción de Azure está asociada con créditos de ventajas, como créditos gratuitos ofrecidos desde una suscripción de MSDN, y ha utilizado todos sus créditos del mes, no se pueden crear más colecciones en DocumentDB.

**Solución**: quite el límite de gasto de la cuenta.

1. En el Portal de Azure, en la barra de accesos, haga clic en **Suscripciones**, haga clic en la suscripción asociada con la base de datos de DocumentDB y, luego, en la hoja **Suscripción**, haga clic en **Administrar**. ![DocumentDB ofrece varios modelos de coherencia bien definidos entre los que elegir](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. En la nueva ventana del explorador, verá que no quedan créditos. Haga clic en el botón **Quitar el límite del gasto** para quitar el gasto solo del periodo de facturación actual o de manera indefinida. Después, complete el Asistente para agregar o confirmar la información de tarjeta de crédito. ![DocumentDB ofrece varios modelos de coherencia bien definidos entre los que elegir](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## El Explorador de consultas termina con errores
Consulte la sección de [solución de problemas del Explorador de consultas](documentdb-query-collections-query-explorer.md#troubleshoot).

## No hay datos disponibles en los iconos de supervisión
Consulte la sección de [solución de problemas de los iconos de supervisión](documentdb-monitor-accounts.md#troubleshooting).

## No se devuelven documentos en el Explorador de documentos
Consulte la sección de [solución de problemas del Explorador de documentos](documentdb-view-json-document-explorer.md#troubleshoot).

## Pasos siguientes
Si sigue experimentando problemas en el portal, envíe un correo electrónico [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para solicitar asistencia, o bien rellene una solicitud de asistencia en el portal haciendo clic en **Examinar**, **Ayuda y soporte técnico** y, después, en **Crear una solicitud de soporte técnico**.

<!---HONumber=AcomDC_0831_2016-->