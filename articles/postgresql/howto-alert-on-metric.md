---
title: "Configurar alertas de métricas para Azure Database for PostgreSQL en Azure Portal | Microsoft Docs"
description: "En este artículo se describe cómo configurar las alertas de métricas de Azure Database for PostgreSQL, y obtener acceso a ellas, mediante Azure Portal."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 3a09be8131b57381eb470027a134109c116467ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Usar Azure Portal para configurar alertas de métricas para Azure Database for PostgreSQL 

En este artículo se explica cómo configurar alertas de Azure Database for PostgreSQL mediante Azure Portal. Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure.

La alerta se desencadena cuando el valor de una métrica especificada cruza el umbral que asigna. La alerta se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición deja de cumplirse. 

Puede configurar una alerta para realizar las siguientes acciones cuando se desencadene:
* Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
* Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook.

Puede obtener información sobre las reglas de alerta y configurarlas mediante:
* [Portal de Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interfaz de la línea de comandos (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creación de una regla de alerta sobre una métrica desde Azure Portal
1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de Azure Database for PostgreSQL que quiera supervisar.

2. En la sección **Supervisión** de la barra lateral, seleccione **Reglas de alerta** como se muestra a continuación:

   ![Seleccionar Reglas de alerta](./media/howto-alert-on-metric/1-alert-rules.png)

3. Seleccione **Agregar alerta de métrica** (icono +). 

4. La página **Agregar regla** se abre como se muestra a continuación.  Rellene la información necesaria:

   ![Formulario de adición de alerta de métrica](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Configuración | Descripción  |
   |---------|---------|
   | Nombre | Especifique un nombre para la regla de alerta. Este valor se envía en el correo electrónico de notificación de alerta. |
   | Descripción | Proporcione una descripción breve de la regla de alerta. Este valor se envía en el correo electrónico de notificación de alerta. |
   | Alerta activada | Elija **Métricas** para este tipo de alerta. |
   | La suscripción | Este campo se rellena previamente con la suscripción que hospeda Azure Database for PostgreSQL. |
   | Grupos de recursos | Este campo se rellena previamente con el grupo de recursos de Azure Database for PostgreSQL. |
   | Recurso | Este campo se rellena previamente con el nombre de Azure Database for PostgreSQL. |
   | Métrica | Seleccione la métrica para la que desea emitir una alerta. Por ejemplo, **Porcentaje de almacenamiento**. |
   | Condición | Elija la condición para la métrica con la que va a realizar la comparación. Por ejemplo, **Mayor que**. |
   | Umbral | Valor de umbral de la métrica, como, por ejemplo, 85 (porcentaje). |
   | Período | Período de tiempo que debe cumplir la regla de métrica antes de que se desencadene la alerta. Por ejemplo, **En los últimos 30 minutos**. |

   Según el ejemplo, la alerta busca el porcentaje de almacenamiento por encima del 85 % durante un período de 30 minutos. Esa alerta se desencadena cuando el porcentaje de almacenamiento medio se mantiene por encima el 85 % durante 30 minutos. Una vez que se desencadena por primera vez, se vuelve a desencadenar cuando el promedio de porcentaje de almacenamiento está por debajo del 85 % durante más de 30 minutos.

5. Elija el método de notificación que desee para la regla de alerta. 

   Active la opción **Enviar correo electrónico a propietarios, colaboradores y lectores** si desea que se envíe un correo electrónico a los administradores y coadministradores de la suscripción cuando se active la alerta.

   Si quiere enviar una notificación a otras direcciones de correo electrónico cuando se active la alerta, agréguelas en el campo **Correos electrónicos adicionales del administrador**. Separe las direcciones de correo electrónico con punto y coma, de la siguiente manera: *email@contoso.com;email2@contoso.com*

   También puede indicar un identificador URI válido en el campo **Webhook** si quiere llamarlo cuando se active la alerta.

6. Seleccione **Aceptar** para crear la alerta.

   En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="manage-your-alerts"></a>Administración de alertas
Una vez que haya creado una alerta, puede seleccionarla y realizar las acciones siguientes:

* Ver un gráfico que muestre el umbral de las métricas y los valores reales del día anterior en relación con esta alerta.
* **Editar** o **eliminar** la regla de alerta.
* **Deshabilitar** o **habilitar** la alerta, si desea detener temporalmente o reanudar la recepción de notificaciones.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre cómo [configurar webhooks en las alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Obtenga [información general sobre la colección de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
