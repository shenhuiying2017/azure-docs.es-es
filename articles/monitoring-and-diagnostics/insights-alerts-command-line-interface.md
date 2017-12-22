---
title: "Creación de alertas para servicios de Azure - CLI multiplataforma | Microsoft Docs"
description: "Desencadenamiento de correos electrónicos y notificaciones, y llamadas a direcciones URL de sitios web (webhooks) o a la automatización cuando se cumplen las condiciones especificadas."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 92246a8da73a244a1c9a924bed55711d71a20fd8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Creación de alertas de métricas en Azure Monitor para los servicios de Azure: CLI multiplataforma
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Información general
En este artículo se muestra cómo configurar alertas de métricas de Azure con la interfaz de la línea de comandos (CLI) multiplataforma.

> [!NOTE]
> Azure Monitor es el nuevo nombre de lo que se conocía como "Azure Insights" hasta el 25 de septiembre de 2016. Sin embargo, los espacios de nombres y, por tanto, los siguientes comandos aún contienen el término "insights".
>
>

Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

* **Valores de métrica** : la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.    
* **Eventos de registro de actividades**: una alerta puede desencadenarse con *cada* evento o solo cuando se producen ciertos eventos concretos. Para obtener más información sobre las alertas de registro de actividad, [haga clic aquí](monitoring-activity-log-alerts.md).

Puede configurar una alerta de métrica para hacer lo siguiente cuando se desencadena:

* Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
* Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook.
* Iniciar la ejecución de un runbook de Azure (en este momento, solo desde Azure Portal).

Puede obtener información sobre las reglas de alerta de métricas y configurarlas mediante:

* [Portal de Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaz de la línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Siempre puede escribir un comando y agregar -help al final para obtener ayuda sobre dicho comando. Por ejemplo:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Creación de reglas de alerta mediante la CLI
1. Cumpla los requisitos previos e inicie sesión en Azure. Consulte los [ejemplos de CLI de Azure Monitor](insights-cli-samples.md). En pocas palabras, instale la CLI y ejecute estos comandos. Con estos comandos, puede iniciar sesión, ver la suscripción que usa y prepararse para ejecutar comandos de Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Para ver las reglas existentes en un grupo de recursos, use el formato siguiente **azure insights alerts rule list** *[opciones] &lt;grupoRecursos&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Para crear una regla, primero debe contar con varios datos importantes.
  * El **identificador de recurso** del recursos para el que desea establecer una alerta.
  * Las **definiciones de métricas** disponibles para ese recurso.

     Una forma de obtener el identificador de recurso es usar Azure Portal. Seleccione el recurso en el portal, suponiendo que ya existe. En la sección *Configuración* de la hoja siguiente, seleccione *Propiedades*. El campo *RESOURCE ID* está en la hoja siguiente. Otra forma que puede usar es el [Explorador de recursos de Azure](https://resources.azure.com/).

     El siguiente es un ejemplo de identificador de recurso de una aplic. web:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Para obtener una lista de las métricas disponibles y las unidades de esas métricas para el ejemplo de recurso anterior, use el siguiente comando de CLI:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* es la granularidad de la medida disponible (intervalos de 1 minuto). El uso de distintas granularidades le brinda opciones de métricas diferentes.
4. Para crear una regla de alerta basada en métricas, use un comando con el siguiente formato:

    **azure insights alerts rule metric set***[opciones] &lt;nombreRegla&gt;&lt;ubicación&gt;&lt;grupoRecursos&gt;&lt;tamañoVentana&gt;&lt;operador&gt;&lt;umbral&gt;&lt;idRecursoObjetivo&gt;&lt;nombreMétrica&gt;&lt;horaInserciónOperador&gt;*

    En el ejemplo siguiente se configura una alerta en un recurso de sitio web. La alerta se desencadena cada vez que se recibe constantemente cualquier tráfico durante 5 minutos y nuevamente cuando no se recibe tráfico durante 5 minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Para crear un webhook o enviar un correo electrónico cuando se active una alerta de métrica, debe crear el correo electrónico o los webhooks. Cree la regla inmediatamente después. No puede asociar webhooks o correos electrónicos con reglas ya creadas mediante la CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Observe una regla individual para comprobar que las alertas se crearon correctamente.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Para eliminar reglas, use un comando con este formato:

    **insights alerts rule delete** [opciones] &lt;grupoRecursos&gt;&lt;nombreRegla&gt;

    Estos comandos eliminan las reglas que se crearon anteriormente en este artículo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga información general sobre la supervisión de Azure](monitoring-overview.md) , incluidos los tipos de información que puede recopilar y supervisar.
* Obtenga más información sobre cómo [configurar webhooks en las alertas](insights-webhooks-alerts.md).
* Obtenga más información sobre la [configuración de alertas sobre los eventos de registro de actividad](monitoring-activity-log-alerts.md).
* Obtenga más información sobre los [runbooks de Azure Automation](../automation/automation-starting-a-runbook.md).
* Obtenga [información general sobre la colección de registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recopilar métricas detalladas de alta frecuencia sobre el servicio.
* Obtenga [información general sobre la colección de métricas](insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
