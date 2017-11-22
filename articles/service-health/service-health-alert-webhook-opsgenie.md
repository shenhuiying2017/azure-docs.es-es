---
title: "Configuración de alertas de estado del servicio de Azure con OpsGenie | Microsoft Docs"
description: Obtenga notificaciones personalizadas sobre los eventos del estado de servicio en la instancia de OpsGenie.
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: a2309a050225dd0d7ac8d5b3e4c762bc5bcb25c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Configuración de alertas de estado del servicio con OpsGenie

Este artículo muestra cómo configurar las alertas de estado del servicio de Azure con OpsGenie mediante un webhook. Mediante el uso de la integración del estado del servicio de Azure de [OpsGenie](https://www.opsgenie.com/) puede reenviar alertas de estado del servicio de Azure a OpsGenie. OpsGenie puede determinar las personas adecuadas a las que enviar la notificación según las programaciones de entrega mediante correo electrónico, mensajes de texto (SMS), llamadas de teléfono, notificaciones de inserción de iOS y Android y la escalación de alertas hasta que la alerta se confirma o se cierra.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Creación de una dirección URL de integración de estado del servicio en OpsGenie
1.  Asegúrese de que se ha registrado y ha iniciado sesión en su cuenta de [OpsGenie](https://www.opsgenie.com/).

2.  Vaya a la sección **Integrations** (Integraciones) en OpsGenie.

    ![La sección "Integrations" (Integraciones) en OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Seleccione el botón de integración **Azure Service Health** (Estado del servicio de Azure).

    ![El botón "Azure Service Health" (Estado del servicio Azure) en OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Nombre** la alerta y especifique el campo **Asignado al equipo**.

5.  Rellene los demás campos como **Recipients** (Destinatarios), **Enabled** (Habilitado) y **Suppress Notifications** (Suprimir notificaciones).

6.  Copie y guarde la **dirección URL**, ya que debe contener la `apiKey` anexada al final.

    ![La "dirección URL de integración" en OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Seleccione **Save Integration** (Guardar integración).

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Creación de una alerta con OpsGenie en Azure Portal
### <a name="for-a-new-action-group"></a>Para un nuevo grupo de acciones:
1. Siga los pasos del 1 al 8 en [Creación de una alerta basada en una notificación de mantenimiento del servicio para un nuevo grupo de acciones con Azure Portal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Defina la lista de **acciones**:

    a. **Tipo de acción**: *webhook*

    b. **Detalles**: la **dirección URL de integración** de OpsGenie guardada anteriormente.

    c. **Nombre**: el identificador, alias o nombre de webhook.

3. Seleccione **Guardar** cuando termine para crear la alerta.

### <a name="for-an-existing-action-group"></a>Para un grupo de acciones existentes:
1. En [Azure Portal](https://portal.azure.com/), seleccione **Supervisión**.

2. En la sección **Configuración**, seleccione **Grupos de acciones**.

3. Busque el grupo de acciones que desee editar.

4. Defina la lista de **acciones**:

    a. **Tipo de acción**: *webhook*

    b. **Detalles**: la **dirección URL de integración** de OpsGenie guardada anteriormente.

    c. **Nombre**: el identificador, alias o nombre de webhook.

5. Cuando termine de actualizar el grupo de acciones, seleccione **Guardar**.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Prueba de la integración de webhook a través de una solicitud HTTP POST
1. Crear la carga de estado del servicio que desee enviar. Puede encontrar una carga de webhook de estado del servicio de ejemplo en [Webhooks para alertas del registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Cree una solicitud HTTP POST de la siguiente manera:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Debe recibir una respuesta `200 OK` con el mensaje de estado "correcto".

4. Vaya a [OpsGenie](https://www.opsgenie.com/) para confirmar que la integración se ha configurado correctamente.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información acerca de cómo [configurar notificaciones de webhook para los sistemas de administración de problemas existentes](service-health-alert-webhook-guide.md).
- Revise el [Esquema de webhook de alertas del registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Más información acerca de las [Notificaciones del estado del servicio](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Más información sobre los [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md).