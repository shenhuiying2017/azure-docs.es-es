---
title: "Recuperación de información de estado de un trabajo del servicio Azure Import/Export | Microsoft Docs"
description: "Descubra cómo obtener información de estado de trabajos del servicio Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Recuperación de la información de estado de un trabajo de Import/Export
Puede llamar a la operación [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) para obtener información sobre trabajos tanto de importación como de exportación. Entre la información devuelta se incluyen los siguientes datos:

-   Estado actual del trabajo.

-   Porcentaje aproximado en que se ha completado cada trabajo.

-   Estado actual de cada unidad.

-   URI de blobs que contienen registros de errores e información de registro detallada (si está habilitada).

En las secciones siguientes se explica la información devuelta por la operación `Get Job`.

## <a name="job-states"></a>Estados de trabajo
En la tabla y el diagrama de estado siguientes se describen los estados por los que pasa un trabajo durante su ciclo de vida. Se puede determinar el estado actual del trabajo llamando a la operación `Get Job`.

![Estados de trabajo](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "Estados de trabajo")

En la tabla siguiente se describe cada estado por el que puede pasar un trabajo.

|Estado de trabajo|Descripción|
|---------------|-----------------|
|`Creating`|Después de llamar a la operación Put Job, se crea un trabajo y su estado se establece en `Creating`. Mientras el trabajo se encuentra en el estado `Creating`, el servicio Import/Export asume que las unidades no se han enviado al centro de datos. Un trabajo puede permanecer en el estado `Creating` durante dos semanas, después de lo cual el servicio lo eliminará automáticamente.<br /><br /> Si se llama a la operación Update Job Properties mientras el trabajo está en el estado `Creating`, el trabajo permanece en el estado `Creating` y el intervalo de tiempo de espera se restablece en dos semanas.|
|`Shipping`|Después de enviar el paquete, debe llamar a la operación Update Job Properties para actualizar el estado del trabajo a `Shipping`. El estado de envío solo se puede restablecer si se ha definido `DeliveryPackage` (el transportista y el número de seguimiento) y las propiedades de `ReturnAddress` para el trabajo.<br /><br /> El trabajo permanecerá en el estado Shipping durante un máximo de dos semanas. Si después de dos semanas no se han recibido las unidades, se notificará a los operadores del servicio Import/Export.|
|`Received`|Una vez recibidas todas las unidades en el centro de datos, el estado del trabajo se establecerá en Received.|
|`Transferring`|Cuando se hayan recibido las unidades en el centro de datos y al menos una unidad haya iniciado el procesamiento, el estado del trabajo se establecerá en `Transferring`. Consulte la sección `Drive States` para obtener información detallada.|
|`Packaging`|Cuando todas las unidades hayan terminado el procesamiento, el trabajo pasará al estado `Packaging` hasta que las unidades se envíen de vuelta al cliente.|
|`Completed`|Cuando todas las unidades se hayan devuelto al cliente, si el trabajo se ha completado sin errores, se establecerá en el estado `Completed`. El trabajo se eliminará automáticamente después de 90 días en el estado `Completed`.|
|`Closed`|Cuando todas las unidades se hayan devuelto al cliente, si se ha producido algún error durante el procesamiento del trabajo, este se establecerá en el estado `Closed`. El trabajo se eliminará automáticamente después de 90 días en el estado `Closed`.|

Un trabajo solo se puede cancelar con determinados estados. Un trabajo cancelado omite el paso de copia de datos, pero sigue las mismas transiciones de estado que un trabajo no cancelado.

En la tabla siguiente se describen los errores que pueden aparecer para cada estado del trabajo, así como el efecto en el trabajo cuando se produce un error.

|Estado de trabajo|Evento|Resolución y pasos siguientes|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Se han recibido una o varias unidades para un trabajo, pero este no se encuentra en el estado `Shipping` o no hay ningún registro del trabajo en el servicio.|El equipo de operaciones del servicio Import/Export intentará ponerse en contacto con el cliente para crear o actualizar el trabajo con la información necesaria para proseguir el trabajo.<br /><br /> Si el equipo de operaciones no puede ponerse en contacto con el cliente en dos semanas, intentará devolver las unidades.<br /><br /> En caso de que no se puedan devolver las unidades y el cliente esté ilocalizable, las unidades se destruirán en 90 días de forma segura.<br /><br /> Tenga en cuenta que un trabajo no puede procesarse hasta que se actualice su estado a `Shipping`.|
|`Shipping`|El paquete de un trabajo no se ha recibido en más de dos semanas.|El equipo de operaciones notificará al cliente de que falta el paquete. En función de la respuesta del cliente, el equipo de operaciones ampliará el intervalo de espera para que llegue el paquete o cancelará el trabajo.<br /><br /> En caso de que no sea posible contactar al cliente o de que no responda en el plazo de 30 días, el equipo de operaciones hará lo necesario para pasar el trabajo del estado `Shipping` directamente al estado `Closed`.|
|`Completed/Closed`|Las unidades nunca han llegado a la dirección de devolución o han resultado dañadas durante el envío (solo aplicable a trabajos de exportación).|Si las unidades no llegan a la dirección de devolución, el cliente primero debe llamar a la operación Get Job o comprobar el estado del trabajo en el portal para asegurarse de que las unidades se han enviado. Si se han enviado las unidades, el cliente deberá ponerse en contacto con el transportista para intentar localizar las unidades.<br /><br /> Si las unidades han resultado dañadas durante el envío, el cliente puede solicitar otro trabajo de exportación o descargar los blobs que faltan.|
|`Transferring/Packaging`|El trabajo no tiene dirección de devolución o es incorrecta.|El equipo de operaciones se dirigirá a la persona de contacto del trabajo para obtener la dirección correcta.<br /><br /> En caso de que no se haya podido contactar al cliente, las unidades se destruirán en 90 días de forma segura.|
|`Creating / Shipping/ Transferring`|El paquete de envío incluye una unidad que no figura en la lista de unidades que se van a importar.|Las unidades adicionales no se procesarán y se devolverán al cliente una vez completado el trabajo.|

## <a name="drive-states"></a>Estados de unidad
En la tabla y el diagrama siguiente se describe el ciclo de vida de una unidad individual durante su transición a través de un trabajo de importación o exportación. Puede obtener el estado actual de la unidad si llama a la operación `Get Job` e inspecciona el elemento `State` de la propiedad `DriveList`.

![Estados de unidad](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "Estados de unidad")

En la tabla siguiente se describe cada estado por el que puede pasar una unidad.

|Estado de la unidad|Descripción|
|-----------------|-----------------|
|`Specified`|Para un trabajo de importación, cuando se crea el trabajo con la operación Put Job, el estado inicial de una unidad es `Specified`. Para un trabajo de exportación, ya que no se especifica ninguna unidad cuando se crea el trabajo, el estado inicial de la unidad es `Received`.|
|`Received`|La unidad cambia al estado `Received` cuando el operador del servicio Import/Export ha procesado las unidades recibidas del transportista para un trabajo de importación. Para un trabajo de exportación, el estado inicial de la unidad es `Received`.|
|`NeverReceived`|La unidad pasará al estado `NeverReceived` si llega un paquete para un trabajo que no contiene la unidad. Una unidad también puede cambiar a este estado si han transcurrido dos semanas desde que el servicio recibió la información de envío pero el paquete aún no ha llegado al centro de datos.|
|`Transferring`|Una unidad pasará al estado `Transferring` cuando el servicio empiece a transferir datos de la unidad a Windows Azure Storage.|
|`Completed`|Una unidad pasará al estado `Completed` cuando el servicio haya transferido correctamente todos los datos sin errores.|
|`CompletedMoreInfo`|Una unidad de disco pasará al estado `CompletedMoreInfo` cuando el servicio encuentre algún problema mientras copia datos en la unidad o desde ella. La información puede incluir errores, advertencias o mensajes informativos por sobrescribir blobs.|
|`ShippedBack`|La unidad de disco pasará al estado `ShippedBack` cuando se haya enviado desde el centro de datos a la dirección de devolución.|

En la tabla siguiente se describen los estados de error de la unidad y las medidas tomadas para cada estado.

|Estado de la unidad|Evento|Resolución y paso siguiente|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Una unidad que está marcada como `NeverReceived` (porque no se recibió como parte del envío del trabajo) llega en otro envío.|El equipo de operaciones cambiará la unidad al estado `Received`.|
|`N/A`|Una unidad que no forma parte de ningún trabajo llega al centro de datos como parte de otro trabajo.|La unidad se marcará como unidad adicional y se devolverá al cliente cuando se complete el trabajo asociado al paquete original.|

## <a name="faulted-states"></a>Estados de error
Cuando un trabajo o una unidad no pueda progresar normalmente a través de su ciclo de vida esperado, el trabajo o la unidad pasarán al estado `Faulted`. En ese momento, el equipo de operaciones se pondrá en contacto con el cliente por correo electrónico o por teléfono. Una vez resuelto el problema, la unidad o el trabajo con errores cambiarán del estado `Faulted` al estado adecuado.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)
