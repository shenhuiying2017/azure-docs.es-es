---
title: "Creación y administración de grupos de acciones en Azure Portal | Microsoft Docs"
description: "Obtenga información acerca de cómo crear y administrar grupos de acciones en Azure Portal."
author: dkamstra
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: dukek
ms.openlocfilehash: 772b9c2b9532bd2cc37ad89db92545297eecd903
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creación y administración de grupos de acciones en Azure Portal
## <a name="overview"></a>Información general ##
En este artículo se muestra cómo crear y administrar grupos de acciones en el portal de Azure.

Los grupos de acciones le permiten configurar una lista de acciones. Estos grupos pueden ser utilizados por cada alerta que defina, asegurándose de que las mismas acciones se realizan cada vez que se desencadena la alerta.

Un grupo de acciones puede tener hasta 10 acciones de cada tipo. Cada acción se compone de las siguientes propiedades:

* **Nombre**: un identificador único dentro del grupo de acciones.  
* **Tipo de acción**: enviar un SMS, enviar un correo electrónico, llamar a un webhook, enviar datos a una herramienta ITSM, llamar a una aplicación de Azure o ejecutar un runbook de Automation.
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

    b. **Tipo de acción**: seleccionar correo electrónico, SMS, aplicación de Azure, webhook, ITSM o runbook de Automation.

    c. **Detalles**: según el tipo de acción, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook, una aplicación de Azure, una conexión de ITSM o un runbook de Automation. Para la acción de ITSM, especifique además **Elemento de trabajo** y otros campos que requiera la herramienta ITSM.

   > [!NOTE]
   > La acción de ITSM requiere una conexión de ITSM. Aprenda cómo crear una [conexión de ITSM](../log-analytics/log-analytics-itsmc-overview.md). 

8. Seleccione **Aceptar** para crear el grupo de acciones.

## <a name="manage-your-action-groups"></a>Administración de los grupos de acciones ##
Después de crear un grupo de acciones, está visible en la sección **Grupos de acciones** de la hoja **Supervisión**. Seleccione el grupo de acciones que desea administrar para:

* Agregar, editar o quitar acciones.
* Eliminar el grupo de acciones.

## <a name="next-steps"></a>pasos siguientes ##
* Más información sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).  
* [Comprender el esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).  
* Obtenga más información sobre [Conector de ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Más información sobre la [limitación de velocidad](monitoring-alerts-rate-limiting.md) en las alertas.
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
