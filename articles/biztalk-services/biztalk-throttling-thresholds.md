---
title: "Información sobre la limitación en BizTalk Services | Microsoft Docs"
description: "Obtenga información acerca de los umbrales de limitación y comportamientos en tiempo de ejecución resultantes para BizTalk Services. La limitación se basa en el uso de la memoria y el número de mensajes. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 39fc5ef36bb581c3a81c9948fda048f6cb75eb7e
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: limitaciones

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services implementa limitaciones del servicio según dos condiciones: uso de memoria y el número de mensajes simultáneos que se procesan. En este tema se enumeran los umbrales de limitación y se describe el comportamiento del tiempo de ejecución cuando se produce una condición de limitación.

## <a name="throttling-thresholds"></a>Umbrales de limitación
La siguiente tabla muestra los orígenes y umbrales de limitación:

|  | Description | Umbral bajo | Umbral alto |
| --- | --- | --- | --- |
| Memoria |% de memoria total del sistema disponible/PageFileBytes. <p><p>La cantidad total disponible de PageFileBytes es de aproximadamente 2 veces la memoria RAM del sistema. |60% |70% |
| Procesamiento de mensajes |Número de mensajes que se procesan simultáneamente |40 * número de núcleos |100 * número de núcleos |

Cuando se alcanza un umbral alto, Azure BizTalk Services empieza a limitarse. La limitación se detiene cuando se alcanza el umbral bajo. Por ejemplo, el servicio está utilizando un 65 % de la memoria del sistema. En esta situación, el servicio no se limita. El servicio empieza a usar un 70 % de la memoria del sistema. En esta situación, el servicio se limita y sigue limitándose hasta que el servicio utiliza un 60 % de la memoria del sistema (umbral bajo).

Azure BizTalk Services realizan un seguimiento del estado de las limitaciones (estado normal frente a estado limitado) y de la duración de la limitación.

## <a name="runtime-behavior"></a>Comportamiento del tiempo de ejecución
Cuando Azure BizTalk Services entran en un estado de limitación, ocurre lo siguiente:

* La limitación es por instancia de rol. Por ejemplo:<br/>
  RoleInstanceA se está limitando. RoleInstanceB no se está limitando. En esta situación, los mensajes en RoleInstanceB se procesan según lo esperado. Los mensajes en RoleInstanceA se descartan y producen el siguiente error:<br/><br/>
  **El servidor está ocupado. Vuelva a intentarlo.**<br/><br/>
* Ningún origen de extracción sondea ni descarga un mensaje. Por ejemplo,<br/>
  una canalización extrae los mensajes desde un origen FTP externo. La instancia de rol que realiza la extracción entra en un estado de limitación. En esta situación, la canalización deja de descargar mensajes adicionales hasta que la instancia de rol detiene la limitación.
* Se envía una respuesta al cliente para que el cliente pueda volver a enviar el mensaje.
* Debe esperar a que se resuelva la limitación. Específicamente, debe esperar hasta que se alcance el umbral bajo.

## <a name="important-notes"></a>Notas importantes
* La limitación no se puede deshabilitar.
* Los umbrales de limitación no se pueden modificar.
* La limitación está implementada en todo el sistema.
* El servidor de Azure SQL Database también tiene limitaciones integradas.

## <a name="additional-azure-biztalk-services-topics"></a>Otros temas acerca de Azure BizTalk Services
* [Instalación del SDK de Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriales: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [¿Cómo puedo comenzar a utilizar el SDK de Azure BizTalk Services?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Otras referencias
* [BizTalk Services: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: copias de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: nombre del emisor y clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

