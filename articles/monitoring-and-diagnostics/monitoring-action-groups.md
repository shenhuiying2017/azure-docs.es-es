---
title: Creación y administración de grupos de acciones en Azure Portal | Microsoft Docs
description: Obtenga información acerca de cómo crear y administrar grupos de acciones en Azure Portal.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: dukek
ms.openlocfilehash: e3185b8d8ce97ffd04188b2b49a457bd14d5c6c8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creación y administración de grupos de acciones en Azure Portal
## <a name="overview"></a>Información general ##
En este artículo se muestra cómo crear y administrar grupos de acciones en el portal de Azure.

Los grupos de acciones le permiten configurar una lista de acciones. Estos grupos pueden ser utilizados por cada alerta que defina, asegurándose de que las mismas acciones se realizan cada vez que se desencadena la alerta.

Cada acción se compone de las siguientes propiedades:

* **Nombre**: un identificador único dentro del grupo de acciones.  
* **Tipo de acción**: realizar una llamada de voz o enviar un SMS, enviar un correo electrónico, llamar a un webhook, enviar datos a una herramienta ITSM, llamar a una aplicación lógica, enviar una notificación push a la aplicación de Azure o ejecutar un runbook de Automation.
* **Detalles**: el número de teléfono, dirección de correo electrónico o identificador URI del webhook o los detalles de conexión de ITSM.

Para más información sobre el uso de plantillas de Azure Resource Manager para configurar grupos de acciones, consulte [Plantillas de Resource Manager para grupos de acciones](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creación de un grupo de acciones con Azure Portal ##
1. En el [portal](https://portal.azure.com), seleccione **Monitor**. La hoja **Supervisión** consolida todos los valores y datos de supervisión en una vista.

    ![Servicio "Monitor"](./media/monitoring-action-groups/home-monitor.png)
2. En la sección **Configuración**, seleccione **Grupos de acciones**.

    ![Pestaña "Grupos de acciones"](./media/monitoring-action-groups/action-groups-blade.png)
3. Seleccione **Agregar grupo de acciones** y rellene los campos.

    ![Comando "Agregar grupo de acciones"](./media/monitoring-action-groups/add-action-group.png)
4. Escriba un nombre en el cuadro de texto **Nombre del grupo de acciones** y especifique un nombre en el cuadro de texto **Nombre corto**. El nombre corto se utiliza en lugar del nombre completo del grupo de acciones cuando se envían notificaciones mediante este grupo.

      ![Cuadro de diálogo "Agregar grupo de acciones"](./media/monitoring-action-groups/action-group-define.png)

5. El cuadro de texto **Suscripción** se rellena automáticamente con la suscripción actual. La suscripción es aquella en la que se guarda el grupo de acciones.

6. Seleccione el **Grupo de recursos** en el que se guarda el grupo de acciones.

7. Defina una lista de acciones proporcionando los siguientes valores para cada acción:

    a. **Nombre**: escriba un identificador único para esta acción.

    b. **Tipo de acción**: seleccionar correo electrónico, SMS, notificación push, voz, aplicación lógica, webhook, ITSM o runbook de Automation.

    c. **Detalles**: según el tipo de acción, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook, una aplicación de Azure, una conexión de ITSM o un runbook de Automation. Para la acción de ITSM, especifique además **Elemento de trabajo** y otros campos que requiera la herramienta ITSM.

8. Seleccione **Aceptar** para crear el grupo de acciones.

## <a name="action-specific-information"></a>Información específica de la acción
<dl>
<dt>Notificaciones push de la aplicación de Azure</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de aplicación de Azure.</dd>
<dd>En este momento, la acción de aplicación de Azure solo admite alertas de ServiceHealth. Los demás momentos de alerta se omitirán. Consulte el artículo acerca de la [configuración de alertas siempre que se publique una notificación de estado de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>Correo electrónico</dt>
<dd>En un grupo de acciones puede tener hasta 50 acciones de correo electrónico.</dd>
<dd>Consulte el artículo de [información sobre las limitaciones](./monitoring-alerts-rate-limiting.md).</dd>

<dt>ITSM</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de ITSM.</dd>
<dd>La acción de ITSM requiere una conexión de ITSM. Aprenda cómo crear una [conexión de ITSM](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Aplicaciones lógicas</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de aplicación lógica.</dd>

<dt>Runbook</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de runbook.</dd>

<dt>SMS</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de SMS.</dd>
<dd>Consulte el artículo de [información sobre las limitaciones](./monitoring-alerts-rate-limiting.md).</dd>
<dd>Consulte el artículo sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).</dd>

<dt>Voz</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de voz.</dd>
<dd>Consulte el artículo de [información sobre las limitaciones](./monitoring-alerts-rate-limiting.md).</dd>

<dt>Webhook</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de webhook.
<dd>Reintentar la lógica: se volverá a intentar la llamada de webhook un máximo de 3 veces cuando se devuelvan los siguientes códigos de estado HTTP: 408, 429, 503, 504</dd>
</dl>

## <a name="manage-your-action-groups"></a>Administración de los grupos de acciones ##
Después de crear un grupo de acciones, está visible en la sección **Grupos de acciones** de la hoja **Supervisión**. Seleccione el grupo de acciones que desea administrar para:

* Agregar, editar o quitar acciones.
* Eliminar el grupo de acciones.

## <a name="next-steps"></a>Pasos siguientes ##
* Más información sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).  
* [Comprender el esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).  
* Obtenga más información sobre [Conector de ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Más información sobre la [limitación de velocidad](monitoring-alerts-rate-limiting.md) en las alertas.
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
