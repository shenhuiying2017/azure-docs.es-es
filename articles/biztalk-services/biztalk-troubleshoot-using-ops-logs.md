---
title: "Solución de problemas de BizTalk Services mediante registros de operaciones | Microsoft Docs"
description: Solucione los problemas de los Servicios de BizTalk con el uso de registros de operaciones. MABS, WABS
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: c0c83361f94ffd9c30d7fcc551ff4b85ad7d6fa5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Servicios de BizTalk: solución de problemas mediante registros de operaciones

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

## <a name="what-are-the-operation-logs"></a>Qué son los registros de operaciones
Registros de operaciones es una característica de los Servicios de administración, disponible en el Portal de Azure clásico, que permite consultar registros históricos realizados en los servicios de Azure, como Servicios de BizTalk. Esta característica le permite consultar datos históricos relacionados con las operaciones de administración en su suscripción a Servicios de BizTalk de hasta 180 días de antigüedad.

> [!NOTE]
> Esta característica únicamente captura registros para operaciones de administración de Servicios de BizTalk, como cuándo se ha iniciado el servicio, una copia de seguridad, y así sucesivamente. Se realiza un seguimiento de dichas operaciones independientemente de si se realizan desde el Portal de Azure clásico o mediante la [API de REST de Servicios de BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obtener una lista completa de las operaciones que se siguen mediante los Servicios de administración, consulte [Seguimiento de operaciones mediante Servicios de administración de Azure](#bizops).<br/><br/>
> No captura los registros de actividades relacionadas con el tiempo de ejecución de los Servicios de BizTalk (como mensajes procesados por puentes, etc.). Para consultar estos registros, debe utilizar la vista de seguimiento desde el portal de Servicios de BizTalk. Para obtener más información, consulte [Mensajes de seguimiento](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Consulta de los registros de operaciones de los Servicios BizTalk
1. En el Portal de Azure clásico, seleccione **Servicios de administración** y, después, seleccione la pestaña **Registros de operaciones**.
2. Puede filtrar los registros en función de diferentes parámetros, como suscripción, intervalo de fechas, tipo de servicio (por ejemplo, Servicios de BizTalk), nombre de servicio o estado (de la operación, por ejemplo, Succeeded, Failed).
3. Seleccione la marca de verificación para consultar la lista filtrada. En la imagen siguiente se muestran las actividades relacionadas con testbiztalkservice: ![Ver registros de operaciones][ViewLogs]. 
4. Para obtener más información sobre una operación específica, seleccione la fila y haga clic en **Detalles** en la barra de tareas situada en la parte inferior.

## <a name="bizops"></a>Seguimiento de operaciones mediante Servicios de administración de Azure
En la tabla siguiente se enumeran las operaciones cuyo seguimiento se realiza utilizando Servicios de administración de Azure.

| Nombre de la operación | Tarea |
| --- | --- |
| CreateBizTalkService |Operación para crear un nuevo Servicio BizTalk |
| DeleteBizTalkService |Operación para eliminar un Servicio BizTalk |
| RestartBizTalkService |Operación para reiniciar un Servicio BizTalk |
| StartBizTalkService |Operación para iniciar un Servicio BizTalk |
| StopBizTalkService |Operación para detener un Servicio BizTalk |
| DisableBizTalkService |Operación para deshabilitar un Servicio BizTalk |
| EnableBizTalkService |Operación para habilitar un Servicio BizTalk |
| BackupBizTalkService |Operación para hacer una copia de seguridad de un Servicio BizTalk |
| RestoreBizTalkService |Operación para restaurar un Servicio BizTalk a partir de una copia de seguridad especificada |
| SuspendBizTalkService |Operación para suspender un Servicio BizTalk |
| ResumeBizTalkService |Operación para reanudar un Servicio BizTalk |
| ScaleBizTalkService |Operación para escalar o reducir verticalmente un Servicio BizTalk |
| ConfigUpdateBizTalkService |Operación para actualizar la configuración de un Servicio BizTalk |
| ServiceUpdateBizTalkService |Operación para actualizar o degradar un Servicio BizTalk a una versión diferente |
| PurgeBackupBizTalkService |Operación para purgar copias de seguridad del Servicio BizTalk fuera el período de retención |

## <a name="see-also"></a>Otras referencias
* [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restauración del servicio de BizTalk a partir de una copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Servicios de BizTalk: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Servicios de BizTalk: limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Servicios de BizTalk: nombre del emisor y clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

