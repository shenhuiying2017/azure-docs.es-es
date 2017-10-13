---
title: "Ejemplo de acción de alerta de webhook en Log Analytics de OMS | Microsoft Docs"
description: "Una de las acciones que puede ejecutar en respuesta a una alerta de Log Analytics es un *webhook*, que permite invocar un proceso externo a través de una sola solicitud HTTP. En este artículo, le guiaremos por un ejemplo en el que se creará una acción de webhook en una alerta de Log Analytics con Slack."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Creación de una acción de alerta de webhook en Log Analytics de OMS para enviar mensajes a Slack
Una de las acciones que puede ejecutar en respuesta a una [alerta de Log Analytics](log-analytics-alerts.md) es un *webhook*, que permite invocar un proceso externo a través de una sola solicitud HTTP.  En [Alerts in Log Analytics](log-analytics-alerts.md)

En este artículo, le guiaremos por un ejemplo en el que se creará una acción de webhook en una alerta de Log Analytics con Slack, que es un servicio de mensajería.

> [!NOTE]
> Para realizarlo, es necesario disponer de una cuenta de Slack.  Puede registrarse para obtener una cuenta gratuita en [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Paso 1: habilitar webhooks en Slack
1. Inicie sesión en Slack en [slack.com](http://slack.com).
2. Seleccione un canal en la sección **Channels** (Canales) en el panel de la izquierda.  Se trata del canal al que se enviará el mensaje.  Puede seleccionar uno de los canales predeterminados, como **general** o **random** (aleatorio).  En un escenario de producción, probablemente crearía un canal especial, como **criticalservicealerts**. <br>
   
   ![Canales de Slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Haga clic en **Add an app or custom integration** (Agregar una aplicación o integración personalizada) para abrir el directorio de aplicaciones.
4. Escriba *webhooks* en el cuadro de búsqueda y, después, seleccione **Incoming WebHooks**(Webhooks entrantes). <br>
   
   ![Canales de Slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Haga clic en **Install** (Instalar) junto al nombre del equipo.
6. Haga clic en **Add Configuration**(Agregar configuración).
7. Seleccione el canal que va a usar en este ejemplo y haga clic en **Add Incoming WebHooks integration**(Agregar integración de webhooks entrantes).  
8. Copie el valor del campo **Webhook URL**(Dirección URL del webhook).  Esto será lo que pegue en la configuración de la alerta. <br>
   
    ![Canales de Slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Paso 2: crear una regla de alerta en Log Analytics
1. [Cree una regla de alerta](log-analytics-alerts.md) con la siguiente configuración.
   * Consulta: ```    Type=Event EventLevelName=error ```
   * Buscar esta alerta cada: 5 minutos
   * El número de resultados es: mayor que 10
   * Durante este periodo de tiempo: 60 minutos
   * Seleccione **Yes** (Sí) para **Webhook** y **No** para el resto de acciones.
2. Pegue la dirección URL de Slack en el campo **Webhook URL** (Dirección URL del webhook).
3. Seleccione la opción para **incluir una carga JSON personalizada**.
4. Slack espera una carga con formato JSON y con un parámetro denominado *text*,  que es el texto que se mostrará en el mensaje que crea.  Puede usar uno o varios parámetros de alerta mediante el símbolo *#*, como se muestra en el siguiente ejemplo.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![carga JSON de ejemplo](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Haga clic en **Guardar** para guardar la regla de alerta.
6. Espere el tiempo que haga falta para que la alerta se cree y, después, busque un mensaje en Slack similar al siguiente.
   
   ![webhook de ejemplo en Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Carga de webhook avanzada para Slack
Los mensajes entrantes se pueden personalizar completamente con Slack. Para obtener más información, consulte [Incoming Webhooks](https://api.slack.com/incoming-webhooks) (Webhooks entrantes) en el sitio web de Slack. La siguiente es una carga más compleja para crear un mensaje completo con formato:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Esto generaría un mensaje en Slack similar al siguiente.

![mensaje de ejemplo en Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Resumen
Con esta regla de alerta en vigor, se enviaría un mensaje a Slack cada vez que se cumplan los criterios.  

Esto es solo un ejemplo de una acción que puede crear en respuesta a una alerta.  Podría crear una acción de webhook que llame a otro servicio externo, una acción de Runbook que inicie un Runbook en Automatización de Azure o una acción de correo electrónico que envíe un correo electrónico a usted y a otros destinatarios.   

## <a name="next-steps"></a>Pasos siguientes
* Conozca otras [acciones de alerta en Log Analytics](log-analytics-alerts-actions.md), incluidas otras acciones.


