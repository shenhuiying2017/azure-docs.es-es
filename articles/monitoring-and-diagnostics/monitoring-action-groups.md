---
title: "Creación y administración de grupos de acciones en Azure Portal | Microsoft Docs"
description: 
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 240f7f3788f6a432bcb1ec3b244bc76e3157445a
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>Creación y administración de grupos de acciones en Azure Portal
## <a name="overview"></a>Información general ##
En este artículo se muestra cómo crear y administrar grupos de acciones en el portal de Azure.

Los grupos de acciones permiten configurar una lista de destinatarios. Luego se pueden aprovechar estos grupos al definir alertas de registro de actividad, lo que garantiza que cuando se activa una alerta de registro de actividad, un grupo de acciones concreto recibe la notificación.

Un grupo de acciones puede tener hasta 10 acciones de cada tipo. Una acción se define mediante la combinación de:

**Nombre:** un identificador único dentro del grupo de acciones.  
**Tipo de acción:** define la acción que se llevará a cabo. Algunas opciones son enviar SMS, enviar correo electrónico o llamar a un webhook.  
**Detalles:** según el tipo de acción, es necesario proporcionar el número de teléfono, la dirección de correo electrónico o el identificador URI de webhook correspondientes.

Puede configurar y obtener información sobre las alertas de notificación de estado del servicio mediante:
* [Portal de Azure](monitoring-action-groups.md)
- [Plantillas de Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>Creación de un grupo de acciones para el portal de Azure ##
1.    En el [portal](https://portal.azure.com), navegue hasta el servicio **Monitor**.

    ![Supervisión](./media/monitoring-action-groups/home-monitor.png)
2.    Haga clic en la opción **Monitor** para abrir la hoja Monitor. En esta hoja se encuentran toda la configuración de supervisión y los datos en una sola vista consolidada. Primero se abre la sección **Registro de actividades** .

3.    Ahora haga clic en la sección **Grupos de acciones**.

    ![Grupo de acciones](./media/monitoring-action-groups/action-groups-blade.png)
4.    Haga clic en el comando del grupo de acciones **Agregar** y rellene los campos.

    ![Add-Action-Group](./media/monitoring-action-groups/add-action-group.png)
5.    Proporcione un **nombre** y un **nombre corto** para el grupo de acciones; se hará referencia al nombre corto en las notificaciones enviadas a este grupo.

      ![Action-Group-Define](./media/monitoring-action-groups/action-group-define.png)

6.    La **suscripción** es aquella en la que se guardará el grupo de acciones. Se rellena automáticamente con la suscripción que usa actualmente.

7.    Elija el **grupo de recursos** para este grupo de acciones.

8.    A continuación, defina una lista de acciones mediante una combinación de:
  1. **Nombre:** un identificador único dentro del grupo de acciones.
  2. **Tipo de acción:** define la acción que se llevará a cabo. Algunas opciones son enviar SMS, enviar correo electrónico o llamar a un webhook.
  3. **Detalles:** según el tipo de acción, es necesario proporcionar el número de teléfono, la dirección de correo electrónico o el identificador URI de webhook correspondientes.

9.    Cuando termine de crear el grupo de acciones, seleccione **Aceptar**.

## <a name="managing-your-action-groups"></a>Administración de los grupos de acción ##
Cuando haya creado un grupo de acciones, estará visible en la sección de grupos de acciones del servicio de Monitor. Seleccione el grupo de acciones que desea administrar; podrá:
* Agregar, editar o quitar destinatarios
-    Eliminar el grupo de acciones.

## <a name="next-steps"></a>Pasos siguientes: ##
Más información sobre el [comportamiento de alertas por SMS](monitoring-sms-alert-behavior.md)  
[Comprender el esquema de webhook de alertas de registro de actividad](monitoring-activity-log-alerts-webhook.md)  
Más información sobre la [limitación de velocidad](monitoring-alerts-rate-limiting.md) en las alertas.  
Obtener una [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprender cómo puede recibir alertas  
Cómo [configurar alertas siempre que se publique una notificación de estado de un servicio](monitoring-activity-log-alerts-on-service-notifications.md)

