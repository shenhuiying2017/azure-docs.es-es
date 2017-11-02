---
title: "Automatización de procesos de Azure Application Insights con Microsoft Flow"
description: "Obtenga información acerca de cómo puede utilizar Microsoft Flow para automatizar rápidamente los procesos repetibles mediante el conector de Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: a1d2787626ed8fa71e3e4e9921ffb8a4680014cb
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatización de procesos de Azure Application Insights con el conector para Microsoft Flow

¿Se encuentra a menudo ejecutando las mismas consultas en los datos de telemetría para comprobar que el servicio está funcionando correctamente? ¿Desea automatizar estas consultas para encontrar tendencias y anomalías, y crear sus propios flujos de trabajo en función de ellas? El conector de Azure Application Insights (versión preliminar) para Microsoft Flow es la herramienta adecuada para estos fines.

Con esta integración, ahora se pueden automatizar numerosos procesos sin tener que escribir una sola línea de código. Después de crear un flujo mediante una acción de Application Insights, el flujo ejecuta automáticamente la consulta de Application Insights Analytics. 

También puede agregar acciones adicionales. Microsoft Flow pone a su disposición cientos de acciones. Por ejemplo, puede utilizar Microsoft Flow para enviar automáticamente una notificación por correo electrónico o para crear un error en Visual Studio Team Services. También puede utilizar una de las numerosas [plantillas](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibles para el conector para Microsoft Flow. Estas plantillas aceleran el proceso de creación de un flujo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Creación de un flujo para Application Insights

En este tutorial, obtendrá información sobre cómo crear un flujo que usa el algoritmo de clústeres automáticos de Analytics para agrupar los atributos en los datos para una aplicación web. El flujo envía automáticamente los resultados por correo electrónico, y esto es solo un ejemplo de cómo puede usar Microsoft Flow y Application Insights Analytics conjuntamente. 

### <a name="step-1-create-a-flow"></a>Paso 1: Creación de un almacén
1. Inicie sesión en [Microsoft Flow](http://flow.microsoft.com) y, a continuación, seleccione **Mis flujos**.
2. Haga clic en **Crear un flujo a partir de un documento en blanco**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Paso 2: Creación de un desencadenador para el flujo
1. Elija **Programación** y, a continuación, **Programación: Periodicidad**.
2. En el cuadro **Frecuencia**, seleccione **Día** y, en el cuadro **Intervalo**, escriba **1**.

    ![Cuadro de diálogo del desencadenador de Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Paso 3: Incorporación de una acción de Application Insights
1. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.
2. Busque **Azure Application Insights**.
3. Haga clic en **Azure Application Insights: Visualize Analytics query Preview** [Visualizar consulta de Analytics (Versión preliminar)].

    ![Ventana Ejecutar consulta de Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Paso 4: Conexión a un recurso de Application Insights

Para completar este paso, necesita un identificador de aplicación y una clave de API para el recurso. Puede recuperarlos desde Azure Portal, como se muestra en el diagrama siguiente:

![Identificador de aplicación en Azure Portal](./media/app-insights-automate-with-flow/appid.png) 

- Proporcione un nombre para la conexión, junto con el identificador de aplicación y la clave de API.

    ![Ventana de conexión de Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Paso 5: Especificación del tipo de gráfico y consulta de análisis
Esta consulta de ejemplo selecciona las solicitudes con error del último día y las pone en correlación con las excepciones que se han producido como parte de la operación. Analytics las correlaciona en función del identificador operation_Id. La consulta, a continuación, segmenta los resultados mediante el algoritmo de clúster automático. 

Al crear sus propias consultas, asegúrese de comprobar que funcionan correctamente en Analytics antes de agregarlas al flujo.

- Agregue la siguiente consulta de Analytics y seleccione el tipo de gráfico de tabla HTML. 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Pantalla de configuración de consulta de Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Paso 6: Configuración del flujo para enviar correo electrónico

1. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.
2. Busque **Office 365 Outlook**.
3. Haga clic en **Office 365 Outlook: Enviar un correo electrónico**.

    ![Ventana de selección de Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. En la ventana **Enviar un correo electrónico**, haga lo siguiente:

   a. Escriba la dirección de correo electrónico del destinatario.

   b. Escriba un asunto para el correo electrónico.

   c. Haga clic en cualquier parte del cuadro **Cuerpo** y, en el menú de contenido dinámico que se abre a la derecha, seleccione **Cuerpo**.

   d. Haga clic en **Mostrar opciones avanzadas**.

    ![Configuración de Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. En el menú de contenido dinámico, haga lo siguiente:

    a. Seleccione **Nombre de datos adjuntos**.

    b. Seleccione **Contenido de datos adjuntos**.
    
    c. En el campo **Es HTML**, seleccione **Sí**.

    ![Pantalla de configuración de correo electrónico de Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Paso 7: Guardado y prueba del flujo
- En el cuadro **Nombre de flujo**, agregue un nombre para el flujo y, a continuación, haga clic en **Crear flujo**.

    ![Ventana de creación de flujo](./media/app-insights-automate-with-flow/flow8.png)

Puede esperar a que el desencadenador lleve a cabo esta acción o ejecutar el flujo inmediatamente [ejecutando el desencadenador a petición](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Cuando se ejecuta el flujo, los destinatarios que haya especificado en la lista de correo electrónico recibirán un mensaje de correo electrónico similar al siguiente:

![Correo electrónico de ejemplo](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre cómo crear [consultas de análisis](app-insights-analytics-using.md).
- Más información sobre [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





